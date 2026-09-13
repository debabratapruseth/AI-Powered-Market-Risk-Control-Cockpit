# AI-Powered Market Risk Control Cockpit

An explainable, configuration-driven prototype that transforms market-data control signals into a business-prioritized investigation queue using deterministic controls, machine learning, contextual evidence, risk-impact assessment and governed Generative AI.

> **Python calculates. ML detects. LLM explains. Humans decide.**

---

## 1. Overview

Market Risk and Market Data teams can receive large volumes of technical alerts. A technically severe anomaly, however, is not necessarily the most important issue from a business-risk perspective.

The **AI-Powered Market Risk Control Cockpit** demonstrates a layered approach:

**Market Data → Rule Controls + ML Detection + Context → Evidence Confidence → Business Impact → Final Priority → Required Action**

The prototype combines:

- deterministic market-data controls for known issues;
- Isolation Forest for unsupervised multivariate anomaly detection;
- vendor, peer and historical context;
- explainable Evidence Confidence;
- business-impact prioritization using exposure and portfolio-risk context;
- governed investigation actions;
- optional OpenAI-generated investigation narratives;
- a natural-language Risk Copilot;
- a Streamlit analyst cockpit.

The current implementation uses **synthetic FX market data** for demonstration. The architecture is designed so the same control pattern can be extended to other market-risk domains and enterprise data sources.

This is a prototype decision-support system. It is **not a production trading system, regulatory VaR engine or Expected Shortfall implementation**.

---

## 2. Architecture

The analytical flow is deliberately layered.

```text
                         Synthetic FX Market Data
                                  |
                                  v
                         Feature Enrichment
                                  |
                 +----------------+----------------+
                 |                |                |
                 v                v                v
          Rule Controls      ML Detection       Context
                              Isolation         Analysis
                               Forest
                 |                |                |
                 +----------------+----------------+
                                  |
                                  v
                         Evidence Confidence
                                  |
                                  v
                          Business Impact
                                  |
                                  v
                           Final Priority
                                  |
                                  v
                          Required Action
                                  |
                                  v
                        Investigation Pack
                                  |
                       +----------+----------+
                       |                     |
                       v                     v
                 GPT Investigation       Streamlit
                     Narrative             Cockpit
                                             |
                                             v
                                        Risk Copilot
```

Rules, ML and contextual analysis provide different types of evidence.

A key design principle is:

> **Technical detection severity and final business priority are different.**

A Critical technical anomaly therefore does not automatically become a Critical business-priority investigation.

---

## 3. Synthetic Data

The prototype generates controlled synthetic datasets so the complete analytical workflow can be demonstrated without using confidential bank or market data.

Four source files are created under:

```text
data/raw/
```

| File | Purpose |
| --- | --- |
| `market_data.csv` | Simulated Reuters and Bloomberg FX prices over 30 days, including deliberately injected stale prices and unusual movements for control testing |
| `exposures.csv` | Portfolio exposures by currency pair, synthetic Portfolio Risk Contribution and criticality |
| `historical_alerts.csv` | Example historical alerts, resolutions and recorded root causes |
| `scenarios.csv` | Illustrative stress scenarios and percentage shocks |

A fixed random seed makes the synthetic numerical values reproducible.

### Reproducible timestamp control

Synthetic history is also dependent on time. The Asset Control Pack therefore controls the history endpoint through:

```yaml
data_generation:
  as_of: "2026-09-12 20:27:20.031382"
```

A fixed timestamp produces a repeatable synthetic dataset and stable observation/investigation identities.

To generate a fresh synthetic history using the current system time:

```yaml
data_generation:
  as_of: system
```

Conceptually:

```text
Fixed as_of
    ↓
Repeatable synthetic history
    ↓
Same observations
    ↓
Same Observation IDs
    ↓
Same Investigation IDs
```

Whereas:

```text
as_of: system
    ↓
Current system timestamp
    ↓
New synthetic history
    ↓
New observation timestamps
    ↓
New Investigation IDs
```

The fixed timestamp is therefore useful for reproducible demonstrations, regression testing and reuse of saved investigation reports.

---

## 4. Raw and Processed Data

### Raw datasets

The four raw datasets are generated in:

```text
data/raw/
```

They represent the controlled inputs to the analytical pipeline.

### Processed datasets

Files under:

```text
data/processed/
```

progressively preserve and enrich the original observations.

