# GARCH Volatility Modelling Across Global Equity Indices

A Python-based quantitative finance project investigating volatility dynamics and volatility persistence across four major global equity indices using GARCH(1,1) models.

## Project Overview

This project estimates and compares conditional volatility in the **S&P 500**, **EURO STOXX 50**, **Nikkei 225**, and **FTSE 100** over **January 2015–December 2025**. It examines historical return behaviour, volatility clustering, persistence of volatility shocks, and one-step-ahead volatility forecasts.

The primary research question is: **How does volatility persistence differ across major global equity markets?**

## Data

Daily index price data are downloaded from Yahoo Finance using `yfinance`.

| Equity index | Market | Yahoo Finance ticker |
|---|---|---|
| S&P 500 | United States | `^GSPC` |
| EURO STOXX 50 | Euro area | `^STOXX50E` |
| Nikkei 225 | Japan | `^N225` |
| FTSE 100 | United Kingdom | `^FTSE` |

Each index's daily logarithmic returns are calculated from its own available trading dates to avoid introducing artificial returns when markets observe different holidays:

\[
r_t = 100\ln\left(\frac{P_t}{P_{t-1}}\right)
\]

Returns are expressed in percentage units for model estimation.

## Methodology

1. **Data preparation:** Download historical prices, inspect missing values and duplicate dates, and extract closing prices.
2. **Exploratory data analysis:** Visualise index prices, daily log returns, 30-observation rolling volatility, and return distributions.
3. **GARCH estimation:** Fit a constant-mean GARCH(1,1) model with normally distributed standardised innovations separately to each index, using maximum likelihood estimation.
4. **Persistence comparison:** Calculate the sum of ARCH and GARCH coefficients, \(\alpha+\beta\).
5. **Model evaluation:** Inspect raw and standardised residuals, report AIC/BIC, and generate one-step-ahead conditional volatility forecasts.

The model specification is:

\[
r_t=\mu+\epsilon_t, \qquad
\sigma_t^2=\omega+\alpha\epsilon_{t-1}^2+\beta\sigma_{t-1}^2.
\]

## Results

The following values are taken from the saved outputs in `notebooks/garch_analysis.ipynb` (rounded to four decimal places).

| Index | Alpha | Beta | Persistence (α + β) | Next-period forecast volatility (%) |
|---|---:|---:|---:|---:|
| S&P 500 | 0.1748 | 0.7938 | **0.9685** | 0.6761 |
| EURO STOXX 50 | 0.1540 | 0.8046 | 0.9586 | 0.7304 |
| Nikkei 225 | 0.1562 | 0.7733 | 0.9294 | **0.9288** |
| FTSE 100 | 0.1706 | 0.7589 | 0.9296 | 0.6226 |

All four fits reported an optimiser convergence flag of `0`.

**Key findings:**

- The **S&P 500** has the highest estimated volatility persistence (0.9685), suggesting slower expected decay of volatility shocks under the fitted model.
- The **Nikkei 225** has the highest next-period forecast daily volatility (0.9288%).
- Higher persistence does **not** necessarily imply a higher current forecast volatility level.
- The FTSE 100 and Nikkei 225 persistence estimates are very close; no statistical significance claim is made about their difference.

AIC and BIC are also reported in the notebook. Since the models use different return series and sample sizes, these criteria are **not** used to rank model quality across markets.

## Technology

- **Python**
- `pandas`, `numpy` — data processing and numerical calculations
- `yfinance` — historical index prices
- `matplotlib`, `seaborn` — visualisation
- `arch` — GARCH model estimation and forecasting
- `statsmodels`, `scipy` — supporting statistical tools
- **Jupyter Notebook / VS Code** — interactive analysis

## Project Structure

```text
garch_project/
├── data/                 # Raw and prepared market datasets (if saved locally)
├── notebooks/
│   └── garch_analysis.ipynb
├── src/                  # Optional reusable Python scripts
├── outputs/              # Optional exported figures and tables
├── reports/              # Optional written reports
├── requirements.txt      # Python dependencies
└── README.md
```

## Getting Started

Clone or download the project, open its root directory in VS Code, and create a virtual environment.

**Windows PowerShell:**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Select the `.venv` Python kernel in VS Code and run `notebooks/garch_analysis.ipynb` from top to bottom. An internet connection is required when downloading prices with `yfinance`. If the notebook is run at a later date, results may differ if the upstream data are revised or dependencies change.

## Limitations and Future Work

- Results depend on the **2015–2025** sample, the constant-mean GARCH(1,1) specification, and the normal-innovation assumption.
- Reported one-step-ahead forecasts are generated at the end of the historical sample; they are **not** live forecasts or validated out-of-sample performance estimates.
- Residual plots provide visual diagnostics, but do not by themselves establish the absence of serial correlation or remaining volatility effects.
- Statistical uncertainty in cross-market persistence differences has not been quantified.
- Potential extensions include conditional volatility plots, Student-t innovation comparisons, residual autocorrelation tests, and out-of-sample forecast evaluation.

## Data Source

Historical market data: [Yahoo Finance](https://finance.yahoo.com/), accessed through the [`yfinance`](https://github.com/ranaroussi/yfinance) Python package.

## Disclaimer

This project is for educational and research purposes only and does not constitute investment advice.
