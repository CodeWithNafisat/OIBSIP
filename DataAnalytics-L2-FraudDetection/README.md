# Credit Card Fraud Detection

A machine-learning project for detecting fraudulent credit-card
transactions under severe class imbalance.

## Project Overview

Credit-card fraud detection is a highly imbalanced classification
problem. Fraud represents only a very small proportion of transactions,
so accuracy alone is not a useful measure of model performance.

This project applies **EDA, data cleaning, preprocessing, imbalance
handling, cross-validation, threshold tuning, and model evaluation** to
identify fraudulent transactions while controlling false-positive
alerts.

## Business Problem

Financial institutions need to detect fraudulent transactions while
avoiding unnecessary alerts on legitimate customers.

The key challenge is balancing:

-   **Recall:** catching fraudulent transactions.
-   **Precision:** reducing false fraud alerts.

For this reason, the project focuses on **PR-AUC, precision, recall, and
F1-score**, alongside ROC-AUC.

## Dataset

**Source:** [Credit Card Fraud Detection ---
Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)

The dataset contains:

-   284,807 original transactions
-   30 predictor variables
-   `Time`, `Amount`, and anonymized features `V1–V28`
-   `Class` as the fraud target

After removing **1,081 duplicates**, the dataset contained **283,726
transactions**, including **473 fraud cases (0.167%)**.

## Analysis & Methodology

The workflow was:

``` text
Data Audit

Duplicate Removal & EDA

Stratified Train/Test Split
   
Scaling
   
Imbalance Handling
   
5-Fold Cross-Validation
   
Model Selection
   
Threshold Tuning
   
Final Test Evaluation
```

### Exploratory Data Analysis

The analysis examined:

-   Fraud vs. legitimate transaction distribution
-   Transaction amount distributions
-   Fraud rate across derived transaction hours
-   Class imbalance and its impact on model evaluation

### Models Tested

Three models were compared:

-   Logistic Regression
-   Decision Tree
-   Random Forest

For each model, I evaluated baseline, class-weighted, and SMOTE-based
approaches.

SMOTE and random undersampling were applied inside the training pipeline
to avoid data leakage.

## Results

Random Forest baseline achieved the strongest cross-validation PR-AUC
and was selected for final testing.

  -----------------------------------------------------------------------------
  Model              PR-AUC      ROC-AUC    Precision       Recall           F1
  ------------ ------------ ------------ ------------ ------------ ------------
  **Random       **0.8015**       0.9265   **0.8370**   **0.7958**   **0.8159**
  Forest**                                                         

  Logistic           0.7040   **0.9647**       0.7895       0.7394       0.7636
  Regression                                                       
  -----------------------------------------------------------------------------

The final Random Forest used a tuned probability threshold of **0.170**.
                     
The model correctly detected **113 fraudulent transactions**, generated
**22 false positives**, and missed **29 fraud cases** on the held-out
test set.

> Although Logistic Regression had a higher ROC-AUC, Random Forest
> achieved better PR-AUC, precision, recall, and F1-score on the test
> set. This highlights why PR-AUC is particularly useful for highly
> imbalanced fraud detection.

## Key Findings

-   Fraud accounted for only **0.167%** of the cleaned dataset.
-   Accuracy would be misleading for this problem.
-   Random Forest provided the strongest overall precision-recall
    performance among the tested final models.
-   Threshold tuning improved the model's ability to balance fraud
    detection and false alerts.
-   The most influential Random Forest features included **V17, V12,
    V14, V16, and V10**. Because the features are anonymized, their
    business meaning cannot be directly interpreted.

## Tools Used

**Python · Pandas · NumPy · Matplotlib · Seaborn · Scikit-learn ·
imbalanced-learn · Jupyter Notebook**

## Project Structure

``` text
DataAnalytics-L2-FraudDetection/
├── README.md
├── FraudDetection.ipynb

```

## Reproducibility

1.  Download `creditcard.csv` from the [Kaggle
    dataset](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud).
2.  Place the dataset in the expected project location.
3.  Install the required Python libraries.
4.  Open and run `FraudDetection_clean.ipynb`.

The notebook uses `RANDOM_STATE = 42`.

## Conclusion

This project demonstrates an end-to-end approach to fraud detection
under extreme class imbalance. The final Random Forest achieved **0.8015
PR-AUC, 0.8370 precision, 0.7958 recall, and 0.8159 F1-score** on the
held-out test set.

The main practical lesson is that fraud detection should be evaluated
using metrics that reflect the minority-class problem, rather than
relying on accuracy alone.
