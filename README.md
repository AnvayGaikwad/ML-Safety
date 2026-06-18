# Introduction to Machine Learning Safety

This repository contains the code, notebooks, and results developed for the Introduction to Machine Learning Safety practical exercises.

# Contents

* Exercise 3.4: Dataset Exploration
* Exercise 3.5: Training Binary Classifiers
* Exercise 3.6: Evaluation of Perception Models
* Exercise 4.5: ODD Coverage using k-Projection Coverage
* Exercise 5.1: Designing LLM Evaluation Studies
* Exercise 5.2: Evaluating a Coding Agent
* Exercise 5.3: Poisoning for Prompt Injection Backdoors
* Exercise 5.4: Temperature Scaling and Confidence Thresholds
* Exercise 5.5: Backdoor Attack on the Pedestrian Detector
* Exercise 6.5: Applying Grad-CAM for Model Explainability
* Exercise 6.6: Explainability as a Diagnostic Tool under Distribution Shift
* Exercise 7.4 Visualising the Distribution Shift
* Exercise 7.5 Is the Different Town Out-of-Distribution
* Exercise 7.6 Evaluating the MSP Baseline
* Exercise 7.7 Feature-Based OOD Detection
* Exercise 7.8 Extending the Safety Analyasis for OOD
* Exercise 8.4 Generating Adversarial Examples
* Exercise 8.5 Measuring Robustness

# Dataset

The CARLA dataset is not committed to GitHub because of its size.

The dataset zip files can be obtained from:
https://cloud.ovgu.de/s/fYD35gBQA7Ykqom

# Required Dataset Files

* train.zip
* validation.zip
* test.zip
* test-fog.zip
* test-night.zip
* test-town-01.zip

# Models

Trained model files are stored locally under:

`models/`

Generated models:

* pedestrian_model.pth
* vehicle_model.pth
* traffic_light_model.pth
* backdoor_pedestrian_model.pth

# Notebooks

* excercise-3/excercise_3.ipynb
* excercise-4/excercise_4.ipynb
* excercise-5/excercise_5.ipynb
* excercise-6/excercise_6.ipynb
* excercise-7/excercise_7.ipynb
* excercise-8/excercise_8.ipynb

Each exercise folder also contains a dedicated README.md describing the implementation and theoretical answers.

# Results

The results folder contains a dedicated folder for each exercise that contains the output images along with the .csv files for storing the numeric values.

# External Implementation for Exercise 4.5

The k-projection coverage implementation was obtained from:

https://github.com/kkirchheim/odd-coverage

Clone using:

```bash
git clone https://github.com/kkirchheim/odd-coverage.git
```

# Python Setup

Import all necessary packages before implementation
```

Install required packages using:

```bash
pip install torch torchvision pandas matplotlib pillow scikit-learn tqdm
```

# Notes

* The repository commits only source code, notebooks, README files, and result files.
* Datasets are excluded from Git using `.gitignore`.
* Trained model files are excluded from Git using `.gitignore`.
* Virtual environment files are excluded from Git using `.gitignore`.
* Individual exercise documentation is maintained inside each exercise folder.
* This README should be updated whenever a new exercise is completed.
