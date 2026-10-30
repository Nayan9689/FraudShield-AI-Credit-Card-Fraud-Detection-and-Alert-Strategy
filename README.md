# FraudShield AI: Credit Card Fraud Detection and Alert Strategy

**Business question:** Which transaction patterns consistently signal fraudulent behaviour, and what triggers should be set in a real-time fraud alert system?

**Tools:** Python (pandas, numpy, scikit-learn), Power BI, Google Colab

---

## Dashboard (3 pages)

| Page | What it shows |
|---|---|
| Fraud Intelligence Overview | Transactions, fraud cases, fraud rate, model KPIs, class imbalance |
| Behavioral Patterns and Anomalies | Fraud rate by amount band and by elapsed time, feature importance |
| Risk Monitoring and Alert Strategy | Threshold trade-offs, confusion matrix, recommended alert tiers |

[Add screenshots here]

---

## Data

- **Source:** Kaggle Credit Card Fraud Detection dataset (284,807 transactions over two days, 492 frauds).
- **Cleaning:** 1,081 exact duplicate rows removed, leaving **283,726 transactions and 473 frauds (0.17%)**.
- **Features:** V1 to V28 are anonymized PCA components, plus Time (seconds since the first transaction) and Amount.
- The dataset contains **no card ID, merchant or location**.

## Method

1. Audited the data: missing values, duplicates, class balance.
2. EDA on transaction amount, elapsed time, time gaps and feature distributions.
3. Stratified 80/20 train/test split. The training set was split again into fit and validation sets.
4. Compared Logistic Regression and Random Forest on the validation set.
5. Chose the alert threshold on **validation data**, then evaluated once on the **held-out test set**.
6. Exported summary tables to Power BI.

## Key findings

**Amount**
- Half of all fraud (238 of 473) is under $10. Median fraud amount is $9.82 against $22.00 for legitimate transactions.
- The highest fraud rate is the $500-1000 band at about 0.39%, more than double the 0.17% average.

**Time**
- The 0-6 h and 24-30 h windows after dataset start hold 8.4% of transactions but 24% of fraud (about 0.48% fraud rate).
- These are elapsed hours, not time of day.

**Features**
- V14, V10, V4, V12 and V17 are the most important features in the Random Forest. They are anonymized, so they cannot be interpreted as real-world fields.

**Model (Random Forest, threshold 0.3, held-out test set of 56,746 transactions with 95 frauds)**

| Metric | Value |
|---|---|
| Fraud caught | 71 |
| Fraud missed | 24 |
| False alerts | 6 |
| Precision | 92.2% |
| Recall | 74.7% |
| F1 | 0.826 |
| ROC-AUC | 0.943 |

Logistic Regression at similar recall produced about 1,400 to 3,000 false alerts, so Random Forest is far more usable for a review team.

## Recommended alert strategy

| Model score | Action | Validation evidence (Random Forest) |
|---|---|---|
| 0.8 or higher | Block and call the customer | 60 frauds caught, 2 false alerts, 96.8% precision |
| 0.3 to 0.8 | Step-up authentication or manual review | At 0.3: 81 caught, 9 false alerts, 90% precision, 86% recall |
| 0.1 to 0.3 | Monitor silently | At 0.1: 83 caught, 20 false alerts |
| Below 0.1 | Allow | |

Context signals (early elapsed-time windows, the $500-1000 band, amounts under $10, extreme V14/V17 values) raise review priority. None should trigger an alert on its own: each has a fraud rate of roughly 0.5% or lower, so most of its alerts would be false.

## Limitations

- Features are anonymized, so rules cannot be expressed in business terms.
- There are only 95 frauds in the test set. Test recall (74.7%) is lower than validation recall (86.2%), which shows how much the estimate can vary.
- Time is elapsed seconds over two days, not clock time, and there is no card ID, so per-card velocity cannot be measured.
- Geographic anomalies cannot be analysed because the dataset has no location data.
- The simulated scenario tables in the notebook are illustrative only and are not used for any conclusion in the dashboard.


## Author

Nayan Vijay Kothari | www.linkedin.com/in/nayan-kothari- | https://github.com/Nayan9689
