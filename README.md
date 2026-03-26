# 📈 Lab 4: Supervised Machine Learning for Time Series Forecasting

## 🎯 Overview
This project explores the paradigm shift from classical statistical time series models (like ARIMA/ETS) to **Supervised Machine Learning** approaches. It demonstrates how to transform raw sequential data into a tabular format, engineer temporal features, and successfully address the severe limitations of tree-based models when forecasting trending data.

**Dataset:** BTC-USD 1-Minute Data (Resampled to Daily Frequency)

---

## 🔑 Key Concepts & Implementations

### 1. Tabular Transformation & Feature Engineering
Machine learning models (like Random Forest and XGBoost) do not inherently understand time. To solve this, the time series data was transformed into a structured feature set:
* **Time Embeddings:** Extracted metadata such as `day_of_week`, `month`, `day_of_year`, and `is_weekend`. 
* **Sinusoidal Encoding:** Applied sine transformations to cyclic time features to help the model learn cyclical seasonality.
* **Autoregressive Features:** Engineered `lag_1`, `lag_3`, and `lag_7` to provide historical context.
* **Rolling Windows:** Added moving averages and rolling standard deviations (`rolling_mean_7`, `rolling_std_7`) to capture local momentum and volatility.

### 2. Chronological Validation
To prevent **target leakage** (training on future data to predict the past), the traditional randomized train-test split was replaced with a strict **80/20 chronological split**.

### 3. Diagnosing "The Extrapolation Problem"
Tree-based algorithms rely on recursive space partitioning, which completely restricts them from predicting values outside the minimum and maximum ranges seen in their training data. Initial models failed to capture breaking bullish/bearish trends, resulting in flattened forecasts.

### 4. Hybrid Modeling (The Solution)
To solve the extrapolation problem, a **Hybrid Detrending Architecture** was constructed:
1. An **ElasticNet** (Linear Model) was trained to capture and project the macro trend into the infinite future.
2. The data was detrended by subtracting the linear predictions from the actual target, leaving only the stationary residuals (fluctuations).
3. A **Random Forest Regressor** was then trained exclusively on those residuals.
4. **Final Forecast:** Linear Trend + Tree Residuals.

### 5. Multi-Step Forecasting (Direct Strategy)
Implemented the **Direct Strategy** to predict `T+3` (3 days into the future). By using `shift(-3)` to align future targets against today's features, a dedicated independent model was mapped to predict ahead without accumulating the compounding snowball errors typical of recursive (step-by-step) forecasting.

---

## 🛠️ Technologies Used
* **Language:** Python
* **Data Processing:** Pandas, NumPy
* **Machine Learning:** Scikit-Learn (RandomForest, ElasticNetCV)
* **Gradient Boosting:** XGBoost
* **Data Visualization:** Matplotlib

---
*Created as part of the ITI 9-Month AI & Machine Learning Program.*
