# Malawi Child Depression ML

Machine learning project using UNICEF Multiple Indicator Cluster Survey (MICS) data to investigate whether children aged 5–17 in Malawi are likely to report any form of depression related symptoms.

## Overview

This project analyses a subset of UNICEF MICS data collected in Malawi during 2019–2020. The dataset includes child-level, maternal, and household-level variables, covering aspects such as child background, child labour, household characteristics, water and sanitation, maternal background, life satisfaction, victimisation, and family context.

The target variable is based on survey responses to whether the child seemed very sad or depressed. For the modelling task, the original responses were converted into a binary outcome:

- **No Depression**: the child never had depressive symptoms.
- **Depression** : all responses other than "never".

The aim of the project was to build a **predictive model** to classify whether children reported any depressive symptoms, while also identifying the **factors** that appeared most relevant in explaining this outcome.

## Methodology

The analysis was carried out in Python and included:

- Exploratory Data Analysis on numeric and categorical variables
- Preprocessing pipelines for different variable types
- Handling of missing values and categorical encodings
- Feature selection using L1-penalised Logistic Regression
- Final Logistic Regression model with L2 penalty
- Comparison with a Random Forest classifier
- Interpretation of selected variables through model coefficients and feature importance

### Feature selection approach

Feature selection was performed using a repeated L1-penalised Logistic Regression procedure.

The model was repeatedly fitted on bootstrap samples of the training set. At each iteration, the L1 penalty encouraged sparse solutions by shrinking some coefficients to zero. Features were then ranked according to how frequently they were selected across the repeated fitting process.

Only variables that appeared consistently above a chosen selection frequency threshold were retained for the final model. This was done to reduce the effect of unstable feature selection and focus on predictors that were more consistently informative across resampled versions of the data.

## Models

The final modelling pipeline compared two main approaches:

1. **Logistic Regression**
   - L1 penalty used for feature selection
   - L2-penalised Logistic Regression used as the final interpretable model

2. **Random Forest Classifier**
   - Used as a more flexible model to assess whether non-linear patterns could improve predictive performance (using all features available in the dataset)

## Results

The final chosen model was an L2-penalised Logistic Regression fitted on the restricted feature set. This choice better matched the aim of the project: the model achieved performance similar to the Random Forest, while allowing a clearer interpretation of the selected coefficients.

In particular, the Logistic Regression model was useful as an interpretable screening tool, allowing inspection of the direction and magnitude of associations between selected variables and the outcome. The Random Forest model showed similar performance, suggesting that the available predictors contained only limited predictive signal for this classification task.

Overall, the analysis suggests that depression-related responses may be associated with factors linked to household conditions, maternal circumstances, discrimination or victimisation, and family stress. 

## Limitations

Several limitations should be considered:

- The target variable is based on a single survey question about whether the child seemed very sad or depressed. This is only a rough proxy for depression and not a clinical diagnosis.
- Different severities of depression-related responses were collapsed into a single binary outcome, which may remove useful information.
- Some important aspects of child mental health were not available in the dataset, such as more detailed diagnostic indicators or broader measures of emotional and functional impairment.
- Missing values were present in several variables, and imputation may introduce additional uncertainty.
- The analysis is observational, so the model can identify associations but cannot establish causal relationships.
- Predictive performance was moderate, suggesting that the available variables only partially explain the outcome.

## Possible improvements

Future work could improve the analysis by:

- Using a richer and more clinically specific measure of childhood depression
- Modelling different severity levels instead of collapsing the outcome into a binary variable
- Exploring spatial or multilevel models to account for geographic and household-level clustering
- Incorporating additional relevant variables from the extended MICS data sources
- Investigating whether different groups of children show different patterns of risk factors

## Data

The raw dataset is not included in this repository because it is subject to data agreements and cannot be shared publicly.

Please refer to the official UNICEF MICS website for information on data access and documentation.

## Report

The full project report is available [here](report/final_report.pdf).
