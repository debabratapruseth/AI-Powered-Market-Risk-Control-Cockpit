![Banner](https://github.com/debabratapruseth/AI-Powered-Market-Risk-Control-Cockpit/blob/main/Market%20Cokpit.png)

# AI-Powered Market Risk Control Cockpit

> Detect bad market data • Prioritise business impact • Generate AI investigation reports

An end-to-end Python project demonstrating how **Machine Learning**, **Rule-Based Controls**, and **Generative AI** can be combined to build an intelligent **Market Data Quality & Market Risk Control Framework** for financial institutions.

The project simulates how investment banks can automatically detect anomalous FX market data, estimate business impact, prioritise operational risk, and generate analyst-ready investigation reports.

---

## Companion Blog

A detailed walkthrough of the architecture, implementation, and AI concepts is available here:

👉 **https://debabratapruseth.com/build-ai-market-risk-control-cockpit-python/**

---

# Why This Project?

Every day, banks consume millions of market prices from vendors like Bloomberg, Reuters and internal pricing systems.

These prices drive:

- Foreign Exchange Trading
- Value at Risk (VaR)
- Daily P&L
- Regulatory Capital
- Stress Testing
- Treasury Operations
- Derivatives Pricing

A single incorrect market price can propagate across multiple downstream systems resulting in:

- Incorrect valuations
- False VaR movements
- Trading losses
- Regulatory reporting issues
- Operational risk

Traditional rule engines generate thousands of alerts every day.

This project demonstrates how AI can **augment—not replace—existing control frameworks** by helping analysts identify which alerts actually matter.

---

# Key Features

✅ Synthetic FX market data generation

✅ Multi-vendor market data simulation

✅ Data Quality Validation

✅ Feature Engineering

✅ Rule-Based Detection

- Stale Prices
- Vendor Disagreement
- Feed Latency
- Missing Data
- Extreme Price Movement

✅ Machine Learning Anomaly Detection

- Isolation Forest

✅ Market Context Enrichment

- Historical similarity
- Peer instrument comparison
- Confidence scoring

✅ Business Impact Prioritisation

- Portfolio exposure
- Scenario-based P&L estimation
- Simplified VaR impact

✅ AI Investigation Reports

- LLM-generated analyst summaries
- Root cause explanation
- Recommended next actions

✅ Interactive Streamlit Dashboard



# Technology Stack

| Component | Technology |
|-----------|------------|
| Language | Python |
| Data Processing | Pandas, NumPy |
| Machine Learning | Scikit-learn |
| Visualization | Plotly |
| Dashboard | Streamlit |
| AI | OpenAI / Ollama / Local LLM |
| Charts | Matplotlib, Seaborn |

---

# AI Techniques Used

### Rule-Based Controls

Deterministic market data validation rules:

- Stale Price Detection
- Vendor Difference
- Feed Latency
- Missing Values
- Statistical Thresholds

---

### Machine Learning

The project uses **Isolation Forest** for unsupervised anomaly detection.

Instead of relying on predefined thresholds, Isolation Forest learns what *normal* market behaviour looks like and identifies statistically unusual observations.

---

### Feature Engineering

Example engineered features include:

- Price Change
- Rolling Mean
- Rolling Volatility
- Z-Score
- Feed Latency
- Vendor Difference
- Market Session
- Portfolio Exposure

---

### Generative AI

Large Language Models convert structured alerts into human-readable investigation reports.

Typical outputs include:

- Executive Summary
- Root Cause
- Business Impact
- Confidence
- Recommended Actions

---

# Business Impact Prioritisation

The project combines multiple signals into a unified business priority score.

Inputs include:

- Rule Severity
- AI Severity
- Confidence Score
- Portfolio Exposure
- Estimated Scenario P&L

The objective is simple:

> Show analysts **the five alerts that matter most**, instead of overwhelming them with hundreds of low-value notifications.

---


# Future Enhancements

This project intentionally focuses on **Foreign Exchange (FX)**.

The same architecture can easily be extended to:

- Fixed Income
- Government Bonds
- Interest Rate Curves
- Equities
- Commodities
- Credit Spreads
- Derivatives
- Market Reference Data

Future roadmap:

- Kafka Streaming
- Real-Time Processing
- Vector Database
- RAG for Historical Incidents
- AI Agents
- Multi-Agent Investigation Workflow
- MLOps Pipeline
- Model Monitoring
- Cloud Deployment

---

# Disclaimer

This repository is intended for **educational purposes** and **AI prototyping**.

The simplified VaR, scenario analysis, and business impact calculations included in this project are designed to demonstrate architectural concepts only.

This project **does not implement production-grade market risk models**, regulatory VaR, Expected Shortfall, pricing engines, or Basel-compliant calculations.

It should **not** be used for trading, valuation, regulatory reporting, or production risk management without appropriate governance, validation, and model approval.

---

# Contributing

Contributions are welcome!

If you'd like to improve the project, feel free to:

- Open an Issue
- Submit a Pull Request
- Suggest new market risk controls
- Add support for additional asset classes
- Improve AI explainability
- Extend dashboard capabilities

---

# License

MIT License

---

## ⭐ If you found this project useful, consider giving it a Star!
