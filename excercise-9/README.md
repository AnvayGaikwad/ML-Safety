# Machine Learning Safety Exercise 9
## Uncertainty Quantification, Calibration and Cost-Optimal Decisions

---

## Overview

This exercise focuses on uncertainty quantification and probability calibration for the three CARLA perception models developed in previous assignments:

- Pedestrian Classifier
- Vehicle Classifier
- Traffic-Light Classifier

The implementation evaluates how reliable the predicted confidence scores are, applies temperature scaling to improve calibration, and analyzes how calibrated probabilities influence downstream safety-critical decisions using a cost-sensitive decision threshold.

---

# Objectives

This assignment implements the following practical tasks:

- Compute Expected Calibration Error (ECE) for all three trained models.
- Generate Reliability Diagrams (Confidence vs Accuracy).
- Determine whether each model is overconfident or underconfident.
- Apply Temperature Scaling using validation-set Negative Log Likelihood (NLL).
- Find the optimal temperature (T) for each model.
- Compare calibration before and after scaling.
- Evaluate cost-sensitive pedestrian decisions using:
  - Standard threshold (τ = 0.5)
  - Cost-optimal threshold (τ* ≈ 0.0099)
- Compute total decision loss:
  - False Negatives
  - False Positives
  - Overall Cost
- Extend the STPA safety analysis to include overconfident misclassification.

---

# Models Used

The following trained ResNet-18 binary classifiers were evaluated:

| Model | Target Label |
|--------|--------------|
| Pedestrian | has_pedestrian |
| Vehicle | has_vehicle |
| Traffic Light | has_traffic_light |

---

# Exercise 9.2 Calibration and Expected Calibration Error (ECE)

## Objective

Understand the concept of probability calibration and evaluate whether a classifier's predicted confidence reflects its true prediction accuracy.

### Key Concepts

A classifier is **well-calibrated** when its predicted confidence matches the actual probability of being correct. For example, predictions made with 80% confidence should be correct approximately 80% of the time.

Expected Calibration Error (ECE) is used to measure calibration quality by comparing the model's average confidence with its observed accuracy across multiple confidence bins.

### Observations

- A smaller ECE indicates better calibration.
- An ECE of 0 represents perfect calibration.
- Large ECE values indicate that the model is either overconfident or underconfident.
- Calibration is particularly important in autonomous driving because downstream planning modules rely on confidence values to make safety-critical decisions.

---

# Exercise 9.3 Cost-Optimal Downstream Decisions

## Objective

Derive a decision threshold that minimizes the expected safety cost for the pedestrian classifier by considering asymmetric costs for false negatives and false positives.

### Cost Function

The assignment uses:

- False Negative Cost (CFN) = 100
- False Positive Cost (CFP) = 1

The expected losses are:

**Brake**

E[L | Brake] = (1 − p) × CFP

**Continue Driving**

E[L | Continue] = p × CFN

Equating both expected losses gives the optimal threshold:

τ* = 1 / 101 ≈ 0.0099

### Interpretation

| Threshold | Decision Rule |
|------------|---------------|
| τ = 0.5 | Brake only for high-confidence pedestrian detections. |
| τ* ≈ 0.0099 | Brake whenever pedestrian probability exceeds approximately 1%. |

### Observations

- The optimal threshold is much lower than the standard threshold because missing a pedestrian is significantly more expensive than unnecessary braking.
- Using τ* makes the autonomous vehicle more conservative by prioritizing safety over efficiency.
- This threshold is only optimal when the classifier produces well-calibrated probabilities. If the model is overconfident or underconfident, the estimated probabilities no longer reflect the true likelihood of a pedestrian, and the resulting decision may not minimize the actual expected cost.
# Exercise 9.4 Measuring Calibration

## Implemented

- Loaded each trained model.
- Evaluated on the in-distribution CARLA test set.
- Computed prediction probabilities using the sigmoid function.
- Calculated Expected Calibration Error (ECE).
- Generated Reliability Diagrams for all three classifiers.

---

## Expected Calibration Error (ECE)

| Model | ECE |
|--------|------:|
| Pedestrian | 0.1670 |
| Vehicle | 0.1152 |
| Traffic Light | 0.0482 |

### Observations

- The **Pedestrian classifier** has the highest calibration error and is the most overconfident.
- The **Vehicle classifier** is also overconfident but performs better than the pedestrian model.
- The **Traffic-Light classifier** has the lowest ECE and is therefore the best-calibrated model.

