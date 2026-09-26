# Credit Card Fraud Detection Using Machine Learning

## Business Problem

Credit card fraud is a major challenge for financial institutions because fraudulent transactions are relatively rare but can result in significant financial losses and customer dissatisfaction.

The goal of this project was to develop a machine-learning model that can identify potentially fraudulent credit-card transactions while maintaining a reasonable balance between **detecting fraud** and **avoiding false alarms on legitimate transactions**.

This is particularly challenging because fraud represents only a very small percentage of all transactions. A model could achieve very high accuracy simply by predicting that almost every transaction is legitimate, so accuracy alone is not an appropriate measure of success.

## Project Overview

This project uses machine learning to analyze credit-card transactions and classify them as either **legitimate or fraudulent**.

The analysis covers the complete workflow, from understanding and cleaning the dataset to exploratory analysis, handling class imbalance, comparing machine-learning models, selecting the best-performing approach, tuning the classification threshold, and evaluating the final model on unseen transactions.

The project focuses primarily on **precision, recall, F1-score, and PR-AUC**, which are more useful measures for a highly imbalanced fraud-detection problem.

## Dataset

The dataset contains **284,807 transactions across 31 columns**.

It includes:

* **28 anonymized numerical features (V1–V28)**
* **Time** — the elapsed time associated with each transaction
* **Amount** — the transaction value
* **Class** — the target variable indicating legitimate or fraudulent transactions

The dataset contains no missing values, but **1,081 duplicate records** were identified and removed during data cleaning.

After cleaning, fraudulent transactions represented approximately **0.17%** of the data, compared with approximately **99.83% legitimate transactions**, confirming the severe class imbalance.

## What I Did

I first performed a data-quality audit to check the dataset structure, missing values, duplicate records, and basic statistics.

I then carried out exploratory data analysis to understand fraud patterns. This included examining the distribution of legitimate versus fraudulent transactions, transaction activity by hour, fraud rates across different hours, and transaction amounts.

The analysis showed that transaction activity was highest around **Hour 21**, while the highest observed hourly fraud rate occurred around **Hour 2**. Fraudulent transactions also had a higher average transaction amount than legitimate transactions, although their median amount was lower, indicating that fraudulent transaction values were highly variable.

For the machine-learning stage, I compared **Logistic Regression, Decision Tree, and Random Forest** models. I also tested different approaches for dealing with the severe class imbalance, including baseline models, class weighting, and SMOTE combined with undersampling.

The models were evaluated using **5-fold stratified cross-validation**, with PR-AUC used as the main metric for model comparison.

The strongest overall model configuration was a **Random Forest with balanced class weights**, which achieved a cross-validated PR-AUC of approximately **0.844**.

Rather than automatically using the standard 0.50 classification threshold, I also examined different probability thresholds to find a more appropriate balance between fraud detection and false alerts. A threshold of **0.20** was selected based on the precision and recall requirements used in the analysis.

## Results

On the held-out test set, the final model achieved:

| Metric    |     Result |
| --------- | ---------: |
| Accuracy  | **99.94%** |
| Precision | **85.19%** |
| Recall    | **80.99%** |
| F1-score  | **83.03%** |

The result means the model was able to identify approximately **81% of fraudulent transactions** while maintaining approximately **85% precision** among transactions classified as fraudulent.

The high accuracy is less important than the precision, recall, and F1-score because of the extreme imbalance in the dataset.

## Tools Used

This project was developed using **Python and Jupyter Notebook**, with:

**Pandas** for data manipulation and analysis, **NumPy** for numerical operations, **Matplotlib and Seaborn** for visualization, **Scikit-learn** for preprocessing, model development, cross-validation and evaluation, and **imbalanced-learn** for SMOTE and undersampling.

## Recommendations

The model provides a strong baseline for fraud detection, but several improvements would make the project more suitable for real-world use.

First, threshold selection should ideally be based on the actual business cost of **missed fraud versus false alerts**, rather than only predefined precision and recall targets.

The model should also be tested using **time-based validation**, since fraud patterns can change over time and a future transaction may not behave like a randomly selected historical transaction.

Adding **feature-importance and model-explainability analysis** would help investigators understand why transactions are being flagged.

Finally, a real fraud-detection system would require continuous monitoring for **changes in fraud patterns, data drift, false-positive rates, and model performance**, with periodic retraining when necessary.

## Key Takeaway

This project demonstrates how machine learning can be applied to a highly imbalanced fraud-detection problem, moving beyond simple accuracy to focus on the metrics that matter for identifying rare fraudulent transactions.

The analysis shows that **Random Forest with balanced class weights**, combined with an appropriately tuned classification threshold, provides a useful starting point for detecting fraudulent transactions while maintaining a practical balance between recall and precision.
