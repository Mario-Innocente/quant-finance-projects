# Quant Finance Projects

This repository is a small, portfolio-oriented collection of quant finance notebooks. The goal is to show clean, reproducible workflows that go from raw price data to interpretable results, with enough theory and diagnostics to make the outputs defensible.

The projects are intentionally “end-to-end”: each notebook defines inputs, loads data, builds the model, produces plots/tables, and includes practical checks so the code keeps working even when data providers are unreliable.

---

## Repository structure (high level)

The repository is organized around self-contained project folders under `projects/`.  
Each project typically includes:
- a main notebook (`.ipynb`)
- a project README describing the theory, assumptions, and outputs
- optional helper code (future extension) and optional local data caching

Recommended conventions used across projects:
- prefer clear variable names (`prices`, `rets`, `vol`, `var`, `hits`)
- keep outputs interpretable (tables + plots)
- separate in-sample estimation from out-of-sample evaluation when possible
- avoid committing data caches to Git (use `.gitignore`)

---

## Project 1 — CAPM Calculator and Efficient Frontier (Markowitz)

### Why this project exists
This project demonstrates the core toolkit behind introductory asset pricing and portfolio construction. It shows that you can:
- convert price series into clean return series
- estimate systematic risk using a market model (CAPM-style regression)
- move from single-asset risk/return metrics to portfolio allocation using mean–variance optimization

These are foundational concepts in equity research, multi-asset investing, and quant portfolio construction. They also serve as a good baseline for more advanced models (multi-factor models, shrinkage estimators, constrained optimization).

### What the notebook does
- Downloads or loads adjusted close prices for selected assets and a market proxy
- Computes daily returns and (optionally) excess returns relative to a user-defined risk-free rate
- Runs a regression of asset excess returns on market excess returns to estimate:
  - alpha (intercept)
  - beta (market sensitivity)
  - goodness-of-fit (R-squared)
- Estimates expected returns and the covariance matrix
- Builds:
  - the Global Minimum Variance (GMV) portfolio
  - a tangency (max Sharpe) portfolio if a risk-free rate is provided
  - an efficient frontier by sweeping target returns and solving for minimum variance portfolios
- Produces plots and tables that summarize:
  - CAPM parameters
  - the frontier curve
  - portfolio weights and basic risk/return statistics

### How to interpret results (quick guide)
- Beta measures exposure to market moves; higher beta generally means higher systematic risk.
- Alpha is noisy and unstable across time windows; treat it as descriptive rather than persistent “skill.”
- Efficient frontier weights can become extreme if expected returns are estimated from limited data; this is a known limitation of classic mean–variance optimization.
- If weights look unrealistic, the next step is to add constraints (long-only, max weight) or use covariance shrinkage.

---

## Project 2 — Volatility Forecasting (EWMA vs GARCH) and VaR Backtesting

### Why this project exists
This project demonstrates time-series modeling and risk management workflows. Instead of focusing on “what should I hold?”, the focus is “how risky is it tomorrow?” and “does my risk model work out-of-sample?”

It connects three important ideas:
1) volatility clustering in returns  
2) volatility forecasts from standard models (EWMA and GARCH)  
3) conversion of forecasts into practical risk measures (VaR/ES), validated via formal backtests  

This is a common pipeline in risk teams, systematic trading, and model validation.

### What the notebook does
- Loads daily prices for a liquid instrument (default choice is typically a broad market ETF)
- Computes daily returns and splits the sample into train/test
- Builds an EWMA volatility series as a robust baseline benchmark
- Fits a GARCH(1,1) model (often with Student-t innovations to handle fat tails)
- Produces out-of-sample one-step-ahead volatility forecasts
- Compares EWMA vs GARCH volatility forecasts visually and with a simple loss proxy (variance vs squared returns)
- Translates volatility forecasts into one-day-ahead VaR and ES (baseline Normal formulation)
- Backtests VaR using:
  - Kupiec test (unconditional coverage): are there too many/few VaR violations?
  - Christoffersen test (conditional coverage): do violations cluster or behave independently?

### Practical reliability features
- Market data providers sometimes rate-limit requests. The notebook is designed to be resilient:
  - local caching of downloaded prices (so you do not re-download every run)
  - fallback data source when the primary provider blocks requests
- Returns are forced to a one-dimensional series to avoid common notebook failures caused by DataFrame/Series shape differences.

### How to interpret results (quick guide)
- A good VaR model should produce a violation rate close to the chosen alpha (e.g., ~5% for alpha = 0.05), within sampling error.
- If Kupiec rejects, the model is miscalibrated (too conservative or too aggressive).
- If Christoffersen rejects, violations likely cluster: the model is missing dynamics, tails, or regime behavior.
- EWMA is a strong baseline; GARCH can outperform but may be sensitive to estimation choices and distributional assumptions.

---

## Reproducibility and environment

These notebooks are intended to run inside a dedicated Python environment (for example, a conda environment named `quant`).  
For reproducibility:
- keep an environment snapshot (for example `environment.yml`) so the setup is replicable across machines
- install project-specific dependencies only inside that environment
- avoid committing local caches and editor-specific files

---

## Roadmap (planned extensions)

Potential next improvements that naturally build on this repo:
- Constrained efficient frontier (long-only, max weights) and covariance shrinkage (Ledoit–Wolf)
- Multi-factor risk models (Fama–French style) as an upgrade from single-factor CAPM
- VaR/ES under Student-t using fitted degrees of freedom (tail-consistent risk)
- Rolling re-estimation (monthly/weekly) for more realistic production-like forecasting
- Portfolio VaR for the optimized portfolios from the CAPM/Frontier project

---

## Disclaimer

This repository is for educational and research purposes only.  
Nothing here constitutes investment advice, a recommendation, or an offer to buy/sell any security.