| File | What is added |
| --- | --- |
| `enriched_market_data.csv` | Price movements, rolling statistics, Z-scores, source differences, simulated feed latency, market-session information and portfolio exposures |
| `market_data_with_rules.csv` | Rule breaches, triggered-control descriptions, technical severity scores and rule evidence |
| `rule_observation_alerts.csv` | Rule-triggered market observations without portfolio duplication |
| `rule_portfolio_alerts.csv` | Rule alerts linked to affected portfolios |
| `market_data_with_ai.csv` | Isolation Forest anomaly detection, ML severity and explanatory feature signals |
| `context_observations.csv` | Observation-level vendor, peer and historical context plus Evidence Confidence |
| `market_data_with_context.csv` | Contextual results linked back to affected portfolios |
| `market_data_prioritised.csv` | Business Impact, Final Priority, configured recommendations and illustrative scenario financial impacts |
| `investigation_pack.csv` | Detected investigations with stable IDs, consolidated evidence, Indicative Root Cause, Required Action and structured JSON for AI reporting |

The processed datasets do not replace the underlying market prices. They progressively add control, ML, contextual and business-risk intelligence to the same observations.

---

## 5. Deterministic Rule Controls

The Rule Engine detects known market-data conditions using configured thresholds.

Current control types include:

- Stale Price
- Large Price Movement
- Statistical Outlier / Z-Score
- Source Divergence
- High Feed Latency

Rules are first evaluated at the unique market-observation level and are then linked to affected portfolios. This avoids treating the same underlying market observation as multiple independent detections simply because several portfolios are exposed to it.

Rule outputs include:

- triggered controls;
- Rule Severity Score;
- Rule Severity;
- deterministic Rule Evidence.

Rule methodology and thresholds are configuration-driven through the Asset Control Pack.

---

## 6. Machine-Learning Anomaly Detection

The ML layer uses **Isolation Forest** for unsupervised multivariate anomaly detection.

Current model features include:

- Price Change (%)
- Z-Score
- Source Difference
- Feed Latency

The ML layer produces:

- anomaly classification;
- anomaly score;
- ML Severity Score;
- ML Severity;
- explanatory feature signals.

The model identifies unusual multivariate patterns. It is **not a forecasting model** and does not predict future market prices.

Explanatory thresholds describe notable feature conditions associated with an ML-detected anomaly. They do not independently determine the Isolation Forest classification.

---

## 7. Market Context

Detection alone does not determine business importance.

The contextual layer examines surrounding evidence including:

### Vendor Agreement

Assesses whether Reuters and Bloomberg remain sufficiently aligned.

### Peer Behaviour

Compares the observation's percentage movement with contemporaneous FX peer movement.

### Market-Wide Move

Identifies whether broader FX markets are moving unusually at the same time.

Market-Wide Move is interpretive context and does **not** directly contribute to Evidence Confidence.

### Historical Recurrence

Looks for similar historical alert categories and previously recorded root causes.

Historical root cause is kept separate from the current investigation's **Indicative Root Cause**.

---

## 8. Evidence Confidence

Evidence Confidence is a **0–100 contextual corroboration score**.

It answers:

> **How strongly does surrounding evidence support the detected observation?**

It is not a probability.

Current contextual evidence components are:

| Component | Point budget | Maximum normalized contribution |
| --- | ---: | ---: |
| Vendor Disagreement | 20 | 44.4 |
| Peer Deviation | 15 | 33.3 |
| Historical Recurrence | 10 | 22.2 |

The relative budgets are normalized to a maximum Evidence Confidence of 100.

Rule and ML detection are deliberately not counted again in Evidence Confidence because their technical severity is scored separately.

Market-Wide Move provides additional interpretive context but does not enter the Evidence Confidence calculation.

---

## 9. Business Impact

Business Impact combines technical severity, contextual evidence and business materiality into a 0–100 prioritization score.

Current components are:

| Component | Point budget | Maximum normalized contribution |
| --- | ---: | ---: |
| Rule Severity | 25 | 28.1 |
| ML Severity | 20 | 22.5 |
| Evidence Confidence | 20 | 22.5 |
| Exposure | 20 | 22.5 |
| Portfolio Risk Contribution | 4 | 4.5 |

Business Impact is a **prototype prioritization score**.

It is not regulatory VaR or Expected Shortfall.

Scenario P&L and VaR-impact fields are illustrative analytical outputs and do not determine Business Impact or Final Priority.

---

## 10. Priority and Action Governance

