# Bitcoin Time Series Analysis & Forecasting

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

## 📌 Overview

This project applies **classical statistical time series techniques** to forecast Bitcoin (BTC/USD) prices using 1-minute interval historical data. The analysis covers the full pipeline from raw data ingestion through preprocessing, stationarity testing, model selection, and evaluation.

---

## 📂 Dataset

- **Source:** [Kaggle - Comprehensive BTC/USD 1M Data](https://www.kaggle.com/datasets/imranbukhari/comprehensive-btcusd-1m-data)
- **File:** `BTCUSD_1m_Combined_Index.csv`
- **Target Feature:** `Close` price only
- **Resampled To:** Daily frequency (`D`) using `.last()` aggregation

---

## 🔬 Methodology

### 1. Data Preprocessing & Resampling
- Parsed datetime index from 1-minute OHLCV data
- Resampled to daily close prices
- Removed NaN values post-resampling

### 2. Time Series Decomposition
- Applied **multiplicative decomposition** (`period=12`)
- Extracted: **Trend**, **Seasonality**, and **Residuals**

### 3. Stationarity Testing
- Performed **Augmented Dickey-Fuller (ADF) Test**
- Raw series → **Non-Stationary** (p-value > 0.05)
- Applied **log transformation + first-order differencing**
- Differenced log series → **Stationary** (p-value < 0.05)

### 4. ACF & PACF Analysis
- Plotted ACF and PACF on the stationary series (30 lags)
- Identified no significant seasonality
- Suggested AR order `p=1–2`, MA order `q=1–2`

### 5. Model Building
| Library | Best Model Found |
|---|---|
| Manual | `ARIMA(2,1,0)` |
| `pmdarima` AutoARIMA | `ARIMA(0,1,1)` with intercept |
| `statsforecast` AutoARIMA | `ARIMA(2,1,2)(1,0,1)[30]` with drift |

### 6. Evaluation Metrics
- **MAE** – Mean Absolute Error
- **RMSE** – Root Mean Squared Error
- **MAPE** – Mean Absolute Percentage Error

---

## 🛠️ Tech Stack

| Category | Libraries |
|---|---|
| Data Manipulation | `pandas`, `numpy` |
| Visualization | `matplotlib` |
| Statistical Modeling | `statsmodels` |
| Auto Model Selection | `pmdarima`, `statsforecast` |
| Deep Learning Check | `torch` (GPU detection) |

---

## 🚀 Getting Started

### Prerequisites
```bash
pip install pandas numpy matplotlib statsmodels pmdarima statsforecast kagglehub torch
```

### Run the Notebook
```bash
jupyter notebook Lab1-Student-Tasks.ipynb
```

---

## 📊 Results

- The raw BTC/USD price series is **non-stationary** due to a strong upward trend
- Log differencing successfully removes the trend, yielding a stationary series
- The **statsforecast AutoARIMA** model identified a seasonal ARIMA structure with a 30-day period
- Visual forecasts and confidence intervals are plotted against actual test data

---

## 📁 Project Structure

```
Time series Analysis/
│
├── Lab1-Student-Tasks.ipynb   # Main analysis notebook
├── README.md                  # Project documentation
└── data/                      # Downloaded via kagglehub (auto-generated)
```

---

## 🤝 Acknowledgements

- Dataset by [imranbukhari](https://www.kaggle.com/imranbukhari) on Kaggle
- Built as part of **ITI 9-Month AI & ML Track** coursework