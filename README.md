# AlphaForge-AI Goal

Desired Outcome: An AI-assisted alpha research platform that uses LLM-guided RL/MCTS to discover interpretable formulaic stock-trading alphas, validates them rigorously, and deploys only selected alpha pools into paper trading.

# AlphaForge AI

AlphaForge AI is a personal research project for learning systematic trading, formulaic alpha evaluation, and AI-assisted alpha discovery. The long-term goal is to build a serious alpha research platform that can evaluate, compare, and eventually generate formulaic stock-trading signals using robust backtesting, machine learning, and LLM-assisted research workflows.

This project is inspired by the FYP direction of **Multi-Agent LLM Refinement Loops for RL-Driven Formulaic Alpha Discovery**, where the focus is on discovering and refining formulaic alphas, evaluating them with metrics such as IC / Rank IC / ICIR, and eventually improving alpha generation using RL and LLM-guided methods.

> **Important:** This repository is for research, learning, and paper-trading only. It is not financial advice and is not ready for real-money trading.

---

## Project Goal

The goal of this project is to build an end-to-end research system for formulaic alpha discovery and evaluation.

In the short term, the project focuses on:

- Learning the basics of quantitative finance and systematic trading.
- Implementing simple hand-crafted alpha signals.
- Evaluating alpha signals using IC, Rank IC, ICIR, and simple backtests.
- Understanding whether a signal has predictive power across a stock universe.
- Building a clean foundation for future FYP-related work.

In the long term, the project aims to evolve into:

- A reusable alpha evaluation engine.
- A formulaic alpha DSL / expression system.
- An alpha generation system using random search, genetic programming, RL, or MCTS.
- An LLM-assisted alpha research workflow.
- A robust paper-trading system with risk controls.
- A possible research platform that can be iterated on beyond the FYP.

The current direction is not to build a simple “AI trading bot.” Instead, the aim is to build a serious alpha research platform that can answer questions such as:

- Does this alpha predict future returns?
- Is the signal stable across time?
- Does it survive out-of-sample testing?
- Does it survive transaction costs?
- Is it different from existing alphas?
- Can it be combined with other alphas into a stronger alpha pool?

---

## Current Status

Current phase: **Phase 0 — Basic Alpha Evaluation**

The project currently contains a first research notebook:

```text
notebooks/01_basic_alpha_eval.ipynb
```

This notebook currently:

1. Downloads OHLCV data for a small stock universe.
2. Cleans and reshapes the data into a usable panel format.
3. Computes future next-day returns.
4. Creates several simple hand-written alpha signals.
5. Computes IC and Rank IC for each alpha.
6. Computes ICIR and Rank ICIR.
7. Runs a simple top/bottom long-short backtest.
8. Generates CSV summaries for alpha performance and backtest performance.

The initial hand-written alphas include:

- `momentum_5d`
- `mean_reversion_1d`
- `volume_spike`
- `volatility_breakout`
- `overnight_gap`

This is still an early research baseline. It is useful for learning and evaluation, but it is not yet robust enough for real trading.

---

## Repository Structure

Suggested structure:

```text
alphaforge-ai/
├── README.md
├── notebooks/
│   └── 01_basic_alpha_eval.ipynb
├── data/
│   ├── performance_summary.csv
│   └── backtest_summary.csv
├── reports/
│   └── figures/
├── references/
│   └── reading_notes.md
├── alphaforge/
│   └── __init__.py
└── tests/
```

---

## How to Set Up the Environment

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd alphaforge-ai
```

### 2. Create a virtual environment

Using `venv`:

```bash
python -m venv .venv
source .venv/bin/activate
```

On Windows:

```bash
python -m venv .venv
.venv\Scripts\activate
```

### 3. Install dependencies

For the current Phase 0 notebook, install:

```bash
pip install pandas numpy scipy matplotlib yfinance scikit-learn jupyterlab
```

Optional but recommended:

```bash
pip install ipykernel
python -m ipykernel install --user --name alphaforge-ai --display-name "AlphaForge AI"
```

### 4. Start Jupyter Lab

```bash
jupyter lab
```

Then open:

```text
notebooks/01_basic_alpha_eval.ipynb
```

---

## How to Run the Notebook

1. Activate the virtual environment.
2. Start Jupyter Lab.
3. Open `notebooks/01_basic_alpha_eval.ipynb`.
4. Run the notebook from top to bottom.
5. Check that the data downloads correctly.
6. Check that the output CSV files are generated.

Expected generated files:

```text
data/performance_summary.csv
data/backtest_summary.csv
```

Depending on your notebook version, plots may also be displayed or saved under:

```text
reports/figures/
```

---

## What the Generated CSV Files Mean

### `performance_summary.csv`

This file evaluates whether each alpha has predictive power.

It answers:

> Do stocks with higher alpha scores tend to have higher future returns?

Important columns:

| Column | Meaning |
|---|---|
| `alpha` | Name of the alpha signal |
| `mean_ic` | Average Pearson correlation between alpha values and future returns |
| `mean_rank_ic` | Average Spearman rank correlation between alpha ranks and future return ranks |
| `ic_std` | Standard deviation of daily IC values |
| `rank_ic_std` | Standard deviation of daily Rank IC values |
| `observations` | Number of trading days evaluated |
| `icir` | Mean IC divided by IC standard deviation |
| `rank_icir` | Mean Rank IC divided by Rank IC standard deviation |

How to read it:

- Positive `mean_ic` means higher alpha values are generally associated with higher future returns.
- Positive `mean_rank_ic` means the alpha is useful for ranking stocks.
- Higher `icir` or `rank_icir` means the signal is more stable relative to its noise.
- Values close to zero usually mean the signal is weak or noisy.
- Negative values may mean the signal is reversed, useless, or regime-dependent.

For ranking-based strategies, `mean_rank_ic` and `rank_icir` are usually especially important.

---

### `backtest_summary.csv`

This file evaluates the result of applying each alpha in a simple long-short strategy.

The notebook uses a simple rule:

```text
Each day:
    long the top-ranked stocks by alpha score
    short the bottom-ranked stocks by alpha score
    measure the next-day long-short return
