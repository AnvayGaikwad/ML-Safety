# Exercise 4 – Model Testing and Validation

## Overview

In this exercise, the focus shifts from training models to testing and validating them from a safety perspective. The exercise covers differences between traditional software testing and ML testing, distribution shift analysis, ODD coverage measurement using k-projection coverage, and evaluation of the trained CARLA perception models using confusion matrices.

The previously trained pedestrian, vehicle, and traffic light detectors from Exercise 3 were evaluated on the provided test set. In addition, ODD coverage was analyzed to determine how well the test scenarios represent the operational conditions defined for the system.

---

# Exercise 4.1 – Traditional Testing vs. ML Model Testing

### Difference 1: Logic Written vs. Logic Learned

Traditional software systems are based on logic explicitly written by the programmer. Testing verifies whether the implemented logic behaves as expected.

Machine learning systems learn patterns and behaviour from data. Testing focuses on determining whether the model has learned useful and reliable behaviour for the intended task.

### Difference 2: Exact Outputs vs. Probabilistic Outputs

Traditional software typically produces deterministic outputs with clear pass/fail criteria.

Machine learning models generate probabilistic predictions and may produce different outputs for slightly different inputs.

### Difference 3: Individual Tests vs. Aggregate Metrics

Traditional testing evaluates individual test cases and expects all tests to pass.

ML testing relies on aggregate performance metrics such as precision, recall, and F1-score because some prediction errors are expected.

### Difference 4: Code Coverage vs. Dataset Representativeness

Traditional testing focuses on code coverage and execution paths.

ML testing focuses on dataset representativeness and whether relevant operating conditions are sufficiently covered.

### Difference 5: Traditional Bugs vs. Distribution Shift

Traditional software failures are often caused by implementation bugs.

ML models can fail even when the code is correct because deployment conditions differ from training conditions, resulting in distribution shift.

### Difference 6: Test Oracle Complexity

In traditional software, expected outputs are usually known exactly.

In perception systems, defining a correct output can be difficult because different interpretations may be possible and labels may contain uncertainty.

---

# Exercise 4.4 – Distribution Shift Types

## Scenario 1

### a) Distribution Shift Type

Covariate Shift

The input distribution changes because winter conditions introduce wet roads and glare, while the underlying labels remain unchanged.

### b) Expected Effect

The model may miss pedestrians, vehicles, or traffic lights more frequently, resulting in reduced recall and overall performance degradation.

### c) Mitigation Strategy

Collect additional training data containing winter, rain, and glare conditions or detect when the vehicle is operating outside its trained ODD.

---

## Scenario 2

### a) Distribution Shift Type

Label Shift

The frequency of cyclists changes from less than 5% in the training data to approximately 60% in the deployment environment.

### b) Expected Effect

The model may perform poorly on cyclists because they were underrepresented during training. This can result in increased false negatives and lower recall.

### c) Mitigation Strategy

Collect more cyclist examples and rebalance the training dataset.

---

## Scenario 3

### a) Distribution Shift Type

Concept Shift

The appearance of traffic lights changes significantly, altering the relationship between visual features and labels.

### b) Expected Effect

Traffic lights may not be detected correctly, potentially leading to unsafe decisions and traffic rule violations.

### c) Mitigation Strategy

Retrain or fine-tune the model using examples of the new traffic light designs.

---

# Exercise 4.5 – ODD Coverage with k-Projection Coverage

## 4.5.1 What does k-projection coverage measure and why is it useful?

k-projection coverage measures how well a test set covers combinations of conditions within an Operational Design Domain (ODD). Instead of requiring every possible combination to be tested, it evaluates whether k-way combinations of ODD dimensions are represented.

For example:

* k = 1 checks individual dimensions.
* k = 2 checks pairwise combinations.
* k = 3 checks combinations of three dimensions.

This metric is useful because testing every possible ODD combination quickly becomes infeasible due to combinatorial explosion. k-projection coverage provides a practical balance between completeness and feasibility while helping identify gaps in the test set.

## 4.5.2 Computed k-Projection Coverage

| k | Coverage | Covered | Total |
| - | -------- | ------- | ----- |
| 1 | 0.4167   | 5       | 12    |
| 2 | 0.1754   | 10      | 57    |
| 3 | 0.0746   | 10      | 134   |

All calculations were performed using 3,600 test scenarios.

## 4.5.3 How does coverage change with increasing k?

Coverage decreases significantly as k increases:

* k = 1: 41.67%
* k = 2: 17.54%
* k = 3: 7.46%

This behaviour is expected because the number of possible combinations increases rapidly as more ODD dimensions are considered simultaneously.

The results indicate that while some individual conditions are represented in the test set, coverage of more complex combinations is limited. This suggests that the test set does not fully represent the complete ODD and additional scenarios would be required to improve coverage.

---

# Exercise 4.7 – Per-Class Evaluation

## 4.7.1 Confusion Matrix Analysis

### Pedestrian Detector

| Actual / Predicted | Negative | Positive |
| ------------------ | -------- | -------- |
| Negative           | 2219     | 675      |
| Positive           | 346      | 360      |

The pedestrian detector produces a relatively large number of false positives and false negatives, indicating that pedestrian detection remains challenging.

### Vehicle Detector

| Actual / Predicted | Negative | Positive |
| ------------------ | -------- | -------- |
| Negative           | 344      | 556      |
| Positive           | 94       | 2606     |

The vehicle detector achieves strong detection performance with a high number of correctly identified vehicles and relatively few missed detections.

### Traffic Light Detector

| Actual / Predicted | Negative | Positive |
| ------------------ | -------- | -------- |
| Negative           | 839      | 177      |
| Positive           | 41       | 2543     |

The traffic light detector performs best, producing very few false negatives and a large number of correct detections.

---

## 4.7.2 Which model has the lowest recall?

The pedestrian detector has the lowest recall.

This result is consistent with expectations from the hazard analysis and Exercise 3 evaluation. Pedestrians are smaller, more visually diverse, and significantly less represented in the training dataset than vehicles and traffic lights.

As a result, the pedestrian detector struggles more with missed detections than the other two models.

---

## 4.7.3 What minimum recall would be required before deployment?

For the pedestrian detector, a recall of at least 95% would be desirable before considering deployment in a safety-critical autonomous driving system.

The primary safety concern is false negatives, where a pedestrian is present but not detected. Missing a pedestrian could directly lead to a collision and severe injury.

A high recall threshold therefore reduces the probability of missed detections and provides a stronger safety argument for deployment. While 100% recall is unrealistic in practice, a target of approximately 95% or higher would generally be expected for a safety-critical perception system.
