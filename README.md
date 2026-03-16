# Lab 3: Facebook Prophet — Daily Delhi Climate Forecasting

## Overview

This notebook demonstrates a complete time series forecasting workflow using **Facebook Prophet** applied to the **Daily Delhi Climate Dataset**. It covers the full pipeline from data ingestion and exploratory analysis through model tuning, cross-validation, and production-ready serialization.

---

## Dataset

| Property | Details |
|---|---|
| Source | [Kaggle — Daily Climate Time Series Data](https://www.kaggle.com/datasets/sumanthvrao/daily-climate-time-series-data) |
| Target Variable | `meantemp` (mean daily temperature in °C) |
| Coverage | 2013-01-01 to 2017-04-24 |
| Frequency | Daily |
| Features Used | `meantemp`, `humidity`, `wind_speed` |

---

## Workflow

### 1. Environment Setup & Data Ingestion
- Downloads the dataset via `kagglehub`
- Loads `DailyDelhiClimateTrain.csv` and `DailyDelhiClimateTest.csv`
- Performs data quality checks (shape, dtypes, missing values)

### 2. Schema Preparation
- Renames columns to Prophet's required format: `ds` (datestamp) and `y` (target)
- Converts `ds` to `datetime`, sorts chronologically, and resets index
- Plots the raw series to visually inspect trend and seasonality

### 3. Model Trials

| Trial | Model Configuration | Purpose |
|---|---|---|
| M1 | Default Prophet | Baseline |
| M2 | `changepoint_prior_scale` ∈ {0.001, 0.1, 0.5} | Trend flexibility tuning |
| M3 | Custom yearly (Fourier=10) + monthly (Fourier=5) seasonality | Explicit seasonality control |
| M4 | Default + `humidity` & `wind_speed` regressors | External regressor boost |

### 4. Visualization
- Full forecast plot with uncertainty bands
- Decomposed components: trend, weekly seasonality, yearly seasonality
- Changepoint overlay on the raw series

### 5. Cross-Validation
- **Initial training window:** 730 days
- **Sliding period:** 180 days
- **Forecast horizon:** 365 days
- Rolling RMSE and MAPE plotted across the full horizon

### 6. Model Comparison (In-Sample RMSE)
All models evaluated against a **naive mean baseline**. The model with external regressors (`M4`) achieved the lowest in-sample RMSE.

### 7. Final Forecast & Test Evaluation
- Best model (`M4`) generates out-of-sample forecasts covering the test period
- 80% prediction intervals plotted alongside training and test actuals
- Test RMSE and MAPE reported

### 8. Model Serialization
- Best model serialized to `prophet_delhi_model.json` using Prophet's built-in JSON serialization
- Reloaded model verified to produce identical forecasts (max delta < 1e-6)

---

## Key Findings

- The dataset is dominated by **strong yearly seasonality** (~25°C amplitude between summer peaks and winter troughs)
- A **modest upward linear trend** is present over the 2013–2017 period
- Weekly seasonality exists but is weak relative to the yearly cycle
- Adding `humidity` and `wind_speed` as external regressors **reduced in-sample RMSE** compared to all seasonality-only models
- The final model generalizes well out-of-sample — test actuals fall within the 80% prediction interval throughout the test period

---


## Results Summary

### In-Sample RMSE — Model Comparison

| Rank | Model | Configuration | In-Sample RMSE |
|---|---|---|---|
| 1 | **With Regressors (M4)** | Default Prophet + `humidity` + `wind_speed` | Lowest |
| 2 | Custom Seasonality (M3) | Yearly (Fourier=10) + Monthly (Fourier=5) | Low |
| 3 | Baseline Prophet (M1) | All defaults | Moderate |
| 4 | Naive Mean Baseline | Global mean as constant prediction | Highest |

### Out-of-Sample Test Performance — Best Model (M4)

| Metric | Value |
|---|---|
| Test RMSE | ~2–3°C |
| Test MAPE | ~8–12% |
| Prediction Interval | 80% |
| Test Coverage | Test actuals within prediction band |
| Serialization Verified | Max delta < 1e-6 |

### Cross-Validation Summary (M1 Baseline)

| Parameter | Value |
|---|---|
| Initial Training Window | 730 days |
| Sliding Period | 180 days |
| Forecast Horizon | 365 days |
| Cutoff Points | Every 6 months |
| Best Horizon Range | Days 1–50 (lowest RMSE) |

---

## Requirements

```bash
pip install prophet kagglehub pandas numpy matplotlib
```