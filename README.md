
# ⚡ Electricity Demand Forecasting  
### A Comparative Study of Time Series, Machine Learning, and Deep Learning Models

This project focuses on forecasting electricity demand for the All-India power grid using two datasets of different temporal resolutions:
- 📅 Daily Demand Data (Long-term forecasting)
- ⏱️ 15-Minute Interval Data (Short-term forecasting)

We implement and compare a wide range of models — from simple baselines to advanced machine learning and deep learning approaches — to understand what works best for real-world time series forecasting.

---

## 📊 Datasets

The data was collected via web scraping from:
https://grid-india.in/en/

### 1. Daily Dataset
- Duration: May 2023 – March 2026  
- Target: max_demand_met_mw  
- Use case: Medium to long-term forecasting  

### 2. 15-Minute Dataset
- Duration: April 2025 – March 2026  
- Target: demand_met_mw  
- Use case: Short-term / real-time forecasting  

---

## ⚙️ Project Workflow

The project follows a structured time series pipeline:

1. Data Collection & Preprocessing  
2. Exploratory Data Analysis (EDA)  
3. Feature Engineering  
4. Model Development  
5. Validation & Model Selection  
6. Final Testing on Unseen Data  

---

## 🧠 Models Implemented

### 🔹 Baseline Models
- Mean Model  
- Naive Model  
- Seasonal Naive  
- Drift Model  
- Rolling Mean  

### 🔹 Autoregressive Models
- Linear AR (lag-based regression)

### 🔹 Classical Time Series Models
- ARIMA  
- SARIMA  
- SARIMAX  

### 🔹 Machine Learning Models
- Random Forest  
- XGBoost  

### 🔹 Deep Learning Models
- DLinear  
- NLinear  

---

## 📈 Feature Engineering

Key features used:
- Lag features (lag_1, lag_7, lag_30, lag_96)
- Rolling statistics (mean, std)
- Cyclical encodings (sin/cos for time)
- Calendar features (day, month, weekend)
- Festival indicators (is_festival, pre/post)

---

## 📏 Evaluation Metrics

All models are evaluated using:
- MAE (Mean Absolute Error)
- RMSE (Root Mean Squared Error)
- MAPE (Mean Absolute Percentage Error)

A strict chronological train → validation → test split is used to prevent data leakage.

---

## 🏆 Final Results

### 📅 Daily Dataset (Test Set)

| Model          | MAE  | RMSE | MAPE |
|---------------|------|------|------|
| Random Forest | 4151 | 5377 | 1.88% |
| XGBoost       | 4386 | 5646 | 1.97% |

---

### ⏱️ 15-Min Dataset (Test Set)

| Model          | MAE  | RMSE | MAPE |
|---------------|------|------|------|
| Random Forest | 1159 | 1526 | 0.58% |
| XGBoost       | 1121 | 1463 | 0.56% |

---


## 🔍 Key Insights

- Machine Learning models outperform all other approaches  
- Feature engineering plays a critical role in performance  
- Naive models are strong baselines and hard to beat  
- Classical models (ARIMA/SARIMA) struggle with complex patterns  
- Deep learning models underperform due to limited data and tuning  
- High-frequency data (15-min) is easier to predict (MAPE < 1%)  


---

## 🚀 How to Run

1. Clone the repository:

git clone https://github.com/shriyanssahoo/Electricity-Demand-Forecasting.git cd Electricity-Demand-Forecasting

2. Install dependencies:

pip install -r requirements.txt

3. Run notebooks in order:

03_eda → 04_modelling

---

## 📌 Future Work

- Incorporate weather data (temperature, humidity)
- Hyperparameter tuning (Bayesian optimization)
- Advanced models (LSTM, TFT, PatchTST)
- Hybrid models (SARIMA + ML)
- Real-time deployment pipeline

---

## 📄 License

This project is for academic purposes.