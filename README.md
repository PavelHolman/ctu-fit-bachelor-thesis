# Forecasting energy costs in day-ahead and intraday markets

Code, data pipeline and experiments for my bachelor's thesis at the Faculty of Information Technology, Czech
Technical University in Prague (2025, supervised by Ing. Josef Grus). It forecasts electricity prices on the Czech
day-ahead and intraday markets operated by OTE a.s., so that energy-intensive manufacturing can be scheduled into
cheaper hours.

## Results

The best model for each market, evaluated on **unseen data from March 2025** without any retraining:

| Market | Forecast | Best model | MAE | RMSE |
| --- | --- | --- | --- | --- |
| Day-ahead | 7 days ahead, hourly | XGBoost | 24.00 EUR/MWh | 32.96 EUR/MWh |
| Intraday | 1 day ahead, 15-minute resolution | Random Forest | 9.44 EUR/MWh | 14.72 EUR/MWh |

Along the way, machine-learning and deep-learning models clearly beat statistical baselines; among the time-series
models, the transformer-based **Chronos** had the lowest day-ahead error during model selection.

## Approach

- **Data:** OTE market prices combined with weather forecasts from [Open-Meteo](https://open-meteo.com) and power-grid
  indicators (such as generation estimates) from the [ENTSO-E](https://transparency.entsoe.eu) transparency platform,
  merged by an automated cleaning and feature pipeline.
- **Models:** naive and average baselines, statistical models (e.g. ETS), machine-learning and deep-learning models
  through [AutoGluon](https://auto.gluon.ai) (including XGBoost, Random Forest and Chronos), tabular models with direct
  and recursive multi-step strategies, and ensembles.
- **Evaluation:** a strictly chronological split into training, validation, model-selection (February 2025) and test
  (March 2025) sets, with 14 rolling windows for model comparison. Metrics: MAE, RMSE, MAPE, directional accuracy (MDA)
  and peak detection (precision, recall, F1).

## Repository structure

- `data/`: raw, interim and processed datasets.
- `src/`: data fetching (`data/`), feature engineering (`features/`), models (`models/`) and evaluation (`testing/`).
- `notebooks/`: model comparison for each market and forecasting style (time series and tabular), and the final
  results in `notebooks/results/`.
- `utils.py`: loads environment variables and creates the folder structure if needed.
- `requirements.txt`: Python dependencies (pandas, scikit-learn, AutoGluon, PyTorch, entsoe-py, Open-Meteo client).

## Setup

```sh
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
jupyter notebook notebooks/
```

To fetch fresh data, you need an
[ENTSO-E API key](https://uat-transparency.entsoe.eu/content/static_content/Static%20content/web%20api/how_to_get_security_token.html)
in the `ENTSOE_API_KEY` environment variable; without it, the ENTSO-E part of the data will be missing.
