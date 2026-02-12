# Curating Alpha on BTC & ETH Cryptocurrency Markets  
**Inter IIT Tech Meet 13.0 – Zelta Automations (Team 67)**  

Algorithmic trading strategies for **ETH/USDT** (statistical + regime-based) and **BTC/USDT** (Reinforcement Learning based), built and backtested on hourly crypto data (2020–2023).

---

# 📌 Overview

This project develops and evaluates two independent trading systems:

## 1️⃣ ETH Strategy (Statistical + Regime-Based)

A multi-layered strategy that combines:

- BTC–ETH correlation structure  
- CUSUM-based regime detection  
- Volatility filtering (ATR)  
- Hurst exponent (trend persistence)  
- Momentum & confirmation indicators  
- Strict risk management  

## 2️⃣ BTC Strategy (Reinforcement Learning)

A Q-learning based agent trained to dynamically trade BTC using:

- Discretized state representation  
- Adaptive reward function  
- Stop-loss & drawdown control  
- Quarterly walk-forward validation  

---

# 📊 Dataset

Required files:

```
BTC_2019_2023_1h.csv
ETHUSDT_1h.csv
```

### Data Specifications

- Frequency: 1-hour candles  
- ETH Strategy Backtest: 2020-01-01 → 2023-12-31  
- BTC RL Training: 2020–2022  
- BTC RL Testing: 2023 (quarterly walk-forward)

Columns required:

- Timestamp
- Open
- High
- Low
- Close
- Volume

---

# 🧠 ETH/USDT Strategy

## 🔎 Core Hypothesis

BTC hourly returns significantly influence ETH returns.

Statistical validation:

- Pearson Correlation: **r = 0.7851**
- p-value < 0.001
- Lag-1 BTC → ETH correlation: 0.0551
- Null hypothesis rejected at 5% significance

---

## 🏗 Strategy Architecture

### 1️⃣ Regime Detection – CUSUM on BTC

Deviation:
```
d_i = x_i - μ₀
```

Cumulative sums:
```
S_hi(i) = max(0, S_hi(i−1) + (x_i − μ₀ − k))
S_lo(i) = max(0, S_lo(i−1) + (μ₀ − x_i − k))
```

Dynamic threshold:
```
k_i = δ · σ_i
```

Where:

- μ₀ obtained via Kalman filtering
- σᵢ = rolling volatility

Regime classification:

- Bullish → S_hi exceeds threshold
- Bearish → S_lo exceeds threshold

---

### 2️⃣ Volatility Filtering (ATR)

True Range:
```
TR_t = max(High_t − Low_t, |High_t − Close_{t−1}|, |Low_t − Close_{t−1}|)
```

ATR:
```
ATR_n = (1/n) * Σ TR_i
```

Trade only if:
```
ATR < 1% of BTC price
```

---

### 3️⃣ Trend Strength Filter

Hurst Exponent:

- H > 0.5 → trending market (trade allowed)
- H ≤ 0.5 → mean-reverting (avoid trades)

---

### 4️⃣ Correlation Filter

Rolling 7-hour BTC–ETH correlation.

Trade only if:
```
Correlation > 0.6
```

---

### 5️⃣ Entry Conditions (Long Example)

- BTC RSI > 70  
- BTC price > middle Bollinger band  
- BTC CUSUM bullish  
- ETH Supertrend bullish  
- Hurst > 0.5  
- ATR < 1%  
- Correlation > 0.6  

---

### 6️⃣ Risk Management

- Trailing Stop Loss  
- Volatility Exit (ATR > 2.5%)  
- Time Stop (max 28 days)  
- 1-day cooldown after stop-loss  

---

# 📈 ETH Strategy Performance (2020–2023)

| Metric | Value |
|--------|--------|
| Net Return | **7684.52%** |
| Benchmark Return | 1687.59% |
| Sharpe Ratio | 5.966 |
| Sortino Ratio | 19.67 |
| Max Drawdown | 17.14% |
| Total Trades | 162 |
| Win Rate | 47.53% |

Initial Capital: $1000  
Final Capital: **$77,845.15**

---

# 🤖 BTC/USDT Strategy – Reinforcement Learning

## 📦 State Space

Discrete representation:

- Price change (20 bins: −5% to +5%)
- Current Position {0, 1, −1}
- RSI signal {1, 0, −1}
- EMA ordering (7,14,28)
- Aroon indicator

---

## 🎯 Action Space

1. Enter Long  
2. Exit Long  
3. Enter Short  
4. Exit Short  

---

## 💰 Reward Structure

Includes:

- Realized PnL
- Commission penalties
- 5% stop-loss enforcement
- Bankruptcy penalty
- Inactivity penalty

---

## 🔁 Q-Update Rule

```
Q(s,a) ← Q(s,a) + α [ r + γ max Q(s',a') − Q(s,a) ]
```

- Epsilon-greedy exploration
- Epsilon decay
- Walk-forward evaluation

---

# 📈 BTC RL Performance (2023)

| Metric | Value |
|--------|--------|
| Net Return | **224.90%** |
| Benchmark | 157.08% |
| Sharpe Ratio | 9.15 |
| Sortino Ratio | 26.95 |
| Max Drawdown | 13.50% |
| Win Rate | 64.52% |

Initial Capital: $1000  
Final Capital: **$3249.01**

---

# 📂 Project Structure

```
btc_main.py
eth_main.py
BTC_2019_2023_1h.csv
ETHUSDT_1h.csv
Zelta_FinalReport.pdf
Zelta_presentation.pdf
```

---

# ⚙️ How To Run

### Install Dependencies

```
pip install pandas numpy matplotlib ta scipy scikit-learn
```

### Run ETH Strategy

```
python eth_main.py
```

### Run BTC RL Strategy

```
python btc_main.py
```

---

# 📌 Key Contributions

- Hybrid statistical + regime-based ETH model
- BTC-driven ETH predictive structure
- Dynamic CUSUM with volatility scaling
- Reinforcement learning with discretized state modeling
- Walk-forward validation
- Integrated risk management

---

# ⚠️ Limitations

- No advanced slippage modeling
- Tabular Q-learning (no deep RL)
- Crypto regime instability risk
- Discrete state approximation

---

# 🚀 Future Improvements

- Deep Q-Network (DQN) / PPO
- Continuous state space
- Multi-asset RL portfolio
- Bayesian volatility estimation
- Real-time deployment

---

# 📜 Developed For

Inter IIT Tech Meet 13.0 – Zelta Automations Problem Statement
