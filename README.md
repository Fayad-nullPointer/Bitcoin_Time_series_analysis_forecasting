# Lab 2: Exponential Smoothing & ETS Models

## Objective
Apply State-Space models to the **Daily Climate Delhi Dataset** to forecast mean temperature using various Exponential Smoothing techniques.

## Dataset
- **Source**: [Daily Climate Time Series Data](https://www.kaggle.com/datasets/sumanthvrao/daily-climate-time-series-data) (Kaggle)
- **Target Variable**: `meantemp` (Daily mean temperature in Delhi)
- **Train**: `DailyDelhiClimateTrain.csv`
- **Test**: `DailyDelhiClimateTest.csv`

## Tasks Completed

### 1. Data Loading & Preprocessing
- Loaded dataset via `kagglehub`
- Converted date column to `DatetimeIndex` with daily frequency (`'D'`)

### 2. Seasonal Decomposition
- Performed both **Additive** and **Multiplicative** decomposition with `period=365`
- **Conclusion**: Multiplicative decomposition chosen — residuals are smaller than in the additive case

### 3. Simple Exponential Smoothing (SES)
- Fitted `SimpleExpSmoothing` with optimized alpha (~0.78)
- SES captures level but lacks trend/seasonality — eliminated from final comparison

### 4. Holt's Linear Trend Model
- Tested additive and multiplicative trend, with and without damping
- **Insight**: Damping has minimal effect on in-sample fit but matters for out-of-sample forecasts (prevents unbounded trend growth)

### 5. ETS State-Space Models (Additive vs. Multiplicative)
- Fitted `ETSModel` with configurations:
  - **Additive**: `ETS(A,A,A)` — error='add', trend='add', seasonal='add'
  - **Multiplicative**: `ETS(M,A,M)` — error='mul', trend='add', seasonal='mul'
- **Ljung-Box Test**:
  - Additive: p < 0.05 → residuals have remaining autocorrelation ❌
  - Multiplicative: p > 0.05 → residuals are white noise ✅
- **Error Metrics**: Multiplicative slightly outperforms additive on MAE/MSE/RMSE

### 6. Holt-Winters (Triple Exponential Smoothing)
- Fitted `ExponentialSmoothing` with `trend='add'`, `seasonal='add'`, `seasonal_periods=365`
- Also tested fully multiplicative variant

### 7. Forecasting & Evaluation
- Generated out-of-sample forecasts on the test set for ETS, Holt-Winters, and SES
- SES produces a flat forecast line → eliminated
- ETS and Holt-Winters both capture seasonal patterns in forecasts

### 8. Final Model Selection
| Model | MAE | MSE | RMSE |
|-------|-----|-----|------|
| ETS (A,A,A) | Computed | Computed | Computed |
| Holt-Winters (Add) | Computed | Computed | Computed |

**Winner: Holt-Winters** — lower error metrics on fitted values

## Key Takeaways
- Multiplicative models better suit this dataset based on Ljung-Box residual diagnostics
- Damped trend is recommended for long-horizon climate forecasts to avoid unrealistic extrapolation
- SES is too simplistic for seasonal data — it only captures level
- Both ETS and Holt-Winters perform well; Holt-Winters edges out on in-sample error

## Libraries Used
- `pandas`, `numpy`, `matplotlib`
- `statsmodels` (seasonal_decompose, SimpleExpSmoothing, ExponentialSmoothing, ETSModel)
- `scikit-learn` (mean_absolute_error, mean_squared_error)
- `kagglehub`

## How to Run
```bash
pip install pandas numpy matplotlib statsmodels scikit-learn kagglehub