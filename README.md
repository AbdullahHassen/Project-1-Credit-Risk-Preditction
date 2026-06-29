# Credit Risk Default Prediction

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Wrangling-green)
![ScikitLearn](https://img.shields.io/badge/Scikit--Learn-Modeling-orange)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

## Business Problem

In consumer lending, defaulting borrowers cause significant financial losses. Banks and fintechs need accurate risk assessment tools to decide **who to lend to** and **at what interest rate**.

This project builds a **Probability of Default (PD) model** using historical LendingClub loan data. The goal is to:
- Identify key drivers of default
- Predict the likelihood a borrower will default
- Enable data-driven lending decisions to maximize portfolio returns

> **Business Impact:** A 1% improvement in default prediction accuracy can save a mid-sized lender millions of dollars annually.

---

## Dataset

**Source:** LendingClub Loan Data (2007–2018) – [Kaggle](https://www.kaggle.com/datasets/wendykan/lending-club-loan-data)
**Size:** ~1.9 million loans after cleaning

**Target Variable:** `is_bad`
- `1` = Default / Charged Off
- `0` = Fully Paid / Good Standing

**Features Used:** 20+ financial indicators including:
- Loan amount, interest rate, term
- Annual income, debt-to-income ratio (DTI)
- FICO credit score range
- Revolving credit utilization
- Number of open accounts, delinquencies, public records

---

## Methodology

### 1. Data Cleaning & Preprocessing
- Removed data leakage columns (payment history, hardship status, settlement records)
- Dropped internal/identifier columns (`id`, `member_id`, `emp_title`)
- Handled missing values (0-fill for no-event columns, median-fill for continuous variables)
- Removed active loans (`Current`, `In Grace Period`) as outcomes were unknown

### 2. Feature Engineering
Created 8 custom features to capture deeper risk signals:

| New Feature | Formula | What It Captures |
|-------------|---------|------------------|
| `fico_avg` | `(fico_low + fico_high) / 2` | Average credit score |
| `loan_to_income` | `loan_amnt / annual_inc` | Borrowing burden |
| `installment_to_income` | `installment / (annual_inc/12)` | Monthly payment burden |
| `credit_utilization` | `revol_bal / total_acc` | Credit usage stress |
| `rate_to_dti` | `int_rate / dti` | Combined risk indicator |
| `delinquency_rate` | `delinq_2yrs / total_acc` | Past payment discipline |
| `public_record_rate` | `pub_rec / total_acc` | Legal/financial trouble |
| `mortgage_rate` | `mort_acc / total_acc` | Homeownership indicator |

### 3. One-Hot Encoding
Converted categorical columns (`grade`, `sub_grade`, `home_ownership`, `verification_status`, `purpose`) into 0/1 dummy variables.

### 4. Time-Based Train/Test Split
- **Training Set:** First 80% of loans (chronologically, 2007–2016)
- **Testing Set:** Last 20% (2017–2018)
- **Why:** Prevents data leakage – we only use past data to predict future outcomes.

### 5. Scaling & Modeling
- **Scaling:** StandardScaler applied to continuous features (mean=0, std=1)
- **Model:** Logistic Regression (interpretable, industry-standard baseline)
- **Handling Imbalance:** Class weight balancing (`class_weight='balanced'`)

---

## Results

### Model Performance (Test Set)

| Metric | Value |
|--------|-------|
| **AUC-ROC** | **0.683** |
| Accuracy | 61% |
| Precision (Default) | 0.30 |
| Recall (Default) | **0.67** |
| F1-Score (Default) | 0.41 |

### Interpretation

- The model successfully identifies **67% of actual defaulters** (Recall = 0.67)
- When it predicts default, it is correct **30% of the time** (Precision = 0.30)
- The AUC-ROC of **0.683** confirms the model has meaningful predictive power

### Business Impact

Using this model, a lender can:
- Flag **67% of high-risk applicants** before issuing a loan
- Reduce default-related losses while still approving the majority of good customers
- Lower false negatives (missed defaults) at the cost of more false positives (manual reviews)

---

## Key Business Insights

> **"The model confirms that credit score and debt burden are the strongest predictors of default."**

- Borrowers with `fico_avg < 650` have **3× higher default risk** than those with `fico_avg > 750`.
- A `DTI > 30%` combined with `revol_util > 80%` signals **elevated risk**.
- Loans for **debt consolidation** actually perform better than loans for **small business** or **vacation** purposes.

**Recommendation:** The lending team should:
1. Apply stricter underwriting for applicants with `fico_avg < 660`
2. Cap `loan_to_income` at 35% for high-risk borrowers
3. Offer lower rates to incentivize debt consolidation loans

---

## Tech Stack

| Tool                 | Purpose                             |
| -------------------- | ----------------------------------- |
| **Python**           | Core language                       |
| **Pandas / NumPy**   | Data wrangling                      |
| **Scikit-learn**     | Preprocessing, modeling, evaluation |
| **Jupyter Notebook** | Development environment             |
| **Git / GitHub**     | Version control                     |


---

## How to Run This Project Locally

### 1. Clone the repository
```bash
git clone https://github.com/your-username/credit-risk-prediction.git
cd credit-risk-prediction