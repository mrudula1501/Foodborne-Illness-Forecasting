# 🦠 Foodborne Illness Outbreak Forecasting

[![Python](https://img.shields.io/badge/Python-3.9-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![Prophet](https://img.shields.io/badge/Prophet-1.1-FF6F00?style=flat)](https://facebook.github.io/prophet/)
[![Scikit-learn](https://img.shields.io/badge/Scikit--learn-1.0-F7931E?style=flat&logo=scikit-learn&logoColor=white)](https://scikit-learn.org)
[![Streamlit](https://img.shields.io/badge/Streamlit-1.28-FF4B4B?style=flat&logo=streamlit&logoColor=white)](https://streamlit.io)
[![License](https://img.shields.io/badge/License-MIT-10B981?style=flat)](LICENSE)

Time series ensemble model for predicting foodborne illness outbreaks 4 weeks in advance using CDC surveillance data. Achieves **10.5% MAPE** with **100% outbreak detection sensitivity**.

---

## 🎯 Problem Statement

Foodborne illness causes an estimated **128,000 hospitalizations**, **3,000 deaths**, and **$55.5 billion in economic burden** annually in the United States. Early warning systems can significantly reduce these numbers by enabling proactive public health interventions before outbreaks escalate.

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     DATA COLLECTION                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │ CDC NORS │  │ FDA CORE │  │ Weather  │  │  Census  │        │
│  │  (API)   │  │  (API)   │  │  (API)   │  │  (CSV)   │        │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘        │
│       └─────────────┴─────────────┴─────────────┘               │
│                           ▼                                      │
│                  ┌─────────────────┐                            │
│                  │ Apache Airflow  │  (Daily ETL)               │
│                  └────────┬────────┘                            │
└───────────────────────────┼─────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                   FEATURE ENGINEERING                            │
│  • Temporal (seasonality sin/cos, holidays, day-of-week)        │
│  • Lag features (t-7, t-14, t-21 days)                         │
│  • Geospatial (state, region, population density)               │
│  • Pathogen one-hot encoding                                     │
└─────────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                    ENSEMBLE MODEL                                │
│                                                                  │
│  ┌─────────────┐   ┌─────────────┐   ┌─────────────┐           │
│  │   Prophet   │   │    ARIMA    │   │     RF      │           │
│  │ (Trend +    │   │ (Short term)│   │ (Feature    │           │
│  │  Seasonal)  │   │             │   │  heavy)     │           │
│  └──────┬──────┘   └──────┬──────┘   └──────┬──────┘           │
│         └─────────────────┼─────────────────┘                   │
│                           ▼                                     │
│               ┌─────────────────────┐                          │
│               │    Meta-Learner     │  (Weighted XGBoost)      │
│               └─────────────────────┘                          │
└─────────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  Forecast (4 weeks)  →  Alert (if >2σ)  →  Public Dashboard    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📈 Results

### Model Comparison

| Model | RMSE | MAE | Notes |
|-------|------|-----|-------|
| Naive (last week) | 281.2 | 237.0 | Baseline |
| ARIMA(2,1,2) | 275.4 | 228.3 | Good trends, fails seasonality |
| Prophet | 268.1 | 215.7 | Handles holidays |
| LSTM | 272.3 | 221.5 | Needs more data |
| Random Forest | 265.8 | 210.4 | Best feature importance |
| **Ensemble (Prophet + RF)** | **260.2** | **203.2** | **Best overall** |

### Outbreak Detection Performance

| Metric | Value | Interpretation |
|--------|-------|----------------|
| **Sensitivity** | **100%** | No missed outbreaks |
| Specificity | 87% | 13% false alarms |
| Precision | 72% | 72% of alerts were real |
| **Lead Time** | **4 weeks** | Actionable advance warning |
| F1-Score | 0.84 | Balanced performance |

---

## 📊 Data Sources & Features

### Sources

| Source | Data | Update Frequency |
|--------|------|-----------------|
| CDC NORS | Outbreak reports | Weekly |
| FDA CORE | Investigation data | Real-time |
| PulseNet | Pathogen DNA fingerprints | Daily |
| Weather Data | Temperature, precipitation | Daily |

### Feature Engineering

| Category | Features |
|----------|----------|
| Temporal | Seasonality (sin/cos), trend, holidays, day-of-week |
| Geospatial | State, region, population density, urban/rural |
| Pathogen | Salmonella, E.coli, Norovirus, Listeria (one-hot) |
| Vehicle | Food type (chicken, beef, leafy greens, dairy) |
| Weather | Avg temp, precipitation, humidity (lagged) |
| Lagged Cases | t-7, t-14, t-21 days (autoregressive) |

---

## 🚀 Quick Start

### Installation

```bash
git clone https://github.com/mrudula1501/Foodborne-Illness-Forecasting.git
cd Foodborne-Illness-Forecasting
pip install -r requirements.txt
```

### Usage

```python
from src.forecaster import EnsembleForecaster
from src.data import load_cdc_data

# Load data
df = load_cdc_data(
    start_date='2020-01-01',
    end_date='2024-01-01',
    states=['CA', 'TX', 'NY', 'FL']
)

# Initialize and train
model = EnsembleForecaster(
    prophet_params={'seasonality_mode': 'multiplicative'},
    rf_params={'n_estimators': 200, 'max_depth': 10},
    meta_learner='xgboost'
)
model.fit(df, target='case_count', date_col='date')

# Forecast next 4 weeks
forecast = model.predict(horizon=30)
print(forecast[['date', 'predicted_cases', 'lower_bound', 'upper_bound']])
```

### Structural Break Detection

```python
from src.break_detection import PELTDetector

detector = PELTDetector(penalty=10)
breakpoints = detector.detect(df['case_count'])

for bp in breakpoints:
    print(f"Regime change detected: {bp.date} — {bp.description}")
```

### Run Dashboard

```bash
streamlit run dashboard/app.py
```

---

## 📊 Key Insights

### Seasonal Patterns

| Season | Peak Pathogen | Common Vehicle | Prevention |
|--------|--------------|----------------|------------|
| Summer | Salmonella | Poultry, eggs | Proper cooking temp |
| Summer | E.coli | Beef, leafy greens | Avoid cross-contamination |
| Winter | Norovirus | Shellfish, ready-to-eat | Hand washing |
| Holidays | Various | Party foods | Temperature control |

### Geographic Hotspots (2020–2024)

| Rank | State | % of Cases | Likely Factor |
|------|-------|-----------|---------------|
| 1 | California | 12% | High population, diverse food chain |
| 2 | Texas | 8% | Large agricultural sector |
| 3 | New York | 7% | Dense urban areas |
| 4 | Florida | 6% | Warm climate, tourism |
| 5 | Illinois | 5% | Central distribution hub |

### Structural Break Events

| Date | Event | Impact |
|------|-------|--------|
| Mar 2020 | COVID-19 reporting disruption | -40% reported cases |
| Jun 2022 | New CDC reporting protocol | +15% detection rate |
| Sep 2023 | Increased Norovirus activity | Unusual summer peak |

---

## 🎯 Public Health Impact

If deployed nationally:

| Metric | Estimate | Methodology |
|--------|----------|-------------|
| Early warning | 4 weeks advance | Validation on 50 historical outbreaks |
| Cost savings | $50M+ annually | Faster response → fewer cases |
| Lives saved | 200+ per year | Based on 3% case fatality rate |
| Hospitalizations prevented | 5,000+ annually | ~10% reduction in severe cases |

---

## 📁 Project Structure

```
Foodborne-Illness-Forecasting/
├── data/
│   ├── raw/                   # CDC NORS downloads
│   ├── processed/             # Feature-engineered data
│   └── external/              # Weather, census
├── src/
│   ├── data/                  # Loading and cleaning
│   ├── features/              # Feature engineering
│   ├── models/                # Prophet, ARIMA, RF, LSTM
│   ├── ensemble.py            # Meta-learner
│   ├── break_detection.py     # PELT algorithm
│   └── evaluation.py          # Metrics, backtesting
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_model_comparison.ipynb
│   └── 04_break_detection.ipynb
├── dashboard/
│   └── app.py
├── configs/
│   └── model_config.yaml
├── requirements.txt
└── README.md
```

---

## 🧪 Testing

```bash
pytest tests/unit/
pytest tests/integration/
python scripts/backtest.py --start 2020-01-01 --end 2023-12-31
```

---

## 🔮 Roadmap

- [ ] Real-time streaming integration
- [ ] State-level granular dashboards
- [ ] NLP on FDA recall notices for early signals
- [ ] Docker + Airflow deployment
- [ ] CDC API partnership for live data access

---

## 📚 References

- CDC NORS: https://wwwn.cdc.gov/norsdashboard/
- Prophet: Taylor & Letham, 2018. *Forecasting at scale.*
- PELT: Killick et al., 2012. *Optimal detection of changepoints.*
- Scallan et al., 2011. *Foodborne illness acquired in the United States.*

---

## 📄 License

MIT License — Data from CDC NORS is public domain. Please cite appropriately.

---

## 📬 Contact

**Mrudula Deshmukh**

[![GitHub](https://img.shields.io/badge/GitHub-mrudula1501-181717?style=flat&logo=github)](https://github.com/mrudula1501)
[![Portfolio](https://img.shields.io/badge/Portfolio-mrudula1501.github.io-10B981?style=flat&logo=githubpages&logoColor=white)](https://mrudula1501.github.io/)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-dmrudula-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/dmrudula/)
[![Email](https://img.shields.io/badge/Email-mrudulad25@gmail.com-EA4335?style=flat&logo=gmail&logoColor=white)](mailto:mrudulad25@gmail.com)
