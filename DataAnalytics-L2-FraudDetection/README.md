# Credit Card Fraud Detection

A machine learning project that finds fraudulent credit card transactions, even though fraud is very rare in the data.

**Final result:** The Random Forest model caught **113 of 142 frauds (79.6%)** in the test set. About **84% of the transactions it flagged were truly fraud**, with only **22 false alarms** out of roughly 85,000 transactions.

---

## Project Overview

Credit-card fraud detection is a highly imbalanced classification
problem. Fraud represents only a very small proportion of transactions,
so accuracy alone is not a useful measure of model performance.

This project applies **EDA, data cleaning, preprocessing, imbalance
handling, cross-validation, threshold tuning, and model evaluation** to
identify fraudulent transactions while controlling false-positive
alerts.

---
## Quick Summary

**Problem**:  Find fraud in card transactions when only 0.17% are fraud

**Data**: 283,726 transactions after cleaning ([Kaggle dataset](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)) 

**What I did**:  Cleaned the data, compared 9 model setups, picked the best one, tuned its threshold, and tested it once on unseen data 

**Best model**:  Random Forest (200 trees) 

**Test results**:  PR-AUC 0.8015, Recall 0.7958, Precision 0.8370, F1 0.8159 

**Compared against**: Logistic Regression: PR-AUC 0.7040, Recall 0.7394, Precision 0.7895, F1 0.7636 

---

## Why This Problem Is Hard

Fraud is very rare. Only about 1 in 600 transactions is fraud. A model that says "everything is fine" every time would be about 99.8% accurate, yet it would catch zero fraud. So accuracy is not useful here.

Two kinds of mistakes matter:

- **Missing a fraud** costs the bank the money, plus chargebacks and lost customer trust.
- **A false alarm** wastes an analyst's time and may block a real customer.

A good model has to balance both. So I focused on **recall** (how much fraud we catch), **precision** (how many alerts are real), **F1**, and **PR-AUC**. I used **PR-AUC as the main score for choosing a model**, because it is not fooled by the huge number of normal transactions.

---

## The Data

Source: [Credit Card Fraud Detection on Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)

Original transactions was 284,807
Columns: V1 to V28 (anonymized numbers), Time, Amount, and Class (1 = fraud) 
Missing values: None
Duplicates removed: 1,081 records
Rows after cleaning: 283,726 
Fraud cases: 473 (0.167%) 
Train / test split:  198,608 / 85,118 (70/30, same fraud rate in both) 

---

## How I Kept the Results 

Most of the work here was making sure the results can be trusted.

- **Removed duplicates before splitting**, so the same row can't appear in both train and test.
- **Split the data first**, and kept the test set untouched until the very end.
- **Put scaling and resampling inside one pipeline**, so they only ever learn from training data.
- **Chose the model using PR-AUC**, not accuracy.
- **Treated small differences as ties.** If two models differed by less than the natural wobble between CV folds, I did not call one the winner.
- **Tuned the threshold on training data only** (using out-of-fold predictions), never on the test set.

---

## What I Found in the Data

- **Very unbalanced:** 99.83% normal vs 0.17% fraud.
- **Amounts:** Fraud has a higher average amount (123.87 vs 88.41) but a lower median (9.82 vs 22.00). A few big frauds pull the average up, so the median tells the fairer story.
- **Time of day:** The fraud rate changes a lot by hour, from 0.05% to 1.45%. But `Time` only counts seconds since the first transaction, so it is not a real clock time. I used this for exploring only and did not use the hour as a model feature.

---

## Models I Tested

I tried 3 models, each in 3 ways, for 9 setups in total.

**Models:**
- Logistic Regression (simple baseline)
- Decision Tree (max depth 8)
- Random Forest (200 trees)

**Ways of handling the imbalance:**
- Baseline (no changes)
- Class weights (class_weight="balanced")
- SMOTE plus random undersampling

I compared them with 5-fold cross-validation on the training set.

### Cross-validation results

Recall, precision and F1 use the default 0.5 threshold.

