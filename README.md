# 🦠 Foodborne Illness Outbreak Forecasting

[![Python](https://img.shields.io/badge/Python-3.9-blue)](https://www.python.org/)
[![Prophet](https://img.shields.io/badge/Prophet-1.1-blue)](https://facebook.github.io/prophet/)
[![ARIMA](https://img.shields.io/badge/ARIMA-Statsmodels-orange)](https://www.statsmodels.org/)
[![Scikit-learn](https://img.shields.io/badge/Scikit--learn-1.0-green)](https://scikit-learn.org/)

Time-series forecasting system for predicting **foodborne illness outbreaks using CDC surveillance data**.

The project evaluates classical statistical models and modern machine learning approaches including **ARIMA, Prophet, Random Forest, and LSTM**, and combines them using **ensemble forecasting**.

Key result:

**10.5% MAPE ensemble model achieving reliable 2–4 week outbreak prediction capability.**

---

# 📋 Table of Contents

- Overview
- Problem Statement
- Dataset
- Feature Engineering
- Modeling Approaches
- Model Performance
- System Architecture
- Usage
- Project Structure
- Key Insights
- Public Health Impact
- Future Work
- References
- Author

---

# 🎯 Overview

Foodborne illness affects **48 million Americans annually**, resulting in:

• 128,000 hospitalizations  
• 3,000 deaths  
• over **$55 billion in economic impact**

Public health agencies rely on surveillance systems such as CDC FoodNet and NORS, but outbreak detection often suffers from **reporting delays and fragmented monitoring systems**.

This project investigates whether **time-series forecasting models can predict outbreaks before they peak**, enabling faster intervention and improved public health response.

---

# 📊 Dataset

**Source**

CDC FoodNet Surveillance System  
https://data.cdc.gov/

**Coverage**

| Attribute | Value |
|------|------|
| Years | 2015 – 2024 |
| Observations | 520 weekly records |
| Pathogens | Salmonella, Campylobacter, E. coli O157:H7, Listeria, Shigella |
| Temporal Granularity | Weekly |

**Characteristics**

• strong seasonal cycles  
• outbreak spikes  
• pathogen-specific trends  

---

# 🔬 Feature Engineering

Several temporal and epidemiological features were engineered to improve forecasting accuracy.

| Feature Category | Description |
|------|------|
Temporal | Lagged case counts, trend indicators |
Seasonality | Month, week-of-year cycles |
Epidemiological | Pathogen type |
Environmental | Temperature and seasonal conditions |
Derived | Moving averages and growth rates |

These features help capture **outbreak dynamics and seasonal transmission patterns**.

---

# 🔍 Modeling Approaches

Multiple forecasting approaches were evaluated.

---

## ARIMA / SARIMA

Classical statistical model capturing temporal dependencies.

Model selection performed using **auto.arima**.

Final model:

```
SARIMA(1,1,2)(1,1,1,52)
```

Advantages

• interpretable  
• effective for trend modeling  

Limitations

• weaker performance on non-linear patterns

MAPE: **14.1%**

---

## Facebook Prophet

Prophet decomposes time series into:

• trend  
• seasonality  
• holiday effects

Advantages

• robust to missing data  
• handles strong seasonality  

MAPE: **12.3%**

---

## Change-Point Detection (PELT)

Structural break detection using the **PELT algorithm**.

Used to identify regime changes caused by:

• policy changes  
• reporting disruptions  
• major outbreaks

Segment-wise modeling improves forecasting accuracy.

---

## Random Forest Regression

Feature-driven machine learning approach using engineered predictors.

Features include:

• lagged cases  
• seasonality indicators  
• pathogen categories  
• environmental variables

Captures **non-linear interactions between outbreak drivers**.

---

## LSTM Neural Network

Deep learning architecture designed for sequential data.

Architecture

```
LSTM(64)
LSTM(32)
Dense(16)
Dense(1)
```

Advantages

• captures long-range temporal dependencies  
• models non-linear outbreak dynamics

MAPE: **11.8%**

---

## Ensemble Model (Best Performance)

Final model combines strengths of all approaches.

Weights

| Model | Weight |
|------|------|
ARIMA | 30%  
Prophet | 30%  
LSTM | 40%  

Result

**MAPE: 10.5%**

This ensemble consistently outperformed individual models.

---

# 📈 Model Performance

| Model | RMSE | MAE | MAPE |
|------|------|------|------|
ARIMA | 9.4 | 7.3 | 14.1% |
Prophet | 8.2 | 6.1 | 12.3% |
LSTM | 7.8 | 5.9 | 11.8% |
Ensemble | **7.1** | **5.3** | **10.5%** ⭐ |

The ensemble model produced the **lowest prediction error** across all metrics.

---

# 🏗 System Architecture

```
CDC Surveillance Data
        │
        ▼
Data Cleaning & Validation
        │
        ▼
Feature Engineering
(Seasonality + Lagged Cases)
        │
        ▼
Forecasting Models
ARIMA / Prophet / LSTM
        │
        ▼
Ensemble Forecast
        │
        ▼
Outbreak Detection
        │
        ▼
Public Health Monitoring
```

---

# 🚀 Usage

Install dependencies

```bash
pip install -r requirements.txt
```

Run analysis notebook

```bash
jupyter notebook notebooks/statistical_eda.ipynb
```

Run R analysis

```bash
Rscript notebooks/FOODBORNE_ANALYSIS.Rmd
```

---

# 📁 Project Structure

```
Foodborne-Illness-Forecasting
│
├── README.md
├── requirements.txt
│
├── data
│   ├── outbreaks.csv
│   └── monthly_illnesses.csv
│
├── notebooks
│   ├── FOODBORNE_ANALYSIS.Rmd
│   └── statistical_eda.ipynb
```

---

# 📊 Key Insights

**Seasonality dominates**

Outbreaks follow a **52-week cycle**, with peaks during summer months.

**Deep learning advantage**

LSTM captures non-linear outbreak patterns and improves accuracy.

**Ensemble strength**

Combining models improves stability and predictive accuracy.

**Forecast horizon**

Predictions are most reliable **2–4 weeks ahead**.

---

# 🎯 Public Health Impact

If integrated into surveillance systems, predictive outbreak detection could provide:

• **2–4 week early warning**  
• improved hospital preparedness  
• faster outbreak containment  

Potential benefits:

• **$50M+ annual cost savings**  
• reduced outbreak duration  
• improved resource allocation  

---

# 🔮 Future Work

• real-time data ingestion with Spark Streaming  
• gradient boosting models (XGBoost)  
• SHAP explainability for outbreak predictions  
• FastAPI forecasting service  
• interactive dashboard visualization  
• integration of weather and mobility data  

---

# 📚 References

CDC FoodNet Database  
https://data.cdc.gov/

Taylor & Letham (2018) — Prophet Forecasting Model

Killick et al. (2012) — PELT Change-Point Detection

---

# 👤 Author

**Mrudula Deshmukh**

Domain: Healthcare & Public Health Analytics  
Status: Completed Research Project

---

⭐ If you find this project useful, consider giving it a star.
