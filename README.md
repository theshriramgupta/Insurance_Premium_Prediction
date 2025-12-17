# Insurance Premium Prediction

Lightweight FastAPI backend with a minimal Streamlit frontend to predict insurance premium categories from basic user attributes. Input is validated and derived features (BMI, age group, lifestyle risk, city tier) are computed prior to inference.

## Overview
- FastAPI service exposes health and prediction endpoints.
- Prediction logic and model loading live in models/predict.py.
- Streamlit frontend (frontend/frontend.py) demonstrates the API.

## Tech stack
- Python 3.12
- FastAPI, Uvicorn
- Pydantic v2 (schemas)
- scikit-learn (model inference / pickle)
- Streamlit (UI)
- Docker (optional)

See required packages in `requirements.txt`.

## Features
- Input validation + computed fields (BMI, age group, lifestyle risk, city tier).
- Predict endpoint returning predicted category, confidence and class probabilities.
- Health endpoint exposing model version and load status.
- Simple Streamlit UI to call the API.

## Installation
1. Clone the repository.
2. Create & activate a virtual environment (Windows):
   ```powershell
   python -m venv .venv
   .venv\Scripts\activate
   ```
3. Install dependencies:
   ```powershell
   pip install -r requirements.txt
   ```
4. Ensure a trained model pickle is available at `models/model.pkl` (used by `models/predict.py`).

## Running

- Locally (FastAPI):
  ```powershell
  uvicorn app:app --host 0.0.0.0 --port 8000
  ```

- Streamlit frontend:
  ```powershell
  streamlit run frontend/frontend.py
  ```

- Docker:
  ```powershell
  docker build -t insurance-premium .
  docker run -p 8000:8000 insurance-premium
  ```

## API

- GET /  
  - Human readable status.

- GET /health  
  - Returns: status, model version, model_loaded (bool).

- POST /predict  
  - Request: validated by `schema.user_input.UserInput`.
  - Response model: `schema.prediction_response.PredictionResponse`.
  - NOTE: Current implementation returns the prediction wrapped inside a `response` key:
    ```json
    { "response": { ...prediction payload... } }
    ```
    Consider removing the wrapper to directly return the schema object.

Example request:
```sh
curl -X POST "http://127.0.0.1:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{"age":30,"weight":65.0,"height":1.7,"income_lpa":10,"smoker":false,"city":"Mumbai","occupation":"private_job"}'
```

## Project structure
- app.py — FastAPI application and endpoints
- Dockerfile
- requirements.txt
- frontend/frontend.py — Streamlit UI
- models/predict.py — model loading & inference (expects `models/model.pkl`)
- config/city_tier.py — city tier lists
- schema/user_input.py — input Pydantic model
- schema/prediction_response.py — response schema
- gen_readme_prompt.txt, structure.txt — repo notes

## Future improvements
- Add unit & integration tests (validation, endpoint, inference).
- Align response body with `PredictionResponse` (remove `response` wrapper).
- Add model training script, reproducible model artifact and versioning (MLflow).
- Improve frontend UX and client-side validation.
- Harden Docker build (multi-stage, smaller base) and pin dependency versions.

