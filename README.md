# Stock Market ML Pipeline

An end-to-end machine learning pipeline for stock market data — data ingestion, model training/tracking, and serving predictions via a REST API, built with reproducibility and deployment in mind.

> **Note:** this README is drafted from the project's `Dockerfile`, `requirements.txt`, `.gitignore`, and `.dvcignore` — the `src/`, `k8/`, and `.dvc/` contents weren't available when this was written. Sections marked ⚠️ are inferred from the dependency stack and should be confirmed/edited once the actual source code is in hand.

## Tech Stack

| Purpose | Tool |
|---|---|
| Market data ingestion | [`yfinance`](https://pypi.org/project/yfinance/) |
| Data manipulation | `pandas`, `numpy` |
| Regime/sequence modeling | [`hmmlearn`](https://pypi.org/project/hmmlearn/) (Hidden Markov Models) |
| Deep learning | `torch` (PyTorch) |
| Classical ML | `scikit-learn` |
| Experiment tracking | [`mlflow`](https://mlflow.org/) |
| Data & model versioning | [`dvc`](https://dvc.org/) |
| API serving | `fastapi` + `uvicorn` |
| Containerization | Docker |
| Deployment / orchestration | Kubernetes (`k8/`) |

## ⚠️ Project Structure (inferred)

```
.
├── src/
│   ├── api/
│   │   └── app.py          # FastAPI app entrypoint (uvicorn src.api.app:app)
│   ├── data/                # (git-ignored) raw/processed data, tracked via DVC
│   └── models/              # (git-ignored) trained model artifacts, tracked via DVC
├── k8/                      # Kubernetes manifests (deployment, service, etc.)
├── .dvc/                    # DVC internal config
├── .dvcignore
├── .gitignore
├── Dockerfile
├── requirements.txt
└── README.md
```

`src/data/` and `src/models/` are excluded from Git and tracked with DVC instead — see `.gitignore`.

## ⚠️ What This Project Likely Does

Based on the dependencies:
1. **Ingests** historical stock price data via `yfinance`.
2. **Trains** a market model — possibly a Hidden Markov Model (`hmmlearn`) for detecting market regimes (bull/bear/volatile states) and/or a PyTorch model for price/return prediction.
3. **Tracks experiments** (parameters, metrics, model artifacts) with MLflow.
4. **Versions data and model artifacts** with DVC, so pipelines are reproducible without committing large files to Git.
5. **Serves predictions** through a FastAPI app (`src/api/app.py`), exposed on port `8000`.
6. **Deploys** via Docker, orchestrated with Kubernetes manifests in `k8/`.

*(Replace this section with the actual pipeline description once `src/` is available — e.g. what the model predicts, input features, and endpoint behavior.)*

## Requirements

```bash
pip install -r requirements.txt
```

Key versions:
- Python 3.10 (per Dockerfile base image)
- `mlflow==2.12.1`, `yfinance==0.2.37`, `pandas==2.2.1`, `numpy==1.26.4`
- `hmmlearn==0.3.2`, `scikit-learn==1.4.2`, `torch==2.2.1`
- `dvc==3.48.4`
- `fastapi==0.104.1`, `uvicorn[standard]==0.24.0`, `pydantic==2.5.0`

## Setup

```bash
# Clone the repo
git clone <repo-url>
cd <repo-name>

# Create and activate a virtual environment
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Pull data/model artifacts tracked by DVC
dvc pull
```

## Running Locally

```bash
uvicorn src.api.app:app --host 0.0.0.0 --port 8000
```

The API will be available at `http://localhost:8000` (docs at `/docs`, courtesy of FastAPI's built-in Swagger UI).

## Running with Docker

```bash
docker build -t stock-ml-pipeline .
docker run -p 8000:8000 stock-ml-pipeline
```

## Experiment Tracking

This project uses MLflow to track training runs. To view the MLflow UI locally:

```bash
mlflow ui
```

`mlruns/`, `mlflow.db`, and `mlartifacts/` are git-ignored — MLflow data isn't committed to source control.

## Data & Model Versioning (DVC)

Data (`/data`, `/src/data`) and trained models (`/src/models`) are excluded from Git and tracked via DVC instead:

```bash
dvc pull    # fetch tracked data/models
dvc repro   # reproduce the pipeline (if a dvc.yaml pipeline is defined)
```

## ⚠️ Deployment (Kubernetes)

Manifests live in `k8/` (deployment, service, and any other resources). Typical apply command:

```bash
kubectl apply -f k8/
```

*(Update with actual manifest filenames and any required secrets/config once `k8/` contents are confirmed.)*

## Next Steps

- Fill in the actual pipeline description, model architecture, and API endpoint spec once `src/` is available.
- Document the DVC pipeline stages (`dvc.yaml`) if one exists.
- List the specific Kubernetes resources in `k8/` and any required environment variables/secrets.
- Add an `.env.example` for required environment variables (the `.gitignore` excludes `.env`).
