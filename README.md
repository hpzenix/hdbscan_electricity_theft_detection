# Electricity Theft Detection using HDBSCAN on Smart Meter Data

## Overview

This project implements an unsupervised electricity theft detection framework based on the research [paper](paper/A_Novel_Density_Based_Clustering_Approach_for_Electricity_Theft_Detection.pdf) methodology using the HDBSCAN clustering algorithm and DBCV-based hyperparameter tuning.

The implementation is performed on Dataset-2 of the smart meter dataset for July 2018. The complete workflow includes data cleaning, theft generation, preprocessing, clustering, anomaly ranking, and performance evaluation.

The objective is to identify fraudulent electricity consumers using only smart meter consumption data without requiring labeled training data, observer meters, or network topology information.

---

# Dataset Preparation Pipeline

## 1. Fetch July 2018 Smart Meter Data

The original smart meter dataset for July 2018 was fetched and used as the base dataset.

The raw data initially consisted of:

* 15-minute interval consumption readings
* timestamp information
* meter-wise electricity consumption profiles

---

## 2. Data Cleaning

The raw dataset was cleaned before further processing.

The following records were removed:

* Missing value records (NaN data)
* Duplicate timestamp records
* Invalid or inconsistent entries

This produced a clean smart meter dataset for July 2018.

---

## 3. Conversion from 15-Minute Profiles to Daily Load Profiles

The original 15-minute interval data was converted into hourly daily load profiles.

### Conversion Process

* Four consecutive 15-minute readings were aggregated into one hourly value.
* Each day was represented using 24 hourly readings.
* each daily profile contains 24 dimensions

---

# Consumer Selection and Area Formation

## 4. Random Selection of 150 Consumers

A total of 150 smart meter consumers were randomly selected from the cleaned dataset.

These 150 consumers were used throughout the experiments.

---

## 5. Division into 3 Areas

The selected 150 consumers were randomly divided into:

* Area 1 → 50 consumers
* Area 2 → 50 consumers
* Area 3 → 50 consumers

This follows the experimental setup described in the reference paper.

---

# Fraud Consumer Selection

## 6. Fraud Consumer Identification

For each area:

* 9 consumers were randomly selected as fraudulent consumers.

Therefore:

* Total fraudulent consumers = 27
* Total honest consumers = 123

The same fraudulent consumers were used across all theft modes.

---

# Theft Data Generation

## 7. Theft Generation

Five different electricity theft modes were generated for the selected fraudulent consumers.

The theft generation process was applied only to the fraudulent consumers, while honest consumers retained their original consumption profiles.

The generated theft modes include:

* Mode 1
* Mode 2
* Mode 3
* Mode 4
* Mode 5

Each theft mode modifies the original electricity consumption profile according to the mathematical equations defined in the reference paper.

---

# Data Preprocessing

After theft generation, preprocessing was performed separately for each theft mode dataset.

---

## 8. Missing Value Recovery

Missing hourly readings were recovered using neighboring consumption values.

The preprocessing logic follows the methodology described in the paper.

---

## 9. Erroneous Value Recovery

Erroneous values were identified using:


Threshold = 3 x σ


where:

* σ is the standard deviation of the daily load vector

Values greater than the threshold were replaced using neighboring hourly values.

Cross-day circular indexing was implemented:

* Hour 0 uses previous day Hour 23
* Hour 23 uses next day Hour 0

Special edge cases handled:

* First reading of Day 1 ignored
* Last reading of Day 31 ignored

---

## 10. Daily Load Profile Normalization

Each daily load profile was normalized by dividing all hourly readings by the maximum value of that specific daily profile.

This normalization removes magnitude differences between consumers and preserves consumption behavior patterns.

---

# Electricity Theft Detection using HDBSCAN

## 11. Daily Matrix Construction

For each day of July 2018:

* all 150 consumers’ normalized daily profiles were collected

Matrix (O) was formed resulting matrix shape: 150 x 24


where:

* rows represent consumers
* columns represent hourly consumption values

---

# HDBSCAN Clustering

## 12. Hyperparameter Tuning using DBCV

HDBSCAN hyperparameters were optimized using DBCV (Density-Based Clustering Validation).

The following parameters were tuned:

* `min_cluster_size` : 2 → 20
* `min_samples` : 2 → 20

For every parameter combination:

1. HDBSCAN clustering was performed
2. DBCV score was computed
3. Best parameter combination selected using maximum DBCV score

---

## 13. Noise Consumer Detection

Consumers labeled as noise by HDBSCAN were treated as potential fraudulent consumers.

These consumers formed the suspicious consumer set: [
Z
]

The consumers in (Z) were ranked in descending order based on outlier scores.

Higher outlier score implies higher probability of electricity theft involvement.

---

# Performance Evaluation

## 14. Evaluation Metrics

The following evaluation metrics were computed for each day:

---

### 1. sklearn ROC-AUC

Standard ROC-AUC score computed using outlier scores and ground truth fraud labels.

---

### 2. Paper AUC (Equation 12)

AUC was also computed using the ranking-based formulation defined in the reference paper.

The paper AUC evaluates the ranking quality of fraudulent consumers among normal consumers.

---

### 3. MAP@10

MAP@10 (Mean Average Precision at Top-10) was computed using the ranked suspicious consumer list.

This evaluates how effectively fraudulent consumers appear within the top suspicious positions.

---

# Experiment Execution

## 15. Daily Experiment Workflow

For each theft mode:

* Day-wise experiments were performed for all 31 days of July 2018.
* Each daily run processed all 150 consumers simultaneously.

The workflow for each day included:

1. Daily matrix creation
2. Hyperparameter tuning
3. HDBSCAN clustering
4. Noise consumer ranking
5. Metric computation
6. Result storage

---

# Final Outputs

For each theft mode:

* Mean sklearn ROC-AUC
* Standard deviation of sklearn ROC-AUC
* Mean Paper AUC
* Standard deviation of Paper AUC
* Mean MAP@10
* Standard deviation of MAP@10
* Mean DBCV
* Mean number of noise consumers

were computed across all 31 days.

---

# Technologies Used

* Python
* Pandas
* NumPy
* Scikit-learn
* HDBSCAN
* Matplotlib

---

# Key Concepts Used

* Unsupervised Learning
* Density-Based Clustering
* Anomaly Detection
* Smart Meter Analytics
* Electricity Theft Detection
* Hyperparameter Optimization
* Outlier Scoring
* Daily Load Profile Analysis

---