Business Impact determines Final Priority.

Current priority bands are:

| Business Impact | Final Priority |
| --- | --- |
| >= 85 | Critical |
| >= 70 and < 85 | High |
| >= 50 and < 70 | Medium |
| >= 30 and < 50 | Low |
| < 30 | Monitor |

Final Priority then determines the configured control action.

The governance chain is therefore:

```text
Detection
   ↓
Supporting Context
   ↓
Business Impact
   ↓
Final Priority
   ↓
Required Action
```

Or more simply:

> **Business Impact determines Final Priority. Final Priority determines the required Control Action.**

---

## 11. Investigation Pack

Detected observations are consolidated into:

```text
data/processed/investigation_pack.csv
```

Each investigation contains:

- stable `investigation_id`;
- observation identity;
- timestamp;
- Risk Factor;
- portfolio;
- rule evidence;
- ML evidence;
- market context;
- Evidence Confidence;
- Business Impact;
- Final Priority;
- Indicative Root Cause;
- Control Recommendation;
- Required Action;
- structured `InvestigationJSON`.

Investigation IDs are deterministic for identical identifying inputs.

They do not depend on machine-specific Python hashing, random UUIDs, analytical scores, GPT output or row position.

This enables stable linkage between analytical evidence and saved investigation narratives when the same synthetic dataset is reproduced.

---

## 12. Governed Generative AI

Generative AI is used only after deterministic analytical processing.

The analytical pipeline calculates the evidence and priority. The LLM does not recalculate or override those values.

The master investigation prompt is configuration-controlled and instructs the model to use only supplied structured evidence.

The model must not invent:

- market events;
- causes;
- calculations;
- scores;
- priorities;
- actions;
- external facts.

The generated report separates:

1. Executive Assessment
2. Indicative Root Cause
3. Business Impact Assessment
4. Existing Control Action
5. AI-Suggested Investigation Steps
6. Overall Assessment

Existing Control Action comes from deterministic configuration.

AI-Suggested Investigation Steps are optional decision-support suggestions for human review.

> **Analytics calculates. Configuration governs. GenAI explains. Human reviews.**

---

## 13. Risk Copilot

Risk Copilot provides a natural-language interface over the processed market-control dataset.

The current architecture is:

```text
Natural-language question
        ↓
LLM structured query interpretation
        ↓
Python validation
        ↓
Deterministic read-only pandas execution
        ↓
Grounded result
        ↓
Optional LLM summary
```

The current implementation does not provide unrestricted SQL execution.

The query layer is read-only and validates structured requests before applying them to the dataset.

The currently connected source is:

- Market Data

The architecture can subsequently be extended to additional governed enterprise sources such as:

- Positions & Exposure
- Trade Data
- Risk & Limits
- Historical Alerts

No such source should be interpreted as connected unless it is actually implemented.

---

## 14. Streamlit Cockpit

`app.py` provides the presentation and investigation layer.

The cockpit includes:

### Control Overview

Provides management-level control visibility, filtering, priority distribution, investigation queue and market-data visualization.

### Alert Investigation

Provides investigation-level drill-down using three layers:

1. Assessment & Actions
2. Detection Evidence
3. Market Context

This keeps the business conclusion visible first while preserving detailed technical evidence and configuration traceability.

### Ask Risk Copilot

Provides governed natural-language interrogation of the connected market-data control dataset.

### Control Configuration

Shows the control, ML, context, Evidence Confidence, Business Impact, priority/action and governance settings used by the prototype.

---

## 15. Market Data Trend

The Control Overview includes a simple market-data visualization using the raw synthetic vendor feeds.

For a selected Risk Factor it displays:

- Reuters Price
- Bloomberg Price
- Timestamp

across the available synthetic history.

The chart intentionally displays the underlying price trend without changing, smoothing, resampling or aggregating the source observations.

This helps distinguish:

**market data being analysed**

from

**control and AI intelligence subsequently added by the pipeline.**

---

## 16. Standalone Data Quality Audit

The repository includes a standalone read-only data-quality and statistics audit:

```bash
python data_quality_audit.py
```

The audit does not execute the analytical pipeline, regenerate data, modify source files or call OpenAI.

Its primary focus is validation of the synthetic raw datasets, including:

- dataset structure;
- missing values;
- duplicates;
- timestamp integrity;
- hourly continuity;
- Risk Factor coverage;
- vendor-price statistics;
- Reuters/Bloomberg comparison;
- deliberately injected synthetic test conditions;
- exposure integrity;
- historical-alert structure;
- scenario structure.

