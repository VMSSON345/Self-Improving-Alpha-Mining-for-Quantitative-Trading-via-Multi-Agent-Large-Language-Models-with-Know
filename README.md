# LLM-Based Alpha Mining with Knowledge Accumulation

An automated quantitative research system that leverages Large Language Models (LLMs) to **generate, evaluate, refine, and accumulate alpha factors** for the Vietnamese stock market.

This repository implements the framework introduced in our paper:

> **Self-improving alpha mining for quantitative trading via multi-agent large language models with knowledge base accumulation**
> Vu Minh-Son, Pham The-Trung, and Tran Hong-Viet
> *Machine Learning with Applications*, Elsevier, 2026, Article 100987.

---

## System Architecture

![Alpha Mining Architecture](templates/assets/icons/image.png)

The system follows a multi-agent iterative alpha-mining framework in which LLM agents generate candidate factors, evaluate their implementation, analyze backtesting results, and progressively improve future candidates using accumulated knowledge.

| Component        | Role                                                                                                               |
| ---------------- | ------------------------------------------------------------------------------------------------------------------ |
| `WriterAgent`    | Generates alpha-factor code from a natural-language trading idea and retrieved knowledge-base context              |
| `JudgeAgent`     | Reviews the generated alpha implementation before backtesting                                                      |
| `BacktestEngine` | Executes historical backtests and computes evaluation metrics such as IC, Sharpe Ratio, Win Rate, and Max Drawdown |
| `ReviewerAgent`  | Analyzes backtesting results and provides feedback for subsequent refinement rounds                                |
| `Main KB`        | Stores validated and high-quality alpha factors discovered in previous runs                                        |
| `101 Alpha KB`   | Provides reference patterns derived from the WorldQuant 101 Formulaic Alphas                                       |

---

## Alpha Mining Workflow

The alpha-mining process consists of iterative **inner-loop refinement** and **outer-loop knowledge accumulation**.

```text
Trading Idea
     │
     ▼
Knowledge Retrieval
     │
     ├── Main KB
     └── 101 Alpha KB
     │
     ▼
WriterAgent
     │
     ▼
JudgeAgent
     │
     ▼
BacktestEngine
     │
     ▼
ReviewerAgent
     │
     ├── Feedback → Inner Loop Refinement
     │
     └── Validated Alpha → Main KB
                         │
                         ▼
              Future Alpha Mining Runs
```

The framework allows previously discovered alpha factors and feedback to become reusable knowledge for future alpha-generation tasks.

---

## Knowledge Base Retrieval

At each refinement step, the system retrieves relevant information from two knowledge bases using semantic similarity.

Sentence embeddings are generated using:

```text
all-MiniLM-L6-v2
```

and candidate knowledge is ranked using cosine similarity.

The two knowledge sources are:

* **Main KB** — retrieves similar validated alpha factors discovered during previous mining runs.
* **101 Alpha KB** — retrieves relevant formula structures and patterns from the WorldQuant 101 Formulaic Alphas.

This retrieval mechanism provides the LLM agents with useful historical context instead of generating each alpha factor independently from scratch.

---

## Multi-Agent Framework

### WriterAgent

`WriterAgent` converts a natural-language trading hypothesis into executable alpha-factor code.

Its input may include:

* Trading idea
* Similar factors retrieved from the Main KB
* Relevant WorldQuant 101 Alpha patterns
* Feedback from previous refinement rounds

Its output is an implementation compatible with the system's `AlphaBase` interface.

### JudgeAgent

`JudgeAgent` performs a pre-backtest review of the generated alpha implementation.

It checks aspects such as:

* Code validity
* Logical consistency
* Compatibility with available market features
* Potential implementation problems

Only suitable candidates proceed to historical backtesting.

### BacktestEngine

The `BacktestEngine` evaluates each generated alpha factor on historical Vietnamese stock-market data.

Typical evaluation metrics include:

* Information Coefficient (IC)
* IC Information Ratio (ICIR)
* Sharpe Ratio
* Win Rate
* Maximum Drawdown
* Valid Ratio

### ReviewerAgent

`ReviewerAgent` analyzes the quantitative backtesting results and provides structured feedback.

The feedback is passed back to `WriterAgent`, allowing the alpha factor to be improved during subsequent inner-loop iterations.

Validated factors can then be stored in the Main KB for reuse in later alpha-mining runs.

---

