# Additional Safety Implementation

This folder contains the additional safety experiments implemented to extend the mandatory ML Safety exercises. The experiments build on the existing CARLA perception models and test set without introducing external datasets.

The additional implementation follows a single safety-oriented flow:
1) Input Corruption Robustness (ICR) – determine how perception performance changes when camera inputs are degraded.
2) Safety Envelope Evaluation – determine whether decision-threshold changes can keep performance within an acceptable safety boundary.
3) Runtime Safety Monitor – introduce a prototype runtime mechanism that flags potentially unsafe perception conditions and triggers a simulated fallback.

# 1. Input Corruption Robustness (ICR)

# Objective:
Evaluate the robustness of the three trained CARLA binary classifiers to realistic visual input degradation using the existing 3,600-image test set.

Four synthetic input corruptions were applied at three severity levels:
-Gaussian blur
-Brightness degradation
-Contrast degradation
-Gaussian noise

Recall was treated as the primary safety metric because false negatives correspond to missed objects.

# Main observations:

The models showed substantially different sensitivity to the corruption types.

Pedestrian detector:
Clean recall: approximately 0.51
Severe corruption results:
Blur S3: recall ≈ 0.031
Brightness S3: recall ≈ 0.008
Contrast S3: recall ≈ 0.052
Noise S3: recall ≈ 0.613

The pedestrian detector was particularly sensitive to blur, brightness and contrast.

Vehicle detector:

Clean recall: approximately 0.965
Severe corruption results:
Blur S3: recall ≈ 0.033
Brightness S3: recall ≈ 0.015
Contrast S3: recall ≈ 0.124
Noise S3: recall ≈ 1.000

The vehicle detector performed strongly on clean data but degraded severely under blur and brightness.

Traffic-light detector:

Clean recall: approximately 0.984
Severe corruption results:
Blur S3: recall ≈ 0.032
Brightness S3: recall = 0.000
Contrast S3: recall ≈ 0.294
Noise S3: recall ≈ 0.974

The strongest failure was observed under severe brightness degradation.

# 2. Safety Envelope Evaluation

# Objective

The Safety Envelope experiment investigated whether changing the classifier decision threshold could keep recall within an acceptable degradation boundary under the corrupted inputs identified by ICR. A 10% maximum recall drop relative to the clean baseline at the same threshold was used as the analytical safety-envelope criterion.

Decision thresholds from 0.01 to 0.90 were evaluated.

The analysis measured:
Recall
Precision
F1-score
False positives
False negatives
Recall drop
Safety-envelope coverage

A separate confidence analysis investigated whether confidence thresholds could detect false-negative failures.

# Main Observations:
- The best coverage was obtained at threshold 0.01
- Precision trade-off

For the pedestrian model on clean data:
Threshold 0.01 → recall ≈ 0.982, precision ≈ 0.210
Threshold 0.50 → recall ≈ 0.510, precision ≈ 0.348

The low threshold therefore improves sensitivity at the cost of substantially more false positives. 
At the standard threshold of 0.5, severe corruption caused major recall losses.

The confidence analysis showed that many false negatives remained highly confident. A particularly important case was Traffic Light + Brightness S3, where approximately 2,584 false negatives occurred. At confidence thresholds up to 0.98, none of these false negatives were detected by the confidence-only trigger.

# 3. Runtime Safety Monitor

# Objective

The Runtime Safety Monitor was implemented as a prototype system-level mitigation following the ICR and Safety Envelope findings.
The monitor evaluates:
Model confidence
Input brightness
Input sharpness

A frame triggers a simulated FALLBACK when either:
- confidence < 0.60, or
- the image-quality indicators fall below the normal range established from the clean test set.

The monitor was demonstrated using the pedestrian detector because pedestrian detection was the weakest and most safety-critical perception task.
The average fallback activation rate across corrupted conditions was approximately 77.84%.

# Main observations

1) The monitor responded most strongly to blur, brightness and contrast, which were also the corruptions producing the largest perception degradation in ICR.

Blur: 93.97% → 100% → 100%
Brightness: 92.50% → 100% → 100%
Contrast: 72.58% → 100% → 100%

This indicates that the monitor's response increases with the severity of the main failure modes identified earlier.

2) Lower response to noise

Noise produced lower fallback rates:

Noise S1: 15.39%
Noise S2: 23.56%
Noise S3: 36.03%

This is consistent with the ICR results, where noise caused comparatively limited and non-monotonic recall degradation.

Clean-input limitation:

The monitor also triggered fallback on 702 of 3,600 clean images, corresponding to a 19.50% clean fallback rate.
This indicates that the current monitor is conservative and produces unnecessary fallback activations. The thresholds would require further calibration before any deployment-oriented use.