# 📈 Cryptocurrency Portfolio Optimization using Markowitz Mean-Variance Framework

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)

A dynamic portfolio optimization strategy for cryptocurrency assets using the classical **Markowitz Mean-Variance framework** with rolling windows. This project implements a complete backtesting pipeline with realistic constraints including transaction costs, volatility limits, and diversification requirements.

![Portfolio Evolution](https://img.shields.io/badge/Strategy-Mean--Variance-purple)
![Assets](https://img.shields.io/badge/Assets-20%20Cryptocurrencies-yellow)

---

## 🎯 Project Overview

This project tackles the challenge of constructing an optimal cryptocurrency portfolio that maximizes risk-adjusted returns (Sharpe Ratio) while respecting practical investment constraints.

### Constraints

| Constraint | Threshold | Description |
|------------|-----------|-------------|
| 📊 Position Type | Long-only | No short selling (w ≥ 0) |
| 📉 Volatility | < 25% | Annualized portfolio volatility |
| 🎯 Diversification | > 70% | Concentration metric (inverse HHI) |
| 💰 Transaction Fees | < 0.5% | Total fees relative to initial capital |

### Objective

**Maximize the Sharpe Ratio:**

$$SR = \frac{E[R_p] - R_f}{\sigma_p}$$

Where:
- $E[R_p]$: Expected portfolio return
- $R_f$: Risk-free rate (5% annually)
- $\sigma_p$: Portfolio standard deviation

---

## 🧮 Mathematical Framework

### Markowitz Optimization Problem

The core optimization problem solved at each rebalancing period:

$$\min_{w} \quad \frac{1}{2} w^T \Sigma w - \gamma \mu^T w$$

Subject to:
- $\sum_{i=1}^{n} w_i = 1$ (fully invested)
- $w_i \geq 0$ (long-only)

Where:
- $w$: Portfolio weights vector
- $\mu$: Expected returns vector (annualized)
- $\Sigma$: Covariance matrix (annualized)
- $\gamma$: Risk aversion parameter

### Rolling Window Approach

Instead of static optimization, we use a dynamic strategy:

```
┌─────────────────────────────────────────────────────────────────┐
│                        Timeline                                  │
├─────────────────────────────────────────────────────────────────┤
│  [====== IS Window (70 days) ======][== OOS Window (35 days) ==]│
│         Parameter Estimation              Apply Weights          │
│                                                                  │
│  Then slide forward by OOS_window and repeat...                 │
└─────────────────────────────────────────────────────────────────┘
```

- **In-Sample (IS)**: Historical data for estimating μ and Σ
- **Out-of-Sample (OOS)**: Period where optimized weights are applied

---

## 📁 Project Structure

```
├── Portfolio_Optimization_Markowitz.ipynb   # Main notebook with full analysis
├── data.csv                                  # Cryptocurrency price data (20 assets)
├── README.md                                 # This file
├── requirements.txt                          # Python dependencies
└── LICENSE                                   # MIT License
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10 or higher
- Jupyter Notebook or JupyterLab

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/crypto-portfolio-optimization.git
   cd crypto-portfolio-optimization
   ```

2. **Create a virtual environment (recommended)**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Launch Jupyter Notebook**
   ```bash
   jupyter notebook Portfolio_Optimization_Markowitz.ipynb
   ```

### Requirements

```txt
numpy>=1.21.0
pandas>=1.3.0
scipy>=1.7.0
cvxpy>=1.2.0
matplotlib>=3.4.0
```

---

## 📊 Results

### Performance Comparison

| Metric | Markowitz | Bitcoin Only | Equal-Weight |
|--------|-----------|--------------|--------------|
| Annualized Return | ~30% | Variable | Variable |
| Annualized Volatility | <25% | ~60-80% | ~50-60% |
| Sharpe Ratio | >1.0 | <0.5 | ~0.5 |
| Max Drawdown | Lower | Higher | Medium |

### Key Visualizations

The notebook generates:
- 📈 **Efficient Frontier** with tangency portfolio
- 📉 **Portfolio Value Evolution** over time
- ⚖️ **Weight Allocation** dynamics
- 🎯 **Concentration Metric** tracking
- 🔧 **Hyperparameter Sensitivity** analysis

---

## ⚙️ Optimal Parameters

Found through grid search optimization:

| Parameter | Optimal Value | Description |
|-----------|---------------|-------------|
| IS Window | 70 days | Lookback period for estimation |
| OOS Window | 35 days | Rebalancing frequency |
| γ (Gamma) | 1.7 | Risk aversion parameter |
| EWMA Span | 3 days | Expected return smoothing |

---

## 🔬 Methodology

### 1. Data Preprocessing
- Load daily price data for 20 cryptocurrency assets
- Compute simple returns: $r_t = \frac{P_t - P_{t-1}}{P_{t-1}}$

### 2. Parameter Estimation
- **Expected Returns**: Exponential Weighted Moving Average (EWMA)
- **Covariance Matrix**: Sample covariance from IS window
- Both annualized by multiplying by 365

### 3. Optimization
- Solve convex QP using CVXPY
- Apply diversification shrinkage: $w_{new} = 0.125 \cdot w_{opt} + 0.035$
- Enforce volatility constraint via risk-free asset allocation

### 4. Performance Evaluation
- Track out-of-sample returns
- Calculate Sharpe Ratio, Max Drawdown, Transaction Costs
- Compare against benchmarks

---

## 🛠️ Customization

### Modify Constraints

In the notebook, adjust these parameters:

```python
# Risk constraints
MAX_VOLATILITY = 0.235      # 23.5% (with buffer for 25% limit)
W_DIV = 0.035               # Minimum diversification weight

# Window sizes
IS_window = 70              # In-sample period
OOS_window = 35             # Rebalancing frequency

# Risk aversion
gamma = 1.7                 # Higher = more aggressive
```

### Use Your Own Data

Replace `data.csv` with your own price data:
- CSV format with date index
- Columns = asset prices
- Daily frequency recommended

---

## 📚 References

1. **Markowitz, H.** (1952). Portfolio Selection. *The Journal of Finance*, 7(1), 77-91.

2. **DeMiguel, V., Garlappi, L., & Uppal, R.** (2009). Optimal versus naive diversification. *The Review of Financial Studies*, 22(5), 1915-1953.

3. **Boyd, S., & Vandenberghe, L.** (2004). *Convex Optimization*. Cambridge University Press.

4. **CVXPY Documentation**: [https://www.cvxpy.org/](https://www.cvxpy.org/)

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Thomas**  
Master's Student in Engineering @ CentraleSupélec  
📧 [Contact](mailto:your.email@example.com)  
🔗 [LinkedIn](https://linkedin.com/in/yourprofile)

---

## ⭐ Acknowledgments

- CentraleSupélec for the academic framework
- CVXPY team for the excellent optimization library
- The quantitative finance community for methodology insights

---

<p align="center">
  <i>If you found this project helpful, please consider giving it a ⭐!</i>
</p>