## Web Interface

![Web Interface](templates/assets/icons/app.png)

The system provides a web-based dashboard for interacting with the complete alpha-mining pipeline.

Users can:

* Enter a trading idea in natural language
* Configure inner-loop (`T`) and outer-loop (`K`) parameters
* Start the alpha-mining process
* Monitor real-time execution logs
* Inspect generated alpha factors
* Analyze quantitative evaluation results
* Browse accumulated knowledge
* Run custom alpha factors manually
* Perform market-return prediction

### Dashboard Modules

| Tab                   | Description                                                                              |
| --------------------- | ---------------------------------------------------------------------------------------- |
| **Alpha Mining**      | Enter a trading idea and execute the full multi-agent alpha-mining pipeline              |
| **Knowledge Base**    | Browse, search, filter, and analyze stored alpha factors                                 |
| **Market Prediction** | Train a prediction model using selected alpha factors and forecast 5-day forward returns |
| **Manual Backtest**   | Test custom alpha-factor code and inspect backtesting results                            |

---

## Models

The current implementation uses the following models:

| Component       | Model                              |
| --------------- | ---------------------------------- |
| `WriterAgent`   | Llama-4-Scout-17B-16E via Groq API |
| `JudgeAgent`    | LLaMA-3.1-8B via Groq API          |
| `ReviewerAgent` | LLaMA-3.1-8B via Groq API          |
| Embedding Model | `all-MiniLM-L6-v2`                 |

---

## Alpha Interface

All generated alpha factors follow the `AlphaBase` interface.

Example:

```python
class MyAlpha(AlphaBase):
    inputs = ["close", "volume"]

    def calc(self, data):
        signal = ...  # Compute raw alpha signal

        signal = signal.clip(-5, 5)
        signal = signal.fillna(0)

        return signal
```

Each alpha defines:

* Required input features through `inputs`
* Factor computation logic inside `calc()`
* A standardized output signal compatible with the backtesting pipeline

---

## Key Features

* **LLM-Based Alpha Mining** — Generate quantitative alpha factors directly from natural-language trading hypotheses.
* **Multi-Agent Refinement** — Use specialized Writer, Judge, and Reviewer agents to iteratively improve generated factors.
* **Automated Backtesting** — Evaluate factors using IC, ICIR, Sharpe Ratio, Win Rate, Maximum Drawdown, and other quantitative metrics.
* **Knowledge Accumulation** — Store successful alpha factors and reuse them in future mining runs.
* **Semantic Knowledge Retrieval** — Retrieve related factors and formula patterns using sentence embeddings and cosine similarity.
* **WorldQuant Alpha Integration** — Use the 101 Formulaic Alphas as an external structural knowledge source.
* **Market Prediction** — Combine selected high-quality alpha factors for 5-day forward-return forecasting.
* **Manual Backtesting** — Test custom alpha implementations directly through the dashboard.
* **Interactive Dashboard** — Configure experiments and inspect results using a web-based interface.

---

## Research

This repository is associated with the following publication:

**Vu, Minh-Son, Pham, The-Trung, and Tran, Hong-Viet.**
**“Self-improving alpha mining for quantitative trading via multi-agent large language models with knowledge base accumulation.”**
*Machine Learning with Applications*, Elsevier, 2026, Article 100987.

If you use this repository or framework in your research, please cite:

```bibtex
@article{vu2026self,
  title={Self-improving alpha mining for quantitative trading via multi-agent large language models with knowledge base accumulation},
  author={Vu, Minh-Son and Pham, The-Trung and Tran, Hong-Viet},
  journal={Machine Learning with Applications},
  pages={100987},
  year={2026},
  publisher={Elsevier}
}
```

---

## Authors

* **Vu Minh Son** — 23020424
* **Pham The Trung** — 23020442

### Supervisor

**Dr. Tran Hong Viet**

Institute for Artificial Intelligence
University of Engineering and Technology
Vietnam National University, Hanoi
Vietnam

---

## Citation

If this project is useful for your work, please consider citing our paper:

```bibtex
@article{vu2026self,
  title={Self-improving alpha mining for quantitative trading via multi-agent large language models with knowledge base accumulation},
  author={Vu, Minh-Son and Pham, The-Trung and Tran, Hong-Viet},
  journal={Machine Learning with Applications},
  pages={100987},
  year={2026},
  publisher={Elsevier}
}
```
