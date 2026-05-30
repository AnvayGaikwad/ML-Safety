# Exercise 3 – Training the CARLA Baseline Models

## Overview

In this exercise, three separate binary classifiers were trained using the CARLA dataset:

1. Pedestrian Detector
2. Vehicle Detector
3. Traffic Light Detector

The dataset consists of forward-facing camera images collected in CARLA under clear daytime conditions. Each image contains three binary labels indicating whether a pedestrian, vehicle, or traffic light is present.

A separate ResNet18 model was trained for each task. The final fully connected layer was modified to output a single value for binary classification. BCEWithLogitsLoss was used as the loss function and the Adam optimizer was used with a learning rate of 0.001.

### Training Configuration

#### Pedestrian Detector

* Model: ResNet18
* Loss Function: BCEWithLogitsLoss
* Optimizer: Adam
* Learning Rate: 0.001
* Batch Size: 32
* Epochs: 5

#### Vehicle Detector

* Model: ResNet18
* Loss Function: BCEWithLogitsLoss
* Optimizer: Adam
* Learning Rate: 0.001
* Batch Size: 64
* Epochs: 5

#### Traffic Light Detector

* Model: ResNet18
* Loss Function: BCEWithLogitsLoss
* Optimizer: Adam
* Learning Rate: 0.001
* Batch Size: 64
* Epochs: 5

---

# Exercise 3.4 – Dataset Exploration

## 3.4.1 How many images are in the training and test splits?

* Training images: 7,200
* Test images: 3,600

## 3.4.2 What is the class distribution for each label? Are the classes balanced?

### Traffic Light

| Label | Count | Percentage |
| ----- | ----- | ---------- |
| True  | 5,276 | 73.28%     |
| False | 1,924 | 26.72%     |

### Pedestrian

| Label | Count | Percentage |
| ----- | ----- | ---------- |
| True  | 1,718 | 23.86%     |
| False | 5,482 | 76.14%     |

### Vehicle

| Label | Count | Percentage |
| ----- | ----- | ---------- |
| True  | 5,458 | 75.81%     |
| False | 1,742 | 24.19%     |

### Discussion

The dataset is not perfectly balanced. The pedestrian task is the most imbalanced, with only 23.86% positive examples. Traffic light and vehicle labels are also skewed toward the positive class. Such imbalance can affect model training because the classifier receives significantly more examples from one class than the other.

## 3.4.3 Display a few example images for each label combination. What patterns do you notice?

Several label combinations were visualized. The images show typical urban driving scenes collected from a front-facing camera.

Observed patterns include:

* Vehicles appear in most scenes, explaining the high proportion of positive vehicle labels.
* Pedestrians are less common and typically appear near sidewalks, crossings, or intersections.
* Traffic lights are usually visible at intersections and urban road segments.
* Many scenes contain both vehicles and traffic lights simultaneously.
* Some images contain none of the target objects, representing simpler road segments.

These observations are consistent with realistic urban driving environments and help explain the observed label distributions.

---

# Exercise 3.5 – Train Three Binary Classifiers

## 3.5.1 Describe your model architecture and training setup

All three classifiers use a pre-trained ResNet18 backbone.

The final fully connected layer was replaced with a single output neuron to perform binary classification. Images were resized to 224 × 224 pixels and converted to tensors before being passed through the network.

Training configuration:

* Architecture: ResNet18
* Loss Function: BCEWithLogitsLoss
* Optimizer: Adam
* Learning Rate: 0.001
* Image Size: 224 × 224
* Binary classification output
* Five training epochs for each model

## 3.5.2 Train each model and plot the training and validation loss curves. Do the models converge?

### Pedestrian Detector

Training loss decreased steadily across the five epochs, indicating that the model learned useful features from the training data.

Validation loss fluctuated throughout training and remained relatively high compared to the training loss. This behaviour suggests that the model learned the task but may suffer from mild overfitting due to the limited number of positive pedestrian examples and the class imbalance present in the dataset.

### Vehicle Detector

Training loss decreased consistently throughout training.

Validation loss also decreased and remained stable, indicating good convergence and effective learning. The vehicle detector generalized well to unseen validation data.

### Traffic Light Detector

Training loss decreased rapidly and reached very low values.

Validation loss remained low throughout training, demonstrating strong convergence and excellent generalization performance on the validation set.

### Conclusion

