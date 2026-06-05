# Exercise 6 – Explainability and Diagnostics

The goal of this exercise was to investigate how the trained perception models make decisions and whether they rely on meaningful object features or contextual scene information. Grad-CAM was applied to the pedestrian, vehicle, and traffic-light detection models to visualize important image regions. The models were then evaluated under foggy weather, nighttime conditions, and a different town layout to analyze robustness and generalization.

---

## 6.2 Local vs Global Explainability

### 1. Difference between local and global explainability

**Local explainability** explains a single prediction made by a model. It helps understand why a specific output was produced for a particular input image.

**Global explainability** explains the overall behavior of the model across the entire dataset and identifies general decision patterns and feature importance.


### 2. Which is more useful for debugging a misclassified image?

Local explainability is more useful because it focuses on a specific prediction and reveals which image regions influenced the model's decision. This helps identify whether the model relied on relevant objects or irrelevant background features.

---

## 6.3 Saliency Maps vs Occlusion

### 1. Comparison

**Saliency maps** use gradients to identify pixels that most influence a prediction.

**Occlusion** systematically removes parts of the image and measures the impact on the model's prediction.

### 2. Advantages and Disadvantages

#### Saliency Maps

**Advantage:** Computationally efficient because only one backward pass is required.

**Disadvantage:** Can produce noisy and difficult-to-interpret explanations.

#### Occlusion

**Advantage:** Easy to interpret and model-agnostic.

**Disadvantage:** Computationally expensive because many forward passes are required.

---

## 6.4 Chain-of-Thought Fidelity

### 1. What is a faithful chain-of-thought explanation?

A faithful explanation accurately reflects the actual reasoning process used by the model when producing a prediction.

### 2. Why can a plausible explanation be misleading?

An explanation may sound reasonable to humans while not representing the true factors influencing the model's decision. This can create a false sense of trust.

### 3. Why is fidelity important in safety-critical systems?

In safety-critical applications such as autonomous driving, explanations must accurately reflect model behavior so that errors, biases, and unsafe reasoning can be identified.

---

## 6.5 Applying an Explainability Method

### 1. Chosen Method

Grad-CAM was selected because it is well suited for CNN-based models and provides intuitive visual explanations through heatmaps showing which image regions influenced a prediction.

### 2. Correctly Classified Images

Grad-CAM was applied to five correctly classified examples across the pedestrian, vehicle, and traffic-light models.

### 3. Do the highlighted regions correspond to relevant objects?

Partially. The pedestrian model highlights regions containing pedestrians but also focuses on surrounding scene context. The vehicle model mainly focuses on the road center and driving corridor rather than clearly localizing vehicles. The traffic-light model highlights the traffic-light region but also relies on the surrounding intersection area.

### 4. Misclassified Images

Three misclassified examples were analyzed using Grad-CAM.

The pedestrian false positive focuses on roadside structures and traffic-light regions rather than pedestrians. The vehicle false positive focuses on the road center and horizon area despite no vehicle being present. The traffic-light false positive highlights the intersection region instead of only the traffic light. These explanations suggest that the models sometimes rely on contextual features rather than purely object-specific features.

---

## 6.6 Explainability as a Diagnostic Tool

### Accuracy Results

| Model         | Fog    | Night  | Town01 |
| ------------- | ------ | ------ | ------ |
| Pedestrian    | 80.36% | 79.58% | 59.53% |
| Vehicle       | 53.11% | 34.47% | 70.72% |
| Traffic Light | 72.08% | 34.06% | 80.72% |

### (a) Do the highlighted regions still correspond to the relevant objects?

Only partially. The pedestrian model continues to focus on pedestrians but also emphasizes nearby scene context. The vehicle model frequently highlights road and horizon regions rather than specific vehicles. The traffic-light model highlights traffic-light locations but also depends on broader intersection features.

### (b) Evidence of background or spurious features?

Yes. The Grad-CAM visualizations show attention on road structure, horizon regions, buildings, lighting conditions, and environmental layout. This indicates that the models rely partly on contextual information learned during training rather than solely on target objects.

### (c) How do accuracy and explanation quality change across conditions?

The pedestrian model remains relatively stable in fog and night conditions but performs worse in Town01. The vehicle and traffic-light models show significant performance degradation at night. The explanations also become less object-focused under distribution shifts, suggesting reduced robustness and increased reliance on environmental context.