<img width="1366" height="768" alt="Screenshot (17)" src="https://github.com/user-attachments/assets/3eea30da-bdcb-4784-993e-3ccacb052e84" />

### What this told me

1. **Resampling and class weights did not make the models better at ranking fraud.** For Random Forest, the baseline and class-weighted versions were basically tied (0.8435 vs 0.8394). For Logistic Regression, all three were nearly the same.
2. **They mostly just moved the cutoff.** For Logistic Regression, recall jumped to about 0.9 but precision dropped to 0.06 to 0.10. That means it flagged far more transactions, not that it got smarter. So I handled this trade-off with threshold tuning instead.
3. **They made the Decision Tree worse.** Its PR-AUC fell from 0.75 to 0.51 and 0.46.

I picked **Random Forest (baseline)**. Its class-weighted version was a statistical tie, so this choice doesn't depend on that setting.

---

## Picking the Threshold

The threshold decides how confident the model must be before it flags a transaction. Using out-of-fold training predictions, I looked for the cutoff with the **highest recall** that still kept **both precision and recall at 0.80 or better**. If no cutoff met both goals, I used the best F1 score instead.

- **Random Forest** met both goals at a threshold of **0.170**.
- **Logistic Regression** could not, so it used its best-F1 threshold of 0.079.

The 0.80 targets are only a stand-in. In a real bank, the threshold should be set from the actual cost of a missed fraud compared with a false alarm.

---

## Final Test Results

The threshold was fixed before touching the test set, and the test set was used once.

<img width="1365" height="592" alt="Screenshot (16)" src="https://github.com/user-attachments/assets/5d271cde-761b-4354-9414-b0e035b781fd" />


Random Forest wins on PR-AUC, recall, precision and F1. Logistic Regression has the higher ROC-AUC, but ROC-AUC is inflated by the huge number of normal transactions. That is why PR-AUC was the better score to decide with.

**A note on certainty:** The test set has only 142 frauds, so each fraud changes recall by about 0.7 points. The PR-AUC dropped from 0.84 in cross-validation to 0.80 on the test set, which is about what the fold-to-fold wobble (around 0.03) would lead you to expect.

---

## Key Takeaways

- **Accuracy would have hidden the problem.** A model that catches nothing still scores about 99.8%.
- **Choosing the right model mattered more than imbalance tricks.** Moving to Random Forest helped far more than SMOTE or class weights.
- **Reweighting and SMOTE shifted the cutoff, not the skill.** So I treated the threshold as its own decision.
- **Both models agree on some important features.** `V14` and `V10` rank high in both. Random Forest's top features were `V17`, `V12`, `V14`, `V16` and `V10`. The features are anonymized, so I don't claim to know what they mean in real life.

---

## What This Means in Practice

- **Analyst workload is easy to estimate.** About 84% of flagged transactions were real fraud, with 22 false alarms across about 85,000 test transactions.
- **The threshold is a business dial.** Lowering it catches more fraud but creates more false alarms.
- **Ideas for scaling up (a sketch, not tested in this project).** At around 1 million transactions per hour, even a small false alarm rate creates lots of alerts. I outlined a tiered response (auto-block, extra verification, manual review), training on all fraud plus a sample of normal transactions, and watching for drift using time-based validation instead of random splits.

---

## Tools Used

Python, pandas, NumPy, scikit-learn, imbalanced-learn, matplotlib, seaborn, Jupyter Notebook

## Project Structure

```
DataAnalytics-L2-FraudDetection/
├── README.md
├── FraudDetection.ipynb
├── Screenshots

```

## How to Run

1. Download `creditcard.csv` from the [Kaggle dataset](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud).
2. Put it one folder above the notebook (`../creditcard.csv`), or change `DATA_PATH` in the settings cell to where your file is.
3. Install the libraries:

```bash
pip install numpy pandas matplotlib seaborn scikit-learn imbalanced-learn jupyter
```

4. Open `FraudDetection.ipynb` and run all cells.

I used `RANDOM_STATE = 42` everywhere (the split, the CV folds and the models), so you should get the same results.