All three models converged successfully as indicated by the decreasing training losses. The vehicle and traffic light detectors showed stronger convergence and generalization than the pedestrian detector. The pedestrian task is more difficult because of class imbalance and the smaller visual size of pedestrians within the images.

## 3.5.3 Why might training three separate models be preferable to a single multi-label classifier from a safety perspective?

From a safety perspective, separate models provide several advantages:

* Failures can be analyzed independently for each safety-critical object.
* Performance metrics can be monitored separately for pedestrians, vehicles, and traffic lights.
* Retraining one detector does not affect the behaviour of the others.
* Safety validation and certification become easier because each model has a clearly defined responsibility.
* Fault isolation is improved, making debugging and risk assessment more straightforward.

Therefore, separate classifiers improve transparency, traceability, and safety assurance.

---

# Exercise 3.6 – Evaluation

## 3.6.1 Report accuracy, precision, recall, and F1-score for each model

### Pedestrian Detector

| Metric    | Value  |
| --------- | ------ |
| Accuracy  | 0.7164 |
| Precision | 0.3478 |
| Recall    | 0.5099 |
| F1 Score  | 0.4136 |

### Vehicle Detector

| Metric    | Value  |
| --------- | ------ |
| Accuracy  | 0.8194 |
| Precision | 0.8242 |
| Recall    | 0.9652 |
| F1 Score  | 0.8891 |

### Traffic Light Detector

| Metric    | Value  |
| --------- | ------ |
| Accuracy  | 0.9394 |
| Precision | 0.9349 |
| Recall    | 0.9841 |
| F1 Score  | 0.9589 |

## 3.6.2 Which model performs worst? Hypothesize why.

The pedestrian detector performs worst.

Possible reasons include:

* The pedestrian dataset is highly imbalanced.
* Pedestrians occupy a smaller portion of the image than vehicles or traffic lights.
* Pedestrians exhibit greater variation in appearance, pose, and distance.
* The model receives significantly fewer positive training examples.

These factors make pedestrian detection more challenging than vehicle or traffic light detection.

## 3.6.3 From a safety perspective, which metric matters most for each model – precision or recall?

### Pedestrian Detector

Recall is the most important metric.

Missing a pedestrian can directly lead to collisions and severe safety consequences. Therefore, minimizing false negatives is more important than minimizing false positives.

### Vehicle Detector

Recall is also the most important metric.

Failing to detect another vehicle could result in unsafe driving decisions. Detecting additional vehicles is generally safer than failing to detect a real vehicle.

### Traffic Light Detector

Recall remains the most important metric because missing a traffic light could lead to violations of traffic rules or unsafe vehicle behaviour. Precision is also important, but false negatives pose the greater safety risk.

Overall, recall is the most safety-critical metric for all three detection tasks because missed detections can directly impact safe operation.

---

# Exercise 3.7 – Comparing the ODD to the Training Data

## 3.7.1 What conditions does the training data cover?

The training data covers:

* Daytime conditions
* Clear weather
* Urban driving environments
* Intersections and city streets
* Front-facing camera viewpoint
* Presence of pedestrians, vehicles, and traffic lights

These are the conditions represented in the training and validation datasets used to train the three classifiers.

## 3.7.2 Which ODD dimensions are not covered by the training data?

The training data only contains clear daytime urban scenes. However, the repository also contains separate evaluation datasets such as test-fog, test-night, and test-town-01. These conditions are not represented in the training set and therefore constitute ODD gaps from a training perspective.

Missing ODD dimensions include:

* Fog
* Night-time conditions
* Reduced visibility scenarios
* Different town layouts and road structures
* Distribution shifts compared to the training environment
* Potentially different traffic densities and scene configurations

## 3.7.3 What are the implications of this gap for model safety?

Because the models were trained only on clear daytime data, their performance under fog, night-time conditions, or different town environments cannot be guaranteed.

Although dedicated evaluation datasets exist for these conditions, they have not been used during training. Therefore, the models have not learned features specific to these environments.

As a result, no reliable claims can currently be made regarding model performance, robustness, or safety in these unseen conditions. Performance degradation may occur when the models are deployed outside their training distribution.

## 3.7.4 Note down the gaps identified

The following ODD gaps were identified:

* Fog
* Night-time driving
* Reduced visibility conditions
* Different town layouts (Town01)
* Distribution shifts relative to the training environment
* Potentially unseen traffic and road configurations

These gaps will be revisited in subsequent exercise sheets when ODD coverage and robustness are formally evaluated.
