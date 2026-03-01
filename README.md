# SABR Option Pricing Mini-Project (yfinance)

A small Jupyter-friendly project that downloads option chain data via **yfinance**, computes **market implied volatilities**, calibrates a **SABR (Hagan) model** for a single expiry, and prices options using **Black-76** with SABR-implied vols.

> Goal: build a simple, reproducible workflow for **vol smile extraction → SABR calibration → pricing**.

---

## Features

- Download spot and option chains from Yahoo Finance using `yfinance`
- Build an **OTM smile** (puts for strikes below spot/forward, calls above)
- Compute **implied vol from mid prices** (bid/ask midpoint)
- Calibrate SABR parameters (typically with fixed `beta`)
- Plot:
  - Market IV vs SABR fit
  - SABR − Market error by strike
- Price a target option using **Black-76** with SABR IV

---

## Project Structure (Suggested)


.
├── notebook.ipynb
├── README.md
└── requirements.txt


---

## Requirements

- Python 3.9+ recommended
- Packages:
  - `yfinance`
  - `numpy`
  - `pandas`
  - `scipy`
  - `matplotlib`

Install:

```bash
pip install yfinance numpy pandas scipy matplotlib
Quick Start
1) Choose a ticker and expiry

In the notebook, set:

ticker = "AAPL"          # any Yahoo Finance equity/ETF ticker
expiry = expiries[0]     # pick one expiry from yf.Ticker(ticker).options
2) Download data
import yfinance as yf
t = yf.Ticker(ticker)

spot = float(t.history(period="1d")["Close"].iloc[-1])
chain = t.option_chain(expiry)

calls = chain.calls
puts  = chain.puts
3) Build market implied vols

Mid price = (bid + ask)/2

Convert mid price → implied vol using Black-76 (forward-based)

For a simple setup, you can approximate F ≈ spot and df ≈ 1.0.
For more realism, add rate r and dividend yield q.

4) Construct an OTM smile

Best practice for equity options:

Use puts for strikes K < F

Use calls for strikes K > F

Apply filters to remove noisy quotes:

bid > 0, ask > 0

ask >= bid

limit relative spread (e.g., spread/ask < 0.35)

discard extreme IV outliers

5) Calibrate SABR (Hagan lognormal)

We use the Hagan approximation to map SABR parameters to Black implied vol:

Parameters:

alpha: level (roughly ATM vol scale)

beta: elasticity (often fixed: 0.5–1.0)

rho: skew/asymmetry

nu: vol-of-vol (curvature)

Typically fix beta and calibrate (alpha, rho, nu) by minimizing weighted squared error:

Optionally use vega weights to emphasize ATM strikes.

6) Plot results

Market IV (scatter)

SABR fit (line)

Spot (vertical dashed line)

Error plot: iv_sabr - iv_market

7) Price an option

Compute SABR IV at the target strike and price with Black-76:

iv = sabr_hagan_lognormal(F, K, T, alpha, beta, rho, nu)
price = black76_price(F, K, T, iv, df=df, is_call=True)
Notes & Best Practices
Data quality matters

Yahoo Finance quotes can be noisy, especially far OTM or illiquid strikes.
If your SABR fit becomes nearly linear or unstable, try:

stricter spread filters

require openInterest or volume thresholds

restrict to a reasonable moneyness band (e.g., |ln(K/F)| < 0.25–0.35)

build smile from OTM instruments only

Beta selection

Equity smiles often fit better with beta < 1.
Try scanning:

beta ∈ {0.5, 0.6, 0.7, 0.8, 0.9, 1.0}

Pick the beta that minimizes your calibration loss.

Hagan approximation

Hagan’s SABR implied vol is an approximation; errors increase for:

very short maturities

extreme strikes (deep OTM)

very large nu

Example Outputs

IV smile with SABR fit curve

Error plot showing systematic under/over-fit regions

Calibrated parameters (alpha, beta, rho, nu)

SABR-priced option value vs market mid price (when available)

Disclaimer

This project is for educational/research purposes only and does not constitute financial advice.
Market data from Yahoo Finance may be delayed or inaccurate.