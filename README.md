# ⬡ AI-Powered Data Quality & Fraud Detection Pipeline

> Enterprise-grade anomaly detection and data governance system — deployable in one click on Hugging Face Spaces.

<img width="1346" height="602" alt="image" src="https://github.com/user-attachments/assets/a72de6d9-6a70-43a2-b2d3-cd37a83260ec" />

<img width="1351" height="606" alt="image" src="https://github.com/user-attachments/assets/d60b7ef2-08a3-4f00-9ca5-e4883cfe0afc" />
---

## What This Is

A production-style data quality and fraud detection pipeline built for:

- **Banking compliance** — flag suspicious transactions before downstream processing
- **Fintech risk monitoring** — detect structurally anomalous records in real time
- **Enterprise data governance** — generate audit-ready reports with every batch run

Upload any CSV. The system analyzes data quality, runs dual-method anomaly detection, explains every flagged record in plain English, and gives you cleaned data + a compliance report to download.

---

## Live Demo

[![Open in HuggingFace Spaces](https://img.shields.io/badge/🤗%20Open%20in-Spaces-blue)](https://huggingface.co/spaces/YOUR_USERNAME/data-quality-pipeline)

---

## Architecture

```
CSV Upload
    │
    ▼
data_loader()         Validates file, parses CSV, early-exit on bad input
    │
    ▼
clean_data()          Missing value analysis (per-column severity)
                      Duplicate detection and removal
                      Median/mode imputation
                      Descriptive stats summary
    │
    ▼
detect_anomalies()    ┌─ Isolation Forest (structural outliers)
                      │  200 estimators, dynamic contamination
                      └─ Z-Score >3.5σ (statistical outliers per column)
                      
                      Combines signals → HIGH / MEDIUM / CLEAN risk tier
    │
    ▼
explain_results()     Rule-based NLG engine
                      Per-record plain-English explanations
                      Compliance severity ratings
                      Actionable remediation steps
    │
    ▼
generate_report()     cleaned_dataset.csv
                      flagged_records.csv
                      compliance_report.txt  (timestamped, audit-ready)
```

### Why Dual-Method Detection?

Single-method anomaly detection produces too many false positives for production use. This pipeline uses a **consensus approach**:

| Signal | Method | Catches |
|---|---|---|
| Structural outlier | Isolation Forest | Unusual *combinations* of features |
| Statistical outlier | Z-Score (3.5σ) | Extreme values in individual columns |
| **HIGH risk** | Both agree | Highest confidence flags |
| **MEDIUM risk** | One method | Needs human review |
| **CLEAN** | Neither | Safe for downstream use |

---

## Features

- **CSV upload** with instant preview
- **Data quality report** — missing values (with severity), duplicates, column stats
- **Anomaly detection** — IsolationForest + Z-Score dual engine
- **Risk tiering** — HIGH / MEDIUM / CLEAN per record
- **Plain-English explanations** — why each record was flagged
- **3 downloadable outputs** — cleaned data, flagged records, compliance report
- **Audit-ready format** — UTC timestamps, record IDs, method attribution

---

## Project Structure

```
├── app.py                   # Full pipeline + Gradio UI (single file)
├── requirements.txt         # Python dependencies
├── sample_transactions.csv  # Test dataset (50 rows, planted anomalies)
└── README.md
```

The code is intentionally single-file to be copy-paste deployable on Hugging Face Spaces without a build system.

### Module breakdown inside `app.py`

```python
data_loader()        # File validation and CSV parsing
clean_data()         # Quality checks + imputation → returns (df, quality_report)
detect_anomalies()   # Dual-method detection → returns (flagged_df, anomaly_rows, meta)
explain_results()    # NLG explanation engine → returns report string
generate_report()    # File writer → returns (clean_path, report_path, anomaly_path)
run_pipeline()       # Orchestrator called by Gradio button
build_ui()           # Gradio layout and wiring
```

---

## Quickstart

### Option A — Hugging Face Spaces (recommended, zero setup)

1. Go to [huggingface.co/new-space](https://huggingface.co/new-space)
2. Name: `data-quality-pipeline` | SDK: **Gradio** | Hardware: CPU Basic (free)
3. Click **Files** → **Add file** → upload `app.py` and `requirements.txt`
4. HuggingFace builds automatically (~90 seconds)
5. Upload `sample_transactions.csv` in the app to test

### Option B — Run locally

```bash
git clone https://github.com/YOUR_USERNAME/data-quality-pipeline
cd data-quality-pipeline

pip install -r requirements.txt
python app.py
# Open http://localhost:7860
```

---

## Sample Dataset

`sample_transactions.csv` contains 50 synthetic banking transactions with planted anomalies:

| Record | Why It's Anomalous |
|---|---|
| TXN007 | $18,750 wire, 8-day-old account, 89 transactions in 24h, device risk 0.99 |
| TXN026 | $14,500 wire transfer to Russia, 6-day-old account |
| TXN042 | $99,999 wire, 2-day-old account, 95 transactions/24h — **HIGH RISK** |
| TXN033 | $5,200 crypto, account age 9 days, device risk 0.93 |

Expected output: 1 HIGH risk, 2–3 MEDIUM risk records flagged.

---

## Dependencies

```
gradio>=4.0.0        # UI framework
pandas>=2.0.0        # Data processing
numpy>=1.24.0        # Numeric operations
scikit-learn>=1.3.0  # IsolationForest
scipy>=1.11.0        # Z-score / stats
```

No GPU required. Runs on any CPU environment.

---

## Extending This Project

**Add LLM explanations** — call HuggingFace Inference API with `requests` and use a quantized Mistral/Llama model to generate richer per-record narratives. Fall back to rule-based if the API call fails.

**Add batch processing** — wrap the pipeline in a loop to process multiple CSVs and aggregate results into a single report.

**Add a REST API** — expose `run_pipeline()` as a FastAPI endpoint for integration with upstream data warehouses or ETL systems.

**Add retraining feedback** — log analyst accept/reject decisions on flagged records. Once you have 200+ labeled examples, switch from unsupervised IsolationForest to supervised XGBoost with SHAP explanations.

**Production deployment** — containerize with Docker, deploy on AWS ECS or GCP Cloud Run, add PostgreSQL for result persistence and S3 for output storage.

---

## Interview Q&A

See the [full list of 10 interview questions and answers](./INTERVIEW.md) this project is designed to answer.

---

## License

MIT — free to use, modify, and deploy.

---

*Built as a production-style AI engineering portfolio project. Demonstrates end-to-end ML pipeline ownership: data ingestion, quality analysis, anomaly detection, explanation generation, and output delivery.*
