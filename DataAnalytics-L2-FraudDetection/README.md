# Credit Card Fraud Detection

Fraud makes up just 0.17% of the transactions in this dataset, which means a model can look excellent while catching nothing. This project is about building a detector that holds up under that imbalance and being able to trust its numbers.

The final Random Forest caught **113 of the 142 frauds** in the test set (79.6% recall). About **84% of the transactions it flagged were genuine fraud**, with only **22 false alarms** across roughly 85,000 transactions. I compared nine model setups, chose between them using PR-AUC, tuned the decision threshold on training data only, and used the test set once.

## The Problem

Only about 1 in 600 transactions is fraud. A model that calls everything legitimate would be about 99.8% accurate and catch no fraud at all, so accuracy is useless as a yardstick here.

The errors also carry different costs. A missed fraud costs the bank the money, plus chargebacks and customer trust. A false alarm wastes an analyst's time and can block a real customer. The model has to balance the two.

That shaped how I measured everything. I tracked recall (how much fraud is caught), precision (how many alerts are real), F1 and PR-AUC, and used PR-AUC as the main score for choosing a model because it is not flattered by the mass of normal transactions.

---

## Why This Problem Is Hard

Fraud is very rare. Only about 1 in 600 transactions is fraud. A model that says "everything is fine" every time would be about 99.8% accurate, yet it would catch zero fraud. So accuracy is not useful here.

Two kinds of mistakes matter:

- **Missing a fraud** costs the bank the money, plus chargebacks and lost customer trust.
- **A false alarm** wastes an analyst's time and may block a real customer.

A good model has to balance both. So I focused on **recall** (how much fraud we catch), **precision** (how many alerts are real), **F1**, and **PR-AUC**. I used **PR-AUC as the main score for choosing a model**, because it is not fooled by the huge number of normal transactions.

---

## The Data

The data comes from the [Credit Card Fraud Detection dataset on Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud): 284,807 transactions with 28 anonymized features (`V1` to `V28`), plus `Time`, `Amount` and the `Class` label (1 means fraud). There were no missing values, but 1,081 duplicate records, which I removed. That leaves 283,726 transactions, 473 of them fraud (0.167%).

The split is 70/30, giving 198,608 training rows and 85,118 test rows, with the same fraud rate in both.

## How I Kept the Results Trustworthy

Most of the effort went into making sure the numbers can be believed.

- Duplicates were removed before splitting, so no row can appear in both train and test.
- The test set stayed untouched until the final evaluation.
- Scaling and resampling sit inside a single pipeline, so they only ever learn from training data.
- Models were chosen on PR-AUC, not accuracy.
- Differences smaller than the natural wobble between CV folds were treated as ties, not wins.
- The threshold was tuned on out-of-fold training predictions, never on the test set.

## What the Data Showed

The class imbalance is stark: 99.83% normal against 0.17% fraud.

Fraud has a higher average amount than normal transactions (123.87 against 88.41) but a lower median (9.82 against 22.00). A few large frauds drag the average up, so the median is the fairer comparison.

The fraud rate also varies a lot by hour, from 0.05% to 1.45%. However, `Time` only counts seconds since the first transaction and is not a real clock time, so I used it for exploration only and kept the hour out of the model.


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

Three findings drove the next decisions:

1. **Resampling and class weights did not improve the ranking of fraud.** For Random Forest, the baseline and class-weighted versions were effectively tied on PR-AUC (0.8435 against 0.8394), and Logistic Regression's three versions were nearly identical.
2. **What they did was shift the cutoff.** Logistic Regression's recall rose to about 0.9, but precision fell to between 0.06 and 0.10. It was flagging far more transactions, not discriminating better. That made the threshold a separate decision to tune directly.
3. **They hurt the Decision Tree.** Its PR-AUC fell from 0.75 to 0.51 and 0.46.

I went with the baseline Random Forest. Its class-weighted twin was a statistical tie, so the choice does not hinge on that setting.

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

1. Clone the repo and open the project folder:
```bash
   git clone https://github.com/CodeWithNafisat/OIBSIP.git
   cd OIBSIP/DataAnalytics-L2-FraudDetection
```
2. Download `creditcard.csv` from [Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) and put it one folder above the notebook (or change `DATA_PATH` in the settings cell).
3. Install the dependencies:
```bash
   pip install numpy pandas matplotlib seaborn scikit-learn imbalanced-learn jupyter
```
4. Open `FraudDetection.ipynb` and run all cells.

All randomness uses `RANDOM_STATE = 42`, so results should match.

---
