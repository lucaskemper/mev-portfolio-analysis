# MEV Impact on DeFi Portfolio Execution  
### And Planned Research: MEV-Aware Portfolio Management with Reinforcement Learning

This repository investigates how **Maximal Extractable Value (MEV)** inflates execution costs in decentralized exchanges and motivates a full-scale MSc thesis on **MEV-aware reinforcement learning for portfolio management in DeFi**.

Standard portfolio models assume execution costs are small and stable. In DeFi this assumption is wrong. Execution cost is **regime-dependent and adversarial**, driven by sandwich attacks, backruns, and block-level congestion dynamics. Strategies that ignore MEV conditions systematically overpay, especially during periods of high volatility when rebalancing matters most.

This repo has two components:

1. **Empirical analysis** of MEV-driven execution costs (this section)  
2. **Planned MSc thesis** building an RL agent that adapts trading behavior to MEV conditions (detailed later)

---

# 1. Empirical Analysis: MEV-Driven Execution Costs

## Problem

Traditional portfolio optimization assumes:

> Total Cost = Base Slippage + Fixed Fee

Reality in DeFi:

> Total Cost = f(gas_regime, pool_depth, MEV_risk, volatility)

Where **MEV risk is adaptive and adversarial**. MEV bots strike when gas prices and liquidity conditions make sandwich attacks profitable. This regime-dependence makes naive rebalancing vastly more expensive.

---

# 2. Methodology

- **Dataset:** 10,000 simulated Uniswap blocks (calibrated to Jan–Mar 2024 MEV patterns)  
- **Regimes:** Gas tertiles (Low, Medium, High)  
- **MEV Detection:** Sandwich attack labeling from gas and tx-pattern heuristics  
- **Metrics:** Slippage, sandwich frequency, regime multipliers, execution cost distribution  

---

# 3. Key Findings

## 3.1 Gas Regimes Drive MEV Activity

![Gas Regimes](1_gas_regimes.png)

High-gas periods (>100 gwei) create incentives for MEV bot activation.

## 3.2 Sandwich Attacks Concentrate in High-Gas Regimes

![Sandwich Frequency](2_sandwich_frequency.png)

- Low gas: **12%**  
- Medium gas: **13%**  
- High gas: **33%**

Attackers operate where margins justify priority fees.

## 3.3 Sandwiches Increase Execution Costs 3x

![Execution Cost](3_execution_cost.png)

- Normal blocks: **27 bps**  
- Sandwich blocks: **77 bps**  
- Cost multiplier: **2.9x**

A $1M daily-rebalancing portfolio loses ~$5,000/day vs $1,400/day without MEV.

## 3.4 Rebalancing Costs Scale With Gas Regime

![Regime Costs](4_regime_costs.png)

- Low gas: **27 bps**  
- Medium gas: **31 bps** (1.1x)  
- High gas: **53 bps** (2.0x)

A naive “rebalance every day” strategy pays 2x more during stressful, high-MEV periods.

---

# 4. Portfolio Impact Calculation

Assumptions:
- Daily rebalancing  
- $1M portfolio  
- 252 trading days  
- Equal time spent per regime  

Annual execution cost:
- Low gas: **$680K**  
- Medium gas: **$781K**  
- High gas: **$1.335M**  

Weighted average: **~$930K/year drag**  
Baseline (low gas only): **$680K**  

**Cost of ignoring MEV regimes:** ~$250K/year (2.5% of portfolio value)

This drag reduces Sharpe and amplifies drawdowns, especially when volatility is already elevated.

---

# 5. Implications for Portfolio Management

Ignoring MEV means:

1. Static rebalancing massively overpays during high-gas windows  
2. Vol-targeting rebalances more during stress periods, compounding costs  
3. Tail risk (ES, max DD) is underestimated because execution uncertainty isn’t modeled  

This motivates the next step.

---

# 6. Planned Thesis Research  
## MEV-Aware Portfolio Management with Reinforcement Learning

To address these structural execution problems, my MSc thesis builds and evaluates a **reinforcement learning (RL) policy that is explicitly MEV-aware**.

## 6.1 Research Question

**Can an RL policy that observes gas regimes and MEV conditions achieve lower expected shortfall and max drawdown while preserving Sharpe, compared to MEV-blind baselines?**

---

# 7. Planned Methodology (Thesis)

## 7.1 AMM Simulation Environment

A reproducible **Uniswap v2 constant-product AMM** with:

- 2–4 assets (ETH, stables, mid-cap token)  
- Historical or GBM-generated midprices  
- Fee mechanics  
- Liquidity buckets  

## 7.2 MEV Shock Generator

A rule-based engine injecting:

- Sandwich attack slippage  
- Backrun overshoot-and-revert patterns  
- MEV regimes (low/medium/high), calibrated to real block data  

## 7.3 RL Agent

### State features:
- returns, realized vol  
- pool depth, liquidity  
- gas regime  
- mempool proxies (tx count, priority fee percentiles)  
- current allocations  

### Actions:
- weight adjustments in discrete or continuous space  
- skip-trade option  

### Reward:
```
r_t = PnL_t - λ * slippage_t - μ * tail_risk_t
```

### Algorithms:
- PPO (primary)  
- DQN (discrete action ablation)  

### Baselines:
1. Naive fixed-weight  
2. Vol-targeting  
3. MEV-blind RL agent  

### Evaluation:
- Sharpe  
- ES  
- Max DD  
- Turnover  
- Realized slippage  
- Regime-conditioned performance  

---

# 8. Hypothesis

**MEV-aware agents should avoid or split trades during high-MEV regimes, reducing execution costs and improving drawdown profile without sacrificing returns.**

---

# 9. Contact

- **Email:** lucas.kemper01@gmail.com  
- **LinkedIn:** https://linkedin.com/in/lucaskemper  
