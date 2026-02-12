# Curating Alpha on BTC and ETH Cryptocurrency Markets  
Inter IIT Tech Meet 13.0 – Zelta Automations  
Team 67  

---

## 1. Project Overview

This project develops and evaluates two independent algorithmic trading systems for cryptocurrency markets:

1. A statistical and regime-based trading strategy for ETH/USDT.
2. A Reinforcement Learning (Q-Learning) based trading agent for BTC/USDT.

Both strategies are built using hourly historical data and are evaluated under realistic trading conditions incorporating transaction costs, volatility filters, and risk management constraints.

The objective is to outperform benchmark buy-and-hold returns while maintaining controlled drawdowns and robust risk-adjusted performance.

---

## 2. Data Requirements

The following datasets are required:

```
BTC_2019_2023_1h.csv
ETHUSDT_1h.csv
```

### Data Specifications

- Frequency: 1-hour OHLCV data
- Required columns:
  - Timestamp
  - Open
  - High
  - Low
  - Close
  - Volume

### Backtest Periods

ETH Strategy:
- 01 January 2020 – 31 December 2023

BTC Reinforcement Learning Strategy:
- Training: 01 January 2020 – 31 December 2022
- Testing (walk-forward): 01 January 2023 – 31 December 2023
- Quarterly retraining and evaluation

---

## 3. ETH/USDT Strategy

### 3.1 Core Hypothesis

Bitcoin hourly returns significantly influence Ethereum hourly returns.

Statistical validation results:

- Pearson correlation coefficient: r = 0.7851
- p-value < 0.001
- Lag-1 BTC to ETH correlation: 0.0551
- Null hypothesis rejected at 5% significance level

This establishes a predictive lead–lag relationship used for ETH trade alignment.

---

### 3.2 Regime Detection using CUSUM

Deviation from reference value:

```
d_i = x_i − μ₀
```

Upward and downward cumulative sums:

```
S_hi(i) = max(0, S_hi(i−1) + (x_i − μ₀ − k))
S_lo(i) = max(0, S_lo(i−1) + (μ₀ − x_i − k))
```

Dynamic threshold:

```
k_i = δ · σ_i
```

Where:

- μ₀ is obtained using Kalman filtering
- σ_i is rolling standard deviation
- δ controls sensitivity

Regime classification:

- Bullish regime: S_hi exceeds threshold
- Bearish regime: S_lo exceeds threshold

---

### 3.3 Volatility Filtering (ATR)

True Range:

```
TR_t = max(High_t − Low_t,
           |High_t − Close_{t−1}|,
           |Low_t − Close_{t−1}|)
```

Average True Range:

```
ATR_n = (1/n) Σ TR_i
```

Trading condition:

```
ATR < 1% of BTC price
```

Positions are exited immediately if:

```
ATR > 2.5% of BTC price
```

---

### 3.4 Additional Filters

1. Hurst Exponent  
   - Trade only when H > 0.5 (persistent trending regime)

2. Rolling BTC–ETH Correlation  
   - Trade only when correlation > 0.6

3. Confirmation Indicators  
   - RSI  
   - Bollinger Bands  
   - Supertrend  

---

### 3.5 Risk Management

- Trailing stop-loss
- Volatility-based exit
- Maximum holding period: 28 days
- One-day cooldown after stop-loss

---

### 3.6 ETH Strategy Performance (2020–2023)

| Metric | Value |
|--------|--------|
| Net Return | 7684.52% |
| Benchmark Return | 1687.59% |
| Sharpe Ratio | 5.966 |
| Sortino Ratio | 19.67 |
| Maximum Drawdown | 17.14% |
| Total Trades | 162 |
| Win Rate | 47.53% |

Initial capital: 1000 USD  
Final capital: 77,845.15 USD  

---

## 4. BTC/USDT Reinforcement Learning Strategy

### 4.1 Framework

Model: Tabular Q-Learning  
Objective: Maximize cumulative risk-adjusted return under stop-loss constraints.

Q-update rule:

```
Q(s,a) ← Q(s,a) + α [ r + γ max Q(s',a') − Q(s,a) ]
```

Where:

- α: learning rate
- γ: discount factor
- r: immediate reward

Exploration: epsilon-greedy with decay.

---

### 4.2 State Space

Discrete state representation:

- Percentage price change (20 bins: −5% to +5%)
- Current position {0, 1, −1}
- RSI signal {1, 0, −1}
- EMA ordering (7, 14, 28)
- Aroon indicator

---

### 4.3 Action Space

1. Enter Long
2. Exit Long
3. Enter Short
4. Exit Short

---

### 4.4 Reward Structure

Reward includes:

- Realized profit and loss
- Commission penalties
- 5% stop-loss enforcement
- Bankruptcy penalty
- Inactivity penalty

Short positions capped at 75% of capital for risk control.

---

### 4.5 Training Procedure

- Train on 2020–2022
- Test quarterly in 2023
- After each quarter, include that data into training
- Final evaluation on full 2023 dataset

---

### 4.6 BTC Strategy Performance (2023)

| Metric | Value |
|--------|--------|
| Net Return | 224.90% |
| Benchmark Return | 157.08% |
| Sharpe Ratio | 9.15 |
| Sortino Ratio | 26.95 |
| Maximum Drawdown | 13.50% |
| Win Rate | 64.52% |

Initial capital: 1000 USD  
Final capital: 3249.01 USD  

---

## 5. Project Structure

```
btc_main.py
eth_main.py
BTC_2019_2023_1h.csv
ETHUSDT_1h.csv
Zelta_FinalReport.pdf
Zelta_presentation.pdf
```

---

## 6. Replication Instructions

### Step 1: Clone Repository

```
git clone https://github.com/namank04/Zelta-2024.git
cd Zelta-2024
```

### Step 2: Install Dependencies

```
pip install pandas numpy matplotlib ta scipy scikit-learn
```

If additional dependencies are required, install as prompted.

### Step 4: Place Data Files

Ensure the following files are in the project root directory:

```
BTC_2019_2023_1h.csv
ETHUSDT_1h.csv
```

### Step 5: Run ETH Strategy

```
python eth_main.py
```

This will:

- Load BTC and ETH data
- Compute indicators
- Perform backtest
- Output trade statistics and performance metrics

### Step 6: Run BTC Reinforcement Learning Strategy

```
python btc_main.py
```

This will:

- Train Q-learning agent
- Perform quarterly walk-forward evaluation
- Output final performance statistics

---

## 7. Assumptions and Limitations

- Slippage modeled only via fixed commissions
- Tabular Q-learning (no function approximation)
- Discrete state space approximation
- No live trading deployment
- Crypto regime instability risk

---

## 8. Academic Context

Developed for Inter IIT Tech Meet 13.0  
Zelta Automations – Algorithmic Trading Problem Statement
