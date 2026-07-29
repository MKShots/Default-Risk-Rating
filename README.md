# Default Risk Rating Framework

A credit risk assessment framework for publicly listed companies that combines accounting-based and market-based approaches to estimate default risk.

The project extracts company financial statements and market data from Yahoo Finance (`yfinance`), calculates key credit risk ratios, computes Altman Z-Scores, and applies a modified Merton Structural Model to estimate:

- Distance to Default (DD)
- Probability of Default (PD)

The framework then combines accounting and market signals to provide interpretable risk assessments and commentary for analysts.

---

# Objective

The objective of this project is to estimate the default risk of publicly listed companies using publicly available financial statement and market data.

The framework is designed to support:

- Credit analysis
- Counterparty risk assessment
- Trade receivables monitoring
- Credit limit reviews
- Financial due diligence
- Educational and research purposes

---

# Methodology

The framework combines two complementary approaches:

## 1. Accounting-Based Assessment

Financial statement data is downloaded from Yahoo Finance and used to calculate:

### Liquidity Ratios

- Current Ratio

### Leverage Ratios

- Debt-to-Equity Ratio
- Cash Flow-to-Debt Ratio

### Coverage Ratios

- Interest Coverage Ratio

### Altman Z-Score

The Altman Z-Score is calculated using:

Z = 1.2X₁ + 1.4X₂ + 3.3X₃ + 0.6X₄ + 1.0X₅

Where:

- X₁ = Working Capital / Total Assets
- X₂ = Retained Earnings / Total Assets
- X₃ = EBIT / Total Assets
- X₄ = Market Value of Equity / Total Liabilities
- X₅ = Revenue / Total Assets

Altman Z-Score assessments:

| Z-Score | Assessment |
|----------|----------|
| >= 2.99 | Strong |
| 1.81 - 2.99 | Moderate |
| < 1.81 | Weak |

---

## 2. Market-Based Assessment

A modified Merton Structural Model is used to estimate default risk.

Inputs include:

- Historical Market Capitalization
- Total Debt
- Equity Volatility
- Risk-Free Rate

Historical Market Capitalization is reconstructed as:

Market Capitalization = Share Price × Shares Outstanding

The model estimates:

- Asset Value
- Asset Volatility
- Distance to Default (DD)
- Probability of Default (PD)

The system also records solver diagnostics to verify whether numerical optimization converged successfully.

---

# Combined Risk Interpretation

Accounting and market indicators are evaluated together.

### Low Risk

- Altman Assessment is not Weak
- Merton Assessment is not Weak

### High Risk

- Altman Assessment is Weak
- Merton Assessment is Weak

### Mixed Signals

- Accounting and market indicators disagree

### Insufficient Data

- Required inputs are unavailable

---

# Data Quality Controls

The framework includes several validation checks:

### Input Validation

- Missing key variables
- Negative equity
- Negative EBIT
- Negative operating cash flow
- Zero debt observations

### Model Validation

- Merton solver convergence checks
- Asset value sanity checks
- Asset volatility sanity checks

### Data Completeness Assessment

Each observation is classified as:

| Status | Description |
|----------|----------|
| Complete | All inputs available and model successfully estimated |
| Partial | Model estimated but warning flags exist |
| Insufficient | Missing data prevented a complete assessment |

---

# Outputs

The framework generates two reports.

## 1. Credit Risk Report

Contains all company-year observations.

Example fields:

```text
ticker
company_name
fiscal_year

flag_negative_equity
flag_negative_ebit
flag_negative_ocf
flag_zero_debt
flag_missing_key_data

data_completeness
data_completeness_reason

current_ratio
debt_to_equity
cashflow_to_debt
interest_coverage

altman_z_score
altman_assessment

historical_market_cap

distance_to_default
probability_of_default
probability_of_default_pct_display

merton_solver_ier
merton_solver_status
merton_solver_message

merton_assessment
overall_indicator
commentary
```

---

## 2. Latest Company Credit Risk Report

Returns only the latest usable observation for each company.

This report is intended to provide a current snapshot of each company's credit profile.

---

# Technology Stack

- R
- Quarto
- Python (via Reticulate)
- Pandas
- NumPy
- SciPy
- yFinance

---

# Project Structure

```text
project/

├── credit_risk_model.qmd
├── README.md
├── outputs/
│   ├── credit_risk_report.csv
│   └── latest_company_credit_risk_report.csv
└── data/
```

---

# Important Notes

- This project is intended for educational, analytical, and research purposes.
- Outputs should be used as decision-support information and not as standalone credit ratings.
- Market-based measures are sensitive to changes in stock prices and volatility.
- Financial statement quality and availability may affect output reliability.

---

# Disclaimer

This repository contains an anonymized implementation of a credit risk assessment framework. Company identifiers, file paths, and proprietary information have been removed.

Users should provide their own datasets and perform independent validation before using results for business decisions.
