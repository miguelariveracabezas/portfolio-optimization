# Mean–Variance Portfolio Optimization

A Python implementation of constrained mean–variance portfolio optimization using **Ledoit-Wolf shrinkage**, **Black-Litterman expected returns**, and **out-of-sample validation**.

---

## Overview

This project constructs an efficient frontier across six ETFs and identifies the **maximum Sharpe ratio (tangency) portfolio** using institutional-grade estimation techniques. A train/test split evaluates out-of-sample performance against a minimum-variance and equal-weight benchmark.

---

## Assets

| Ticker | Exposure |
|--------|----------|
| SPY | US Large Cap Blend |
| QQQ | US Large Cap Tech |
| IWM | US Small Cap |
| EFA | International Developed |
| EEM | Emerging Markets |
| TLT | US Long-Term Bonds |

---

## Methodology

### 1. Covariance Estimation — Ledoit-Wolf Shrinkage
The sample covariance matrix is replaced with a **shrinkage estimator** (Ledoit & Wolf, 2004). This reduces in-sample overfitting by pulling extreme off-diagonal correlations toward a structured target, producing more stable portfolio weights.

### 2. Expected Returns — Black-Litterman Equilibrium
Rather than using noisy historical sample means, expected returns are derived from **market-implied equilibrium returns**:

$$\Pi = \delta \Sigma w^{mkt}$$

where $\delta$ is the implied market risk aversion, $\Sigma$ is the shrunk covariance, and $w^{mkt}$ are approximate market-cap weights. This anchors returns to what markets are already pricing in, dramatically reducing estimation error.

### 3. Efficient Frontier — Target-Return Sweep
Instead of sweeping a scalar risk-aversion parameter $\lambda$ (which clusters points non-uniformly), the frontier is traced by fixing a **target return** $\mu^*$ and solving:

$$\min_w \; w^\top \Sigma w \quad \text{s.t.} \quad \mu^\top w = \mu^*, \; \sum w_i = 1, \; 0 \leq w_i \leq 0.30$$

This gives evenly spaced frontier points and a cleaner view of the risk-return tradeoff.

### 4. Tangency Portfolio
The **maximum Sharpe ratio portfolio** is found by solving:

$$\max_w \; \frac{\mu^\top w - r_f}{\sqrt{w^\top \Sigma w}}$$

This is the point where the **Capital Market Line** is tangent to the efficient frontier — the optimal risky portfolio for any investor who can mix with the risk-free asset.

### 5. Out-of-Sample Validation
Weights are **frozen** at the training cutoff and evaluated on held-out data. Three portfolios are compared:

| Portfolio | Construction |
|-----------|-------------|
| Tangency (Max-Sharpe) | Optimized on training data |
| Min-Variance | Lowest-vol portfolio from training |
| Equal-Weight (1/N) | Naive benchmark |

---

## Results

The notebook produces:
- **Efficient frontier** colored by Sharpe ratio with individual assets overlaid
- **Capital Market Line** from the risk-free rate through the tangency portfolio
- **Tangency portfolio weights** bar chart
- **Cumulative return** chart comparing all three portfolios out-of-sample

---

## Setup

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/portfolio-optimization.git
cd portfolio-optimization

# Install dependencies
pip install -r requirements.txt

# Launch notebook
jupyter notebook portfolio_optimization.ipynb
```

---

## Dependencies

```
yfinance
pandas
numpy
scipy
scikit-learn
matplotlib
jupyter
```

Or install directly:

```bash
pip install yfinance pandas numpy scipy scikit-learn matplotlib jupyter
```

---

## Project Structure

```
portfolio-optimization/
│
├── portfolio_optimization.ipynb   # Main notebook
├── README.md                      # This file
└── requirements.txt               # Dependencies
```

---

## Key Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| `RF` | 5.2% | Annualised risk-free rate |
| `W_MAX` | 30% | Maximum weight per asset |
| `TRAIN_END` | 2023-01-01 | Train/test split date |
| Train window | 2018–2022 | ~1,250 trading days |
| Test window | 2023–2025 | ~500 trading days |

---

## References

- Ledoit, O. & Wolf, M. (2004). *A well-conditioned estimator for large-dimensional covariance matrices.* Journal of Multivariate Analysis.
- Black, F. & Litterman, R. (1992). *Global Portfolio Optimization.* Financial Analysts Journal.
- Markowitz, H. (1952). *Portfolio Selection.* Journal of Finance.
