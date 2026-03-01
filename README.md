# SABR Calibration & Option Pricing (Yahoo Finance / yfinance)

A compact, notebook-first project to:
1) download an option chain from Yahoo Finance with `yfinance`,
2) compute **market implied vols** from bid/ask mid prices,
3) build a clean **OTM volatility smile** (puts below forward, calls above),
4) calibrate **SABR** (Hagan 2002 lognormal approximation),
5) price an option using **Black-76** with SABR-implied volatility,
6) plot the smile + fit + residuals.

> Focus: **one expiry** (single maturity) calibration.

---

## What this repo does

- Fetch spot and option chains from Yahoo via `yfinance`
- Compute mid prices and implied vol (Black-76)
- Filter out bad quotes (zero bid, huge spread, no liquidity)
- Construct an OTM smile
- Calibrate SABR parameters `(alpha, rho, nu)` with fixed `beta`  
  (optionally scan several `beta` values)
- Plot:
  - Market IV vs SABR fit
  - Residuals: `SABR − Market`

---

## Requirements

- Python 3.9+
- Packages:
  - `yfinance`
  - `numpy`
  - `pandas`
  - `scipy`
  - `matplotlib`