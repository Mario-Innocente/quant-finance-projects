# Quantitative Risk Engine: Volatility Forecasting & VaR Backtesting

**Author:** Mario Innocente | MIM Candidate, INSEAD  
**Focus:** Market Risk, Econometrics (GARCH), Backtesting, Python


---
[📄 **Download Full Research Report (PDF)**](../../reports/Vol_Forecast_Var_Bactest%20Project.pdf)

## 🚀 Executive Summary
This project implements an end-to-end framework for **Market Risk estimation** using Python. The goal is to move beyond simple historical volatility and build a robust model capable of capturing the stylized facts of financial markets: **volatility clustering** and **fat tails** (extreme events).

The engine processes historical data for the **S&P 500** (`SPY`) to compare two distinct risk methodologies:
1.  **Baseline:** EWMA (RiskMetrics) – Fast reaction, no parameters.
2.  **Challenger:** GARCH(1,1) with Student-t – Mean reverting, heavy-tailed estimation.
3.  **Validation:** Rigorous statistical Backtesting (Kupiec & Christoffersen tests).

---

## 📊 Key Visualizations
*(Note: Run the notebook to generate the interactive plots)*

The analysis generates two critical insights:
* **Volatility Regimes:** Visualizing how GARCH adapts to market stress (e.g., COVID-19 crash) compared to the smoother EWMA.
* **VaR Violations:** A "Hit Sequence" plot showing exactly when the market return breached the predicted risk threshold.



> **Note:** See the `images/` folder or run the notebook to view the `Volatility_Regime_Analysis.png`.

---

## 🛠 Methodology

### 1. Volatility Modeling
We model the time-varying variance $\sigma_t^2$ using two approaches:

* **EWMA (RiskMetrics):** Industry standard with decay factor $\lambda=0.94$.
    $$\sigma_t^2 = \lambda \sigma_{t-1}^2 + (1-\lambda) r_{t-1}^2$$
* **GARCH(1,1) Student-t:** Captures mean reversion and leptokurtosis.
    $$\sigma_t^2 = \omega + \alpha r_{t-1}^2 + \beta \sigma_{t-1}^2$$
    *Design Note:* Parameters are estimated assuming **Student-t innovations** to make the model robust against outliers during the fitting process.

### 2. Value-at-Risk (VaR) Strategy
We compute the **1-day 95% VaR**.
* **The "Clean Comparison" Approach:** While GARCH is estimated with Student-t errors, the final VaR cutoff uses the **Normal Approximation** for both models.
* **Why?** This deliberate design choice isolates the *volatility dynamics* as the sole variable of comparison, ensuring an "apples-to-apples" test of which model better anticipates market regimes.

### 3. Backtesting (The "Reality Check")
We validate the models using hypothesis testing on the violation sequence $I_t$:
* **Kupiec Test:** Checks **Unconditional Coverage**. Does the model fail 5% of the time as predicted?
* **Christoffersen Test:** Checks **Conditional Coverage**. Are violations independent? (A good model should not fail in clusters).

---
