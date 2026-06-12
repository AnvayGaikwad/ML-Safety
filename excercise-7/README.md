# Exercise 7 – Anomaly Detection and Out-of-Distribution (OOD) Detection

## Overview

The goal of this exercise was to investigate how perception models behave when they encounter inputs that differ from the conditions seen during training. The pedestrian, vehicle, and traffic-light classifiers were originally trained on sunny daytime images and were evaluated on fog, night, and different-town scenarios.

The exercise focused on understanding distribution shift, measuring model confidence under OOD conditions, evaluating confidence-based and feature-based OOD detection methods, and extending the safety analysis to account for undetected OOD inputs.

---

## Objectives

The main objectives were:

* Visualize the differences between in-distribution and OOD data.
* Analyze model confidence under fog and night conditions.
* Determine whether images from a different CARLA town should be considered inside or outside the Operational Design Domain (ODD).
* Evaluate the Maximum Softmax Probability (MSP) baseline for OOD detection.
* Implement a feature-based OOD detector using k-Nearest Neighbors (k-NN).
* Extend the system safety analysis to address OOD-related hazards.

---

## Results Summary

### Distribution Shift Analysis

Fog images reduced visibility and contrast, making objects harder to recognize. Night images introduced low illumination, shadows, reflections, and artificial lighting. These conditions represented clear OOD scenarios relative to the sunny daytime training distribution.

Images from the different CARLA town maintained similar weather and lighting conditions but contained different road layouts, buildings, vegetation, and scene structures. This represented a structural distribution shift rather than an appearance-based shift.

---

### Confidence Analysis

Model confidence was measured using:

Confidence = |0.5 − Predicted Probability|

A larger value indicates a more confident prediction, while values close to zero indicate uncertainty.

| Model         | Sunny  | Fog    | Night  |
| ------------- | ------ | ------ | ------ |
| Pedestrian    | 0.3404 | 0.4509 | 0.4363 |
| Vehicle       | 0.4214 | 0.3322 | 0.3328 |
| Traffic Light | 0.4865 | 0.3819 | 0.4300 |

Key observations:

* The pedestrian model remained highly confident under OOD conditions.
* The vehicle model became less confident in fog and night conditions.
* The traffic-light model showed reduced confidence under OOD conditions.
* Fog produced the strongest confidence reduction for the traffic-light detector.

---

### MSP Baseline OOD Detection

The Maximum Softmax Probability (MSP) baseline was used to separate in-distribution and OOD inputs.

#### AUROC Result

MSP AUROC = **0.3056**

This performance is worse than random guessing (0.5), indicating that confidence alone was not sufficient to distinguish OOD inputs from normal inputs.

#### Interpretation of the MSP Distribution

The MSP distribution plot showed strong overlap between in-distribution and OOD samples.

Although fog and night images were outside the training distribution, many still received extremely high confidence scores. As a result:

* OOD samples often appeared indistinguishable from normal samples.
* The model remained overconfident on unseen conditions.
* MSP failed to provide reliable OOD detection.

The histogram demonstrates that prediction confidence alone is not a trustworthy indicator of whether an image belongs to the training distribution.

---

### Feature-Based OOD Detection (k-NN)

A feature-based OOD detector was implemented using k-Nearest Neighbors (k-NN).

Instead of relying on prediction confidence, the detector used deep feature representations extracted from the neural network. OOD images were expected to lie farther away from the training feature distribution.

#### AUROC Results

| Detector              | AUROC  |
| --------------------- | ------ |
| MSP Baseline          | 0.3056 |
| k-NN Feature Detector | 0.9124 |

Night Scenario AUROC = **0.9729**

#### Interpretation of the Distance Distribution

The k-NN distance histogram showed a much clearer separation between in-distribution and OOD samples.

* In-distribution images clustered at small distances.
* Fog images produced moderately larger distances.
* Night images produced substantially larger distances.

This indicates that the deep feature representations captured the distribution shift effectively, even when the classifier remained highly confident.

The largest improvement over MSP occurred for the night scenario, where the feature detector achieved near-perfect separation.

---

## ODD and Safety Analysis

The original Operational Design Domain did not explicitly specify whether different CARLA towns were included. The ODD was revised to include all urban CARLA towns that satisfy the specified weather, lighting, camera, and road conditions.

Under this revised definition:

* Different-town images are considered inside the ODD.
* The perception system is expected to handle them correctly.
* OOD monitors should not flag them solely because they originate from a different town.

The safety analysis was extended to include hazards caused by undetected OOD inputs. New hazards, unsafe control actions, and safety constraints were introduced to ensure that the system can respond safely when perception reliability becomes uncertain.