```

Important columns:

| Column | Meaning |
|---|---|
| `alpha` | Name of the alpha signal |
| `avg_daily_return` | Average daily return of the long-short strategy |
| `daily_vol` | Daily volatility of the strategy |
| `days` | Number of trading days in the backtest |
| `annualized_return` | Approximate yearly return, calculated from average daily return |
| `annualized_vol` | Approximate yearly volatility |
| `sharpe` | Risk-adjusted return estimate |

How to read it:

- Higher `annualized_return` is better, but only if risk is controlled.
- Lower `annualized_vol` means returns are less volatile.
- Higher `sharpe` means better return per unit of risk.
- Negative Sharpe usually means the strategy performed poorly in this simple setup.

Important limitation:

This is a simplified backtest. It does not yet include transaction costs, slippage, realistic execution, borrow costs, sector neutrality, or robust out-of-sample validation.

---

## Research and Paper-Trading Warning

This project is for research and learning only.

The current results should **not** be used for real-money trading.

Reasons:

- The stock universe is small.
- The current backtest is simplified.
- Transaction costs are not fully modelled yet.
- Slippage is not fully modelled yet.
- There is no robust train/validation/test split yet.
- There is no walk-forward validation yet.
- There is no live paper-trading validation yet.
- There may be survivorship bias and data-quality issues.
- A profitable-looking backtest can fail in live trading.

Before any real-money usage, the system should go through:

1. Out-of-sample testing.
2. Transaction-cost modelling.
3. Turnover analysis.
4. Drawdown analysis.
5. Alpha correlation analysis.
6. Walk-forward validation.
7. Paper trading.
8. Manual review.
9. Risk controls and kill switches.

This repository does not provide investment advice or trading recommendations.

---

## Current Phase 0 Checklist

Phase 0 is complete when the project can answer the basic alpha-evaluation questions.

Checklist:

- [ ] Repository is created.
- [ ] Python environment is set up.
- [ ] `notebooks/01_basic_alpha_eval.ipynb` runs from top to bottom.
- [ ] OHLCV data can be downloaded or loaded.
- [ ] Future returns can be computed.
- [ ] At least 5 simple hand-written alphas are implemented.
- [ ] IC and Rank IC are computed.
- [ ] ICIR and Rank ICIR are computed.
- [ ] A simple long-short backtest is implemented.
- [ ] `performance_summary.csv` is generated.
- [ ] `backtest_summary.csv` is generated.
- [ ] Plots are displayed or saved.
- [ ] Basic written observations are added to the notebook.
- [ ] Limitations are clearly documented.

---

## Phase 0 Questions I Should Be Able to Answer

By the end of Phase 0, I should be able to answer:

1. What is a formulaic alpha?
2. What is the difference between an alpha signal and a trading strategy?
3. What is future return, and why must it be shifted correctly?
4. What is IC?
5. What is Rank IC?
6. Why is Rank IC useful for cross-sectional stock ranking?
7. What is ICIR?
8. What does a positive or negative IC mean?
9. Why can an alpha have positive IC but weak backtest performance?
10. Why can a simple backtest be misleading?
11. What does the long-short backtest actually do?
12. What are the limitations of the current notebook?
13. Why are transaction costs important?
14. Why is train/test splitting important?
15. Why is one alpha usually not enough?
16. What is an alpha pool?
17. How does this notebook connect to future LLM/RL alpha generation?

---

## Next Steps

After Phase 0, the next phase is to make the evaluator more realistic.

Phase 1 should add:

- Train / validation / test split.
- Transaction costs.
- Turnover.
- Max drawdown.
- Rolling Rank IC.
- Alpha correlation matrix.
- A cleaner alpha evaluation report.

The purpose of Phase 1 is to move from a toy notebook toward a more trustworthy alpha research engine.
