# Predicting 30-Day Readmission to Prioritize Diabetes Follow-Up Care


A machine-learning decision-support project that estimates the risk of a diabetes patient being readmitted within 30 days of hospital discharge. The predicted probabilities are intended to help care-coordination teams prioritize limited follow-up resources, such as phone calls, medication reviews, and appointment reminders.


## Table of Contents
* Project Overview
* Dataset
* Prediction Task
* Planned Approach
* Models
* Evaluation
* Success Criteria
* Risks and Responsible Use
* Getting Started
* Team
* References

## Project Overview

Hospitals have limited staff, time, and funding for post-discharge care, so they cannot provide every patient with the same level of follow-up. This project treats 30-day readmission as an operational prediction and prioritization problem.

The system will:

1. Estimate each eligible patient's probability of readmission within 30 days.

2. Rank patients by predicted risk.

3. Group patients into high-, medium-, and low-risk categories.

4. Evaluate how many readmissions could be identified when follow-up capacity is limited.

5. Unlike prior work that primarily examined the statistical relationship between HbA1c testing and readmission, this project will compare multiple machine-learning models using a broader set of patient, treatment, and encounter characteristics.

## Dataset

This project uses the Diabetes 130-US Hospitals for Years 1999-2008 dataset from the UCI Machine Learning Repository.


| Property | Description | 
| -------- | ----------- | 
| Source   | UCI Machine Learning Repository     |
| Time period  | 1999-2008     | 
| Size  | 101,766 hospital encounters     | 
| Coverage  | 130 U.S. hospitals and integrated delivery networks    | 
| Unit of observation  | Hospital encounter    | 
| Original target  | Readmitted in fewer than 30 days, after 30 days, or not readmitted    | 
| Project target  | Readmitted within 30 days vs. not readmitted within 30 days   | 


Available features include patient demographics, admission information, diagnoses, time in the hospital, previous hospital visits, laboratory testing, medications, and changes in diabetes treatment.

## Data Handling

* Exclude identifiers such as encounter ID and patient number from predictive features.

* Remove records indicating that a patient could not be readmitted, including applicable hospice or death discharge dispositions.

* Examine missingness before modeling.

* Remove extremely sparse features when justified or represent missingness with an appropriate category.

* Fit imputation and other preprocessing steps using training data only.

* Verify that every feature would be available at the intended prediction time: hospital discharge.

## Prediction Task

The original three-class readmission label will be converted into a binary target:

* Positive class: readmitted in fewer than 30 days
* Negative class: readmitted after 30 days or not readmitted

Since early readmission is less common than non-readmission, ordinary accuracy will not be treated as the primary measure of model quality.

## Planned Approach

flowchart LR

    A[Raw encounter data] --> B[Eligibility filtering]
    B --> C[Exploratory analysis]
    C --> D[Patient-level split]
    D --> E[Training-only preprocessing]
    E --> F[Model training]
    F --> G[Probability calibration]
    G --> H[Risk ranking]
    H --> I[Performance and fairness evaluation]

## Leakage-Aware Data Splitting

Encounters will be split by patient rather than by individual encounter. All encounters belonging to one patient will remain in the same partition, preventing the same patient from appearing in both training and test data.

The main analysis will retain all eligible encounters because hospitals make follow-up decisions at each discharge. As a sensitivity analysis, the final model will also be evaluated using only the first encounter per patient in the test set.

Since encounters from the same patient are correlated, confidence intervals will be estimated by resampling patients rather than individual encounters.

## Models

| Model | Role in the project | 
| -------- | ----------- | 
| Logistic regression   | Interpretable baseline     |
| Random forest  | Capture nonlinear relationships and feature interactions     | 
| Gradient-boosted decision trees  | Evaluate a more advanced ensemble approach     | 

Categorical features will be encoded, and numerical features will be scaled when required by the model.

## Evaluation

### Predictive Performance

* Precision-recall area under the curve (PR-AUC)
* Receiver operating characteristic area under the curve (ROC-AUC)
* Recall
* Precision
* Confusion matrix

### Probability Quality

* Calibration plot
* Brier score

### Capacity-Based Evaluation

To simulate limited follow-up capacity, patients will be ranked by predicted risk. The analysis will measure how many observed 30-day readmissions are captured among the top:

* 10% of patients by predicted risk
* 20% of patients by predicted risk

### Fairness Evaluation

Performance will be reported by age, race, and gender. Subgroup confidence intervals will be included where sample sizes permit, and substantial disparities or limitations will be identified explicitly.

## Success Criteria

The project will be considered successful if it produces a reproducible patient-level evaluation and demonstrates useful prioritization beyond simple baselines. The best model should:

* Outperform a majority-class baseline and the logistic-regression baseline on PR-AUC or ranking lift.
* Capture at least 30% of observed 30-day readmissions within the top 20% of patients ranked by risk. This corresponds to at least 1.5 times the performance of random selection.
* Produce acceptably calibrated probabilities, evaluated with a calibration plot and Brier score.
* Report performance across demographic groups and clearly describe substantial disparities or limitations.

## Risks and Responsible Use

| Risk | Planned mitigation | 
| -------- | ----------- | 
| Class imbalance   | Emphasize PR-AUC, threshold selection, and capacity-based measures instead of accuracy alone.     |
| Missing or inconsistent data  | Analyze missingness, justify feature removal, and fit imputation using training data only. | 
| Leakage from repeated patients  | Split by patient and exclude identifiers from predictive features.     | 
| Demographic disparities  | Report subgroup metrics and confidence intervals where sample sizes permit.    | 
| Dataset age | Present findings as an evaluation of historical 1999-2008 data, not evidence of readiness for current clinical deployment.    | 
| Overreliance on predictions  | Position the model as a prioritization aid that complements, rather than replaces, clinical judgment.    | 


## Getting Started

The implementation is still in development. 

Download the dataset from the UCI dataset page. Do not commit raw patient encounter data to the repository.


## Team

* Karla Nguyen

* Anishka Chauhan

Developed for CS 171: Introduction to Machine Learning at San Jose State University.

## References

Clore, J., et al. (2014). Diabetes 130-US Hospitals for Years 1999-2008. UCI Machine Learning Repository. https://doi.org/10.24432/C5230J

Strack, B., et al. (2014). Impact of HbA1c measurement on hospital readmission rates: Analysis of 70,000 clinical database patient records. BioMed Research International, 2014, Article 781670. https://doi.org/10.1155/2014/781670

