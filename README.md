# LLM-Based Alpha Mining with Knowledge Accumulation

An automated quantitative-research system that uses Large Language Models (LLMs) to **generate, review, backtest, refine, and accumulate alpha factors** for the Vietnamese stock market.

This repository accompanies our published paper:

> **Self-improving alpha mining for quantitative trading via multi-agent large language models with knowledge base accumulation**  
> Minh-Son Vu, The-Trung Pham, and Hong-Viet Tran  
> *Machine Learning with Applications*, Volume 25, 2026, Article 100987  
> **DOI:** [10.1016/j.mlwa.2026.100987](https://doi.org/10.1016/j.mlwa.2026.100987)

---

## Overview

The framework automates the alpha-discovery cycle with a **nested multi-agent architecture**, a deterministic backtesting engine, and two complementary knowledge bases:

- **Main Knowledge Base** — stores successful alpha discoveries for reuse in later generations.
- **101 Alpha Knowledge Base** — provides structural priors derived from the WorldQuant 101 Formulaic Alphas.

The system uses an **inner loop** to improve code quality before backtesting and an **outer loop** to evaluate financial performance, incorporate feedback, and accumulate the best qualifying alpha into the Main Knowledge Base.

---

## System Architecture

![Alpha Mining Architecture](templates/assets/icons/image.png)

| Component | Role |
| --- | --- |
| `WriterAgent` | Generates executable alpha code and its mathematical representation from a trading idea and retrieved context |
| `JudgeAgent` | Reviews code quality, trading logic, robustness, and look-ahead safety before backtesting |
| `BacktestEngine` | Executes factors on historical market data and computes quantitative performance metrics |
| `ReviewerAgent` | Interprets backtest results and provides feedback for subsequent outer-loop iterations |
| `Main KB` | Stores validated alpha records accumulated from previous discovery runs |
| `101 Alpha KB` | Supplies reusable formula structures and operator patterns from the WorldQuant 101 Formulaic Alphas |
| `KB Updater` | Stores the best alpha that satisfies the knowledge-base acceptance criterion |

---


## Experimental Setup

### Dataset

| Item | Value |
| --- | ---: |
| Initial stock symbols | 500 |
| Eligible stocks after coverage filtering | 449 |
| Minimum data coverage | 80% |
| Experiment period | 2021-01-04 to 2025-04-01 |
| Trading days | 1,057 |
| OHLCV records after filtering | 456,860 |
| Average coverage of eligible stocks | 96.3% |

### Chronological Split

| Phase | Trading Days | Date Range | Purpose |
| --- | ---: | --- | --- |
| Training / Alpha Discovery | 748 | 2021-01-04 to 2023-12-31 | Alpha discovery and Main-KB accumulation |
| Validation | 165 | 2024-01-01 to 2024-08-31 | Hyperparameter selection and ablation studies |
| Held-out Testing | 144 | 2024-09-01 to 2025-04-01 | Final out-of-sample evaluation |

During held-out testing, the Main KB is frozen and no further alpha accumulation or parameter tuning is performed.

### Default Configuration

| Parameter | Value |
| --- | ---: |
| Inner-loop iterations | 5 |
| Judge quality threshold | 7.5 / 10 |
| Outer-loop iterations | 5 |
| Main-KB IC threshold | 0.01 |
| KB retrieval Top-K | 1 |
| Forward-return horizon | 5 trading days |
| Signal clipping range | `[-5, 5]` |
| Liquidity filter | `vol_ratio >= 1.0` |

---

## Reported Results

### Alpha Discovery

| Metric | Result |
| --- | ---: |
| Held-out trading ideas | 10 |
| Ideas producing alpha with IC >= 0.01 | 9 |
| Average discovery time per idea | 1.5 min |

### Mean Held-Out Test Performance

| Metric | Proposed Framework |
| --- | ---: |
| IC | **0.0480** |
| ICIR | **0.4711** |
| TIC | **5.60** |
| Sharpe Ratio | **2.39** |
| Win Rate | **65.0%** |
| Maximum Drawdown | **7.8%** |
| Valid Ratio | **85.0%** |

### Comparison with AlphaForge

| Method | IC | ICIR | TIC | Sharpe | WR | MDD | VR |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| AlphaForge | 0.0337 | 0.3199 | 3.80 | 1.35 | 58.0% | 25.7% | 82.7% |
| **Ours** | **0.0480** | **0.4711** | **5.60** | **2.39** | **65.0%** | **7.8%** | **85.0%** |

These results are specific to the Vietnamese-equity dataset and evaluation protocol described in the paper and should not be interpreted as guaranteed live-trading performance.

---

## Web Interface

![Web Interface](templates/assets/icons/app.png)

The dashboard provides four main modules:

| Module | Description |
| --- | --- |
| **Alpha Mining** | Run the complete multi-agent alpha-mining pipeline from a natural-language trading idea |
| **Knowledge Base** | Browse, search, filter, and inspect accumulated alpha records |
| **Market Prediction** | Use selected factors as features for 5-day forward-return prediction |
| **Manual Backtest** | Execute custom alpha-factor code and inspect historical results |

The interface also supports real-time execution logs, generated-factor inspection, parameter configuration, and result analysis.

---

## Publication

**Minh-Son Vu, The-Trung Pham, and Hong-Viet Tran.**  
**“Self-improving alpha mining for quantitative trading via multi-agent large language models with knowledge base accumulation.”**  
*Machine Learning with Applications*, **25** (2026), Article **100987**.  
[https://doi.org/10.1016/j.mlwa.2026.100987](https://doi.org/10.1016/j.mlwa.2026.100987)

---

## Citation

If you use this repository or research in your work, please cite:

```bibtex
@article{vu2026self,
  title     = {Self-improving alpha mining for quantitative trading via multi-agent large language models with knowledge base accumulation},
  author    = {Vu, Minh-Son and Pham, The-Trung and Tran, Hong-Viet},
  journal   = {Machine Learning with Applications},
  volume    = {25},
  pages     = {100987},
  year      = {2026},
  publisher = {Elsevier},
  doi       = {10.1016/j.mlwa.2026.100987}
}
```

---

## Authors

**Minh-Son Vu · The-Trung Pham · Hong-Viet Tran**

Institute for Artificial Intelligence  
University of Engineering and Technology  
Vietnam National University, Hanoi  
Viet Nam

---

## Disclaimer

This repository is intended for **academic research and experimental quantitative analysis**. Generated factors, backtest results, and model outputs do not constitute investment advice or guarantees of future performance.