It also performs structural and lineage checks across processed datasets.

Intentional stale-price and outlier injections are reported as **test conditions**, not automatically classified as data-quality failures.

Synthetic data quality therefore means that the dataset is structurally sound, internally consistent, reproducible and suitable for controlled testing. It does not imply that synthetic prices are genuine observed market prices.

---

## 17. Local Analytical Pipeline

`run_pipeline.py` runs the analytical processing layer.

`app.py` remains the separate Streamlit presentation and investigation layer.

The local implementation follows the original config-driven notebook architecture but does not execute the notebook at runtime.

### Setup

Python 3.11 or newer is recommended.

```bash
python3 -m venv .venv
source .venv/bin/activate

python -m pip install \
    pandas \
    numpy \
    scikit-learn \
    PyYAML \
    openai \
    streamlit \
    plotly \
    jsonschema
```

---

## 18. Running the Analytical Pipeline

Run without OpenAI:

```bash
python run_pipeline.py
```

For command options:

```bash
python run_pipeline.py --help
```

The pipeline regenerates the synthetic sources and analytical CSV outputs.

Do not run the original notebook and local pipeline concurrently against the same output directory.

If an analytical stage fails, resolve the failure and rerun the pipeline before refreshing the cockpit.

---

## 19. Optional GPT Investigation Reports

To generate GPT investigation reports:

```bash
python run_pipeline.py --generate-ai-reports
```

AI reporting requires the corresponding GenAI configuration to be enabled.

The reporting layer uses the configured master investigation prompt and sends the saved structured investigation evidence to the model.

OpenAI credentials are resolved in this order:

1. A nonempty `OPENAI_API_KEY` environment variable.
2. Otherwise, the root `OPENAI_API_KEY` entry in `.streamlit/secrets.toml`.
3. Otherwise, AI reporting is skipped with a clear message while analytical outputs are preserved.

Secrets are read only and should never be committed to source control.

Existing compatible reports may be reused for investigations with matching identities and analytical evidence.

A fresh `system`-timestamp synthetic run creates new observation identities and therefore may require new GPT reports.

---

## 20. Running the Cockpit

Set the project root:

```bash
export MARKET_RISK_BASE_PATH="$PWD"
```

Then launch:

```bash
streamlit run app.py
```

The Streamlit application reads the generated analytical outputs separately from the analytical pipeline.

---

## 21. Notebook-to-Python Mapping

| Notebook block | Local implementation |
| --- | --- |
| 1: setup, pack validation, seeds, audit | `run_pipeline.py`, `src/config_loader.py` |
| 2: synthetic sources | `src/data_generation.py` |
| 3: enrichment and features | `src/enrichment.py` |
| 4: unique-observation rules, severity, evidence, quality gates, portfolio merge | `src/rule_engine.py` |
| 5: scaling, Isolation Forest, anomaly scores | `src/anomaly_detection.py` |
| 6: market and historical context, Evidence Confidence | `src/market_context.py` |
| 7: scenarios, Business Impact and Final Priority | `src/business_impact.py` |
| 8: Indicative Root Cause and Investigation Pack | `src/investigation.py` |
| 9: optional governed GPT reports | `src/ai_reporting.py` |

Each stage retains explicit analytical handoffs through CSV outputs.

The YAML Asset Control Pack remains the authoritative configuration source for configured thresholds, scoring and governance values.

---

## 22. Reproducibility

A fixed random seed reproduces the synthetic numerical generation.

Time is controlled separately through:

```yaml
data_generation:
  as_of:
```

For the reproducible hackathon dataset:

```yaml
data_generation:
  as_of: "2026-09-12 20:27:20.031382"
```

This exact endpoint has been validated against the saved synthetic dataset.

The generator produces a shared hourly time grid of **720 observations for each of five Risk Factors**.

For the validated reference dataset:

| Raw dataset | Rows |
| --- | ---: |
| `market_data.csv` | 3,600 |
| `exposures.csv` | 20 |
| `historical_alerts.csv` | 50 |
| `scenarios.csv` | 10 |

The market-data history runs from:

```text
2026-08-13 21:27:20.031382
```

to:

```text
2026-09-12 20:27:20.031382
```

Explicit regeneration using the fixed timestamp and seed reproduced all four raw datasets exactly.

Validation confirmed:

