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

Each exercise folder also contains a dedicated README.md describing the implementation and theoretical answers.

# Results

## Exercise 3

* exercise3_results.csv
* pedestrian_loss.png
* vehicle_loss.png
* traffic_light_loss.png

## Exercise 4

* exercise4_results.csv
* k_projection_coverage.csv
* pedestrian_confusion_matrix.png
* vehicle_confusion_matrix.png
* traffic_light_confusion_matrix.png

## Exercise 5

* exercise5_results.csv
* trigger_example.png
* confidence_distribution_T_0.5.png
* confidence_distribution_T_1.0.png
* confidence_distribution_T_2.0.png
* normal_test_confidence_distribution.png
* fog_test_confidence_distribution.png
* night_test_confidence_distribution.png
* town01_test_confidence_distribution.png

# External Implementation for Exercise 4.5

The k-projection coverage implementation was obtained from:

https://github.com/kkirchheim/odd-coverage

Clone using:

```bash
git clone https://github.com/kkirchheim/odd-coverage.git
```

# Python Setup

Create a virtual environment:

```bash
python3 -m venv .venv
```

Activate:

Linux / macOS:

```bash
source .venv/bin/activate
```

Windows:

```bash
.venv\Scripts\activate
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
