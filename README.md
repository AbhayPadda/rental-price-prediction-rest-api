# Rental Price Prediction REST API

A full-stack machine learning application for predicting Airbnb rental prices from listing features. The project includes:

- A Flask REST API backend that serves price predictions
- A Streamlit frontend for interactive single-property and batch prediction
- Docker support for both services
- A trained regression model packaged with the backend

## Overview

This project estimates the expected nightly rental price for a property based on attributes such as room type, number of guests accommodated, bathrooms, cancellation policy, cleaning fee, instant bookability, review rating, bedrooms, and beds.

The backend uses a trained machine learning model and exposes endpoints for:

- Single-property prediction
- Batch CSV prediction

The frontend provides a simple UI so users can enter input manually or upload a CSV file for bulk predictions.

## Architecture

```text
+-------------------+        HTTP        +---------------------+
| Streamlit Frontend| -----------------> | Flask API Backend   |
| frontend/app.py   |                    | backend/app.py      |
+-------------------+                    +----------+----------+
                                                     |
                                                     v
                                            Trained model
                                       rental_price_prediction_model_v1_0.joblib
```

## Tech Stack

### Backend
- Python 3.9
- Flask
- Gunicorn
- Pandas
- NumPy
- scikit-learn
- XGBoost
- Joblib

### Frontend
- Python 3.9
- Streamlit
- Pandas
- Requests

## Repository Structure

```text
rental-price-prediction-rest-api/
├── README.md
├── backend/
│   ├── app.py
│   ├── Dockerfile
│   ├── requirements.txt
│   └── rental_price_prediction_model_v1_0.joblib
├── frontend/
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
└── .gitignore
```

## Model

The backend loads a serialized trained regression model from:

```text
backend/rental_price_prediction_model_v1_0.joblib
```

The model predicts the log-price of a rental listing and the backend converts it back into a dollar value using `np.exp(...)` before returning the result.

## API Endpoints

### GET /

Returns a welcome message.

Example:

```bash
curl http://localhost:7860/
```

Response:

```text
Welcome to the Airbnb Rental Price Prediction API!
```

### POST /v1/rental

Predicts the rental price for a single listing.

Request body example:

```json
{
  "room_type": "Entire home/apt",
  "accommodates": 2,
  "bathrooms": 2,
  "cancellation_policy": "strict",
  "cleaning_fee": "True",
  "instant_bookable": "t",
  "review_scores_rating": 90,
  "bedrooms": 1,
  "beds": 1
}
```

Response example:

```json
{
  "Predicted Price (in dollars)": 182.35
}
```

### POST /v1/rentalbatch

Accepts a CSV file with listing rows and returns a dictionary of predicted prices keyed by listing `id`.

Example CSV columns:

```csv
id,room_type,accommodates,bathrooms,cancellation_policy,cleaning_fee,instant_bookable,review_scores_rating,bedrooms,beds
1,Entire home/apt,2,2,strict,True,t,90,1,1
2,Private room,1,1,flexible,False,f,85,0,1
```

Example response:

```json
{
  "1": 182.35,
  "2": 89.12
}
```

## Running Locally

### 1. Backend

```bash
cd backend
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python app.py
```

The API will run at:

```text
http://localhost:5000
```

### 2. Frontend

```bash
cd frontend
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
streamlit run app.py --server.port 8501
```

Open:

```text
http://localhost:8501
```

Note: the frontend is configured to call the backend at `http://backend:7860` for Docker-based networking. If running locally outside Docker, update the `BACKEND_URL` in `frontend/app.py` to use the local backend address (for example, `http://127.0.0.1:5000`).

## Running with Docker

### Backend container

```bash
docker build -t rental-backend ./backend
docker run -p 7860:7860 rental-backend
```

### Frontend container

```bash
docker build -t rental-frontend ./frontend
docker run -p 8501:8501 rental-frontend
```

## Features

- Single listing price estimation
- Bulk CSV prediction
- Dockerized deployment
- Simple browser-based interface
- Real-time prediction API

## License

This project does not currently specify a license file. If you plan to distribute or reuse it publicly, consider adding an appropriate open-source license such as MIT or Apache 2.0.

## Contributing

Contributions are welcome. If you want to improve the model, UI, API, or documentation, open a pull request with a clear explanation of the change.

## Contact

Maintained by: Abhay Padda

## Notes

This repository is a lightweight demonstration of a machine learning-powered rental price prediction workflow. It is designed as a simple end-to-end example of serving a trained model behind an API and exposing it with a user-friendly interface.