---

## Generated Images

- pedestrian_reliability.png
- vehicle_reliability.png
- traffic_light_reliability.png

---

# Exercise 9.5 – Temperature Scaling

## Implemented

Temperature scaling was applied to each trained model.

Implementation steps:

1. Collected logits from the validation dataset.
2. Performed a grid search over:

T = {0.5, 0.6, 0.7, ..., 3.0}

3. Computed Validation Negative Log Likelihood (NLL) for every temperature.
4. Selected the temperature with the lowest validation NLL.
5. Applied the selected temperature to the test logits.
6. Recomputed Expected Calibration Error.

---

## Optimal Temperature Values

| Model | Best Temperature (T) |
|--------|---------------------:|
| Pedestrian | 2.6 |
| Vehicle | 2.4 |
| Traffic Light | 1.7 |

---

## ECE Before vs After Temperature Scaling

| Model | ECE Before | ECE After |
|--------|-----------:|----------:|
| Pedestrian | 0.1670 | 0.2048 |
| Vehicle | 0.1152 | 0.0912 |
| Traffic Light | 0.0482 | 0.0415 |

---

## Observations

### Pedestrian

- Validation NLL decreases until approximately **T = 2.6**.
- Although validation NLL improved, the ECE increased on the test set.
- Temperature scaling therefore did **not** improve calibration for this model.

### Vehicle

- Best temperature is approximately **2.4**.
- Temperature scaling reduced the ECE.
- Confidence estimates became better aligned with observed accuracy.

### Traffic-Light

- Best temperature is approximately **1.7**.
- The model was already well calibrated.
- Temperature scaling produced only a small improvement.

---

## Generated Images

- pedestrian_temperature_curve.png
- vehicle_temperature_curve.png
- traffic_light_temperature_curve.png

---

# Exercise 9.6 – Cost-Optimal Decision in Practice

The pedestrian classifier was evaluated using:

- Standard threshold

τ = 0.5

- Cost-optimal threshold

τ* ≈ 0.0099

using

CFN = 100

CFP = 1

The total loss was computed as

L = CFN × FN + CFP × FP

---

## Detailed Results

| Model | Threshold | False Negatives | False Positives | Total Loss |
|--------|-----------|---------------:|---------------:|-----------:|
| Uncalibrated | τ = 0.5 | 346 | 675 | 35275 |
| Uncalibrated | τ* = 0.0099 | 13 | 2615 | 3915 |
| Temperature Scaled | τ = 0.5 | 346 | 675 | 35275 |
| Temperature Scaled | τ* = 0.0099 | 0 | 2894 | 2894 |

---

## Required 2 × 2 Comparison Table

| | τ = 0.5 | τ* = 0.0099 |
|--------------------|---------:|-----------:|
| Uncalibrated | 35275 | 3915 |
| Temperature Scaled | 35275 | 2894 |

---

## Observations

- Using the standard threshold (0.5) results in a very large number of false negatives.
- Because each false negative costs 100 times more than a false positive, the total loss is extremely high.
- Lowering the threshold to τ* dramatically reduces false negatives.
- Temperature scaling combined with τ* completely eliminates false negatives.
- Although more false positives are produced, the overall cost becomes the lowest because false negatives dominate the loss function.

**Lowest Total Loss**

| Configuration | Total Loss |
|--------------|-----------:|
| Temperature Scaled + τ* | **2894** |

---

# Exercise 9.7 Safety Analysis

The STPA analysis was extended by introducing overconfident false negatives as a causal scenario.

New additions include:

- Overconfident pedestrian false negative causing failure to brake.
- Model-level calibration safety constraint using Expected Calibration Error.
- System-level safety constraint requiring:
  - Cost-optimal threshold τ*
  - Safe fallback
  - Reduced reliance on raw confidence values.
- Verification using calibration results from Exercises 9.4 and 9.5.
- Residual risk discussion showing that calibration alone cannot eliminate unsafe behavior.

---

# Files Generated

## CSV Files

- exercise9_ece.csv
- exercise9_temperature_scaling.csv
- exercise9_cost_optimal_table.csv
- exercise9_cost_optimal_details.csv

---

## Figures

### Reliability Diagrams

- pedestrian_reliability.png
- vehicle_reliability.png
- traffic_light_reliability.png

### Temperature Scaling Curves

- pedestrian_temperature_curve.png
- vehicle_temperature_curve.png
- traffic_light_temperature_curve.png

---
