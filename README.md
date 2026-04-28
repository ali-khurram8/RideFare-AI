# RideFare AI — Taxi Trip Cost Predictor

A machine learning web app that predicts ride fares (in PKR) using three trained regression models. Built with Flask and a dark-themed single-page frontend.

## Features

- Predict ride fares based on pickup/dropoff zone, distance, passengers, time of day, traffic, and ride type
- Compare predictions side-by-side across all three models
- Automatic surge pricing indicator for high-traffic conditions
- Live model performance metrics (MAE, RMSE, R²)

## Models

| Model | MAE (PKR) | RMSE (PKR) | R² | Confidence |
|---|---|---|---|---|
| Random Forest | 129 | 161 | 0.998 | 94% |
| Gradient Boosting | 141 | 187 | 0.998 | 92% |
| Linear Regression | 957 | 1418 | 0.874 | 74% |

Random Forest is used as the default model.

## Project Structure

```
Ride-Fare-AI/
├── train_model.py              # Generates dataset and trains all three models
├── app (1).py                  # Flask API server
├── templates/
│   └── uber_trip_cost_predictor.html  # Single-page frontend
├── random_forest_model.pkl     # Trained Random Forest pipeline
├── gradient_boosting_model.pkl # Trained Gradient Boosting pipeline
├── linear_regression_model.pkl # Trained Linear Regression pipeline
├── taxi_fare_model.pkl         # Default model (copy of Random Forest)
├── RideFare_AI_Report.pdf
└── RideFare_AI_Presentation.pptx
```

## Setup

**Requirements:** Python 3.8+

```bash
pip install flask pandas numpy scikit-learn joblib
```

**Train the models** (only needed once, or to retrain):

```bash
python train_model.py
```

This generates the four `.pkl` files. Pre-trained models are already included in the repo.

**Start the server:**

```bash
python "app (1).py"
```

Open `http://localhost:5000` in your browser.

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | Serves the frontend |
| `POST` | `/predict` | Predict fare with a single model |
| `POST` | `/compare` | Predict fare with all three models |
| `POST` | `/predict_best` | Return the lowest fare across all models |
| `GET` | `/models` | List loaded models and their metadata |

### Request body (for POST endpoints)

```json
{
  "pickup": "downtown",
  "dropoff": "airport",
  "distance": 15.5,
  "passengers": 2,
  "timeofday": "morning",
  "traffic": "medium",
  "ridetype": "standard",
  "model": "random_forest"
}
```

**Options:**
- `pickup` / `dropoff`: `downtown`, `airport`, `suburb`, `midtown`, `uptown`
- `timeofday`: `morning`, `midday`, `evening`, `night`, `latenight`
- `traffic`: `low`, `medium`, `high`, `jam`
- `ridetype`: `standard`, `xl`, `premium`, `economy`
- `model` (predict only): `random_forest`, `gradient_boosting`, `linear_regression`

## Training Data

The dataset is synthetically generated (10,000 samples) with fare logic:

- Base fare: PKR 200 + PKR 80/km
- Traffic jam multiplier: 1.8×
- Premium ride multiplier: 2.0×
- Minimum fare: PKR 200
- Gaussian noise added for realism
