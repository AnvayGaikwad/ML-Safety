# Exercise 8: Adversarial Robustness and Safety Analysis
## Overview
The goal of this exercise was to investigate the robustness of CARLA perception models against adversarial attacks. Three binary classifiers trained in previous exercises were evaluated:

- Pedestrian Detector
- Vehicle Detector
- Traffic Light Detector

The exercise focused on:

- Understanding adversarial machine learning concepts
- Implementing the Fast Gradient Sign Method (FGSM)
- Generating adversarial examples with different perturbation strengths
- Measuring robustness through recall degradation
- Extending the STPA safety analysis to include adversarial threats

---

# Exercise 8.2: Attack Formulation

## Untargeted Attack
Goal: Cause any incorrect prediction.
Formula:

x(i+1) = x(i) + α ∇x L(y, f(x(i)))

## Targeted Attack
Goal: Force the model to predict a specific target class.
Formula:

x(i+1) = x(i) − α ∇x L(t, f(x(i)))

## Perturbation Budget
To ensure that the adversarial image remains within a bounded distance from the original image, the adversarial example is projected back into the ε-ball around the original input after each update.

---

# Exercise 8.3 Adversarial Training
Adversarial training improves robustness by training the model on both clean and adversarial examples.

## Advantages

- Improves robustness against adversarial attacks
- Reduces vulnerability to perturbations

## Trade-Offs

- Higher computational cost
- Longer training time
- Possible reduction in clean-data accuracy

---

# Exercise 8.4 Fast Gradient Sign Method (FGSM)
## Objective
Generate adversarial examples for all three CARLA perception models using:

ε ∈ {0.01, 0.05, 0.10}

The FGSM attack follows:

x_adv = x + ε · sign(∇x L(y, f(x)))

where:

- x = original image
- x_adv = adversarial image
- ε = perturbation strength
- L = loss function
- sign(∇xL) = direction that maximally increases the loss

---

## FGSM Results

| Model | Clean | ε=0.01 | ε=0.05 | ε=0.10 |
|---------|---------|---------|---------|---------|
| Pedestrian | 0.587 | 0.004 | 0.380 | 0.723 |
| Vehicle | 1.000 | 0.987 | 1.000 | 0.998 |
| Traffic Light | 1.000 | 0.060 | 0.000 | 0.019 |

---

## Observations

### Pedestrian Detector

- Clean confidence: 0.587
- Confidence collapses to 0.004 at ε = 0.01
- Small perturbations successfully fool the classifier
- Larger perturbations produce unstable behavior and confidence recovery

### Vehicle Detector

- Maintains extremely high confidence across all attack strengths
- Minimal effect from adversarial perturbations
- Most robust model among the three classifiers

### Traffic Light Detector

- Confidence drops from 1.000 to 0.060 at ε = 0.01
- Completely fails at ε = 0.05
- Remains highly vulnerable at ε = 0.10
- Least robust classifier

---

## Visibility of Perturbations

| Epsilon | Observation |
|----------|------------|
| 0.01 | Perturbations are visually imperceptible |
| 0.05 | Perturbations become noticeable |
| 0.10 | Perturbations are clearly visible and degrade image quality |

---

## Generated Output

File:

exercise8_fgsm_examples.png

---

# Exercise 8.5 Measuring Robustness
## Objective

Evaluate model robustness using a random subset of 100 test images.
Recall Drop = Recall(clean) − Recall(adversarial)
Positive values indicate performance degradation.
Negative values indicate recall improvement under attack.

---

## Recall Results

| Model | Epsilon | Clean Recall | Adversarial Recall | Recall Drop |
|---------|---------|---------|---------|---------|
| Pedestrian | 0.01 | 0.611 | 0.056 | 0.556 |
| Pedestrian | 0.05 | 0.611 | 0.778 | -0.167 |
| Pedestrian | 0.10 | 0.611 | 0.889 | -0.278 |
| Vehicle | 0.01 | 0.988 | 0.877 | 0.111 |
| Vehicle | 0.05 | 0.988 | 1.000 | -0.012 |
| Vehicle | 0.10 | 0.988 | 1.000 | -0.012 |
| Traffic Light | 0.01 | 0.986 | 0.099 | 0.887 |
| Traffic Light | 0.05 | 0.986 | 0.099 | 0.887 |
| Traffic Light | 0.10 | 0.986 | 0.099 | 0.887 |

---

## Robustness Analysis

### Pedestrian Detector

- Most vulnerable at ε = 0.01
- Recall decreases from 61.1% to 5.6%
- Larger perturbations unexpectedly improve recall
- Indicates unstable decision boundaries

### Vehicle Detector

- Maintains recall above 87%
- Minimal degradation
- Most robust classifier

### Traffic Light Detector

- Recall drops from 98.6% to 9.9%
- Severe degradation at all attack strengths
- Detector effectively fails under FGSM attack

---

## Recall Drop Visualization

File:

exercise8_recall_drop.png

### Graph Interpretation

- Traffic Light detector exhibits the largest recall degradation (0.887)
- Vehicle detector shows the smallest degradation
- Pedestrian detector demonstrates unstable behavior

---

# Exercise 8.6 Extending the STPA Safety Analysis
## Hazard
### H-ADV

The vehicle operates using perception outputs generated from adversarially manipulated sensor inputs, resulting in unsafe driving behavior.

Contributes to:

- H1: Failure to stop for pedestrians
- H2: Failure to obey traffic lights
- H4: Failure to avoid obstacles

---

## Unsafe Control Action
### UCA-ADV

The planner continues normal operation despite perception outputs being corrupted by adversarial perturbations.

Possible consequences:

- Missed pedestrian detection
- Missed vehicle detection
- Missed traffic light detection
- Failure to brake
- Increased collision risk

---

## Safety Constraints
### Model-Level Constraint

Perception models shall maintain acceptable robustness within the tested FGSM budget:

ε ≤ 0.10

Models showing excessive recall degradation must be retrained using adversarial training or other robustness methods.

### System-Level Constraint

When adversarial behavior or abnormal confidence is detected, the vehicle shall:

- Reduce speed
- Increase following distance
- Request sensor verification
- Perform a safe stop if uncertainty persists

---

## Residual Risk

Even with adversarial training:

- New attack methods may remain effective
- Larger perturbations may still fool the model
- OOD inputs remain a challenge
- Sensor failures and environmental conditions still pose risks

Therefore, adversarial training reduces risk but does not eliminate it completely.

---

# Key Findings

| Model | Robustness Ranking |
|---------|---------|
| Vehicle Detector | 1st (Most Robust) |
| Pedestrian Detector | 2nd |
| Traffic Light Detector | 3rd (Least Robust) |

## Main Conclusions

- High clean-data accuracy does not imply adversarial robustness.
- Different perception tasks exhibit significantly different vulnerabilities.
- The Traffic Light detector is highly susceptible to adversarial attacks.
- The Vehicle detector remains robust across all tested perturbation strengths.
- Adversarial robustness must be considered as part of the overall safety analysis for autonomous driving systems.

---

# Generated Files

## Results

```text
exercise8_fgsm_examples.png
exercise8_fgsm_results.csv
exercise8_recall_drop.csv
exercise8_recall_drop.png
```

## Notebook

```text
exercise_8.ipynb
```

## Documentation

```text
README.md
```