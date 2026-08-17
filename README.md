# Credit Risk Assessment Framework (Version 2)

A credit risk assessment framework for publicly listed companies that combines accounting-based and market-based methodologies to evaluate financial health and estimate default risk.

The framework extracts company financial statement and market data from Yahoo Finance (`yfinance`), calculates key credit risk ratios, computes Altman Z-Scores, applies a modified Merton Structural Model, and evaluates data completeness to generate transparent credit risk assessments.

The framework estimates:

- Altman Z-Score
- Distance to Default (DD)
- Probability of Default (PD)
- Accounting-Based Risk Assessment
- Market-Based Risk Assessment
- Overall Credit Risk Indicator
- Data Completeness Assessment

The framework combines accounting and market signals to provide interpretable risk assessments and commentary for analysts.

---

# Objective

The objective of this project is to estimate the credit risk of publicly listed companies using publicly available financial statement and market data.

The framework is designed to support:

- Credit analysis
- Counterparty risk assessment
- Trade receivables monitoring
- Credit limit reviews
- Financial due diligence
- Educational and research purposes

The approach combines accounting-based and market-based credit indicators to provide a more complete view of a company's financial risk profile.

---

# Version History

## Version 2.0

Major enhancements introduced in Version 2:

- Automated company-year panel construction
- Historical market capitalization reconstruction using year-end pricing
- Enhanced Merton Structural Model implementation
- Merton solver diagnostics and validation checks
- Data completeness assessment framework
- Observation-level credit commentary generation
- Latest Company Credit Risk Report generation
- Preservation of partial assessments when one model is available but the other is not
- Improved report ordering and output management
- Expanded validation and quality assurance controls

## Version 1.0

The original version focused on calculating accounting-based and market-based credit risk metrics and generating company-level credit risk assessments.

---

# Methodology

The framework combines two complementary approaches.

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

### Altman Assessment Framework

| Z-Score | Assessment |
|----------|----------|
| >= 2.99 | Strong |
| 1.81 - 2.99 | Moderate |
| < 1.81 | Weak |

---

## 2. Market-Based Assessment

A modified Merton Structural Model is used to estimate market-implied default risk.

### Inputs

- Historical Market Capitalization
- Total Debt
- Equity Volatility
- Risk-Free Rate
- One-Year Time Horizon

### Historical Market Capitalization

Historical Market Capitalization is reconstructed using:

Market Capitalization = Year-End Share Price × Shares Outstanding

Year-end share prices are obtained from Yahoo Finance historical price data.

### Equity Volatility

Equity volatility is estimated from daily stock returns and annualized using:

σE = σDaily × √252

where 252 represents the approximate number of trading days in a year.

### Merton Model Outputs

The model estimates:

- Asset Value (VA)
- Asset Volatility (σA)
- Distance to Default (DD)
- Probability of Default (PD)

Distance to Default is calculated as:

DD = [ln(VA/K) + (r − 0.5σA²)T] / (σA√T)

Probability of Default is calculated as:

PD = N(-DD)

where N() is the cumulative standard normal distribution.

### Merton Assessment Framework

| Probability of Default | Assessment |
|----------|----------|
| < 0.10% | Strong |
| 0.10% - < 1.00% | Moderate |
| >= 1.00% | Weak |

---

# Validation and Quality Assurance Pipeline

Before any credit risk calculations are performed, the framework validates the availability and quality of required inputs.

The workflow follows:

1. Data Extraction
2. Input Validation
3. Accounting Metric Calculation
4. Altman Z-Score Estimation
5. Merton Structural Model Estimation
6. Data Completeness Assessment
7. Credit Risk Interpretation
8. Report Generation

This approach prevents invalid observations from producing misleading risk estimates while preserving transparency regarding missing or incomplete data.

---

# Solver Diagnostics

The framework records solver diagnostics for every company-year observation, including:

- Solver convergence status
- Solver return codes
- Solver messages
- Failed estimation reasons

This allows analysts to distinguish between:

- Successful model estimations
- Missing data
- Numerical convergence issues
- Invalid model solutions

Observations are retained in the final report even when model estimation is unsuccessful.

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

### Partial Assessment

Examples:

- Altman: Insufficient Data, Merton: Strong
- Altman: Moderate, Merton: Insufficient Data

In these situations the framework preserves any available model results rather than discarding useful information.

### Insufficient Data

- Both Altman and Merton assessments are unavailable due to missing inputs.

---

# Data Quality Controls

The framework includes several validation checks.

### Input Validation

- Missing key variables
- Missing Altman model inputs
- Missing Merton model inputs
- Negative equity
- Negative EBIT
- Negative operating cash flow
- Zero debt observations

### Model Validation

- Merton solver convergence checks
- Solver return code tracking
- Solver message logging
- Failed estimation tracing
- Asset value sanity checks
- Asset volatility sanity checks
- Distance to Default validation

### Data Completeness Assessment

Each observation is classified as:

| Status | Description |
|----------|----------|
| Complete | All required inputs available and model successfully estimated |
| Partial | Model estimated but warning indicators exist |
| Insufficient | Missing data prevented a complete assessment |

---

# Outputs

The framework generates two reports.

## 1. Credit Risk Report

Contains all available company-year observations.

The report preserves observations even when model estimation is incomplete, allowing analysts to review validation issues, missing data, and partial assessments.

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

asset_value
asset_volatility

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

Returns the latest usable observation for each company.

The report attempts to select the most recent company-year with sufficient accounting and market-based information available.

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
│
├── csv_documents/
│   ├── income_statements/
│   ├── balance_sheets/
│   ├── cashflows/
│   ├── company_info/
│   └── price_history/
│
├── outputs/
│   ├── credit_risk_report.csv
│   └── latest_company_credit_risk_report.csv
│
└── logs/
```

---

# Model Limitations

This framework is designed as a practical credit risk screening tool and includes several simplifying assumptions.

- Yahoo Finance data quality depends on the availability and accuracy of publicly reported information.
- Historical market capitalization is estimated using year-end share price multiplied by shares outstanding.
- Current shares outstanding are used as a proxy where historical share count information is unavailable.
- The Merton model assumes a one-year forecast horizon.
- The risk-free rate is based on the annual average US 10-Year Treasury yield.
- Altman Z-Score thresholds were originally developed for publicly traded manufacturing firms and may be less predictive for certain industries such as financial institutions.
- Market-based estimates can be sensitive to changes in share prices and volatility.
- The framework does not represent an official credit rating methodology.

---

# Future Development

Potential future enhancements include:

- Sector-level credit risk framework
- Sector-level Merton Structural Models
- Market-cap-weighted sector default probability estimation
- Industry-level benchmarking
- Power BI credit risk dashboards
- Historical sector trend analysis
- Sector credit risk heatmaps
- Cross-sector risk comparison reporting

---

# Important Notes

- This project is intended for educational, analytical, and research purposes.
- Outputs should be used as decision-support information and not as standalone credit ratings.
- Credit assessments should always be supplemented with additional qualitative review and professional judgement.
- Financial statement quality and availability may affect output reliability.

---

# Disclaimer

This repository contains an anonymized implementation of a credit risk assessment framework.

Company identifiers, file paths, proprietary datasets, client coverage lists, and organization-specific information have been removed.

Users should provide their own datasets and perform independent validation before using results for business decisions.