- `market_data.csv`: 100% byte-identical;
- `exposures.csv`: 100% byte-identical;
- `historical_alerts.csv`: 100% byte-identical;
- `scenarios.csv`: 100% byte-identical;
- Observation IDs: 100% identical.

This confirms that the fixed `as_of` timestamp, together with the existing random seed and generation methodology, provides a reproducible synthetic dataset.

To generate a fresh history instead:

```yaml
data_generation:
  as_of: system
```

In `system` mode, current time becomes part of the generated history. New timestamps therefore intentionally result in new Observation IDs and Investigation IDs.

---

## 23. Regression Testing

`regression_check.py` can compare a candidate analytical run with a saved reference project.

Example:

```bash
python regression_check.py \
    --baseline /path/to/reference_project \
    --candidate "$PWD"
```

The regression checker is read-only.

It compares analytical/source/report artifacts for items such as:

- file existence;
- row counts;
- ordered schemas;
- identifiers;
- categorical values;
- numerical values;
- priorities;
- detection counts;
- structured investigation evidence.

Timestamp and identifier differences are expected when comparing a fixed reference dataset with a fresh `system`-timestamp simulation.

For meaningful deterministic comparison, use the same:

- code;
- configuration;
- dependency environment;
- random seeds;
- `data_generation.as_of`.

---

## 24. Repository Structure

```text
.
├── app.py
├── run_pipeline.py
├── data_quality_audit.py
├── regression_check.py
│
├── src/
│   ├── config_loader.py
│   ├── data_generation.py
│   ├── enrichment.py
│   ├── rule_engine.py
│   ├── anomaly_detection.py
│   ├── market_context.py
│   ├── business_impact.py
│   ├── investigation.py
│   └── ai_reporting.py
│
├── config/
│   ├── asset_packs/
│   │   └── fx.yaml
│   └── prompts/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── reports/
│
└── audit/
```

---

## 25. Governance Principles

The prototype follows several deliberate control principles.

### Deterministic analytics remain authoritative

The LLM does not calculate or override analytical scores.

### Configuration is separated from execution

Control thresholds, scoring and actions are governed through configuration.

### Known and unknown patterns are treated differently

Rules identify known control conditions; ML identifies unusual multivariate patterns.

### Technical severity is separated from business priority

A severe technical anomaly may not represent the highest business-risk investigation.

### Context is explicit

Vendor, peer and historical evidence are visible rather than hidden inside an opaque model score.

### Actions are governed

Final Priority determines the configured Required Action.

### GenAI is grounded

Investigation narratives use supplied structured evidence and remain decision-support outputs.

### Human review remains central

The cockpit prioritizes and explains; it does not autonomously execute Market Risk decisions.

---

## 26. Prototype Scope and Limitations

This repository is a hackathon/research prototype.

The current implementation uses:

- synthetic market data;
- simplified portfolio exposure information;
- synthetic Portfolio Risk Contribution;
- illustrative stress scenarios;
- prototype P&L and VaR-impact estimates;
- unsupervised anomaly detection;
- optional LLM-generated investigation narratives.

It does not claim to provide:

- regulatory VaR;
- Expected Shortfall;
- Basel capital calculations;
- production market-data validation;
- autonomous trading decisions;
- autonomous risk-control decisions;
- causal root-cause determination.

`LikelyRootCause`, displayed in the cockpit as **Indicative Root Cause**, represents analytical decision-support evidence rather than proven causality.

---

## 27. Extending the Prototype

The current FX implementation demonstrates a reusable control pattern.

Potential extensions include:

- Fixed Income
- Equities
- Commodities
- Credit Spreads
- Derivatives
- additional market-data vendors
- positions and exposure platforms
- trade data
- risk limits
- historical investigation systems
- analyst workflow integration

The objective is not simply to detect more anomalies.

The objective is to help Risk teams identify:

> **What happened, how strongly the evidence supports it, why it matters, what should be investigated first, and what action is required.**

---

## 28. Important Prototype Disclaimer

This project is intended for **demonstration, research and hackathon purposes**.

All market data, portfolio exposures, historical alerts, risk contributions and scenarios used by the included demonstration dataset are synthetic.

Outputs should not be interpreted as trading advice, regulatory risk measures, production control decisions or evidence of actual market events.

The prototype demonstrates an architecture for combining deterministic controls, machine learning, contextual analytics, business prioritization and governed Generative AI while retaining human review and explainability.