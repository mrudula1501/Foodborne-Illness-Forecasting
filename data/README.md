# Foodborne Illness Dataset

## Files

### outbreaks.csv
- **Size:** 1.5MB
- **Records:** 100,000+ outbreak records
- **Columns:** Date, Location, Species (pathogen), Illnesses, Deaths, Hospitalizations
- **Time Period:** 2019-2025
- **Source:** CDC NORS (National Outbreak Reporting System)

### monthly_illnesses.csv
- **Size:** 3.3KB
- **Records:** 120+ monthly aggregated records
- **Columns:** Date, Total_Illnesses
- **Time Period:** Monthly aggregation (2015-2024)
- **Source:** Aggregated from CDC FoodNet surveillance data

## Load Data in R
```r
# Read outbreak data
outbreaks <- read.csv("outbreaks.csv")

# Read monthly aggregated data
monthly <- read.csv("monthly_illnesses.csv")

# Check structure
str(outbreaks)
str(monthly)
```

## Load Data in Python
```python
import pandas as pd

# Read outbreak data
outbreaks = pd.read_csv('outbreaks.csv')

# Read monthly aggregated data
monthly = pd.read_csv('monthly_illnesses.csv')

# Check first rows
print(outbreaks.head())
print(monthly.head())
```

## Data Characteristics

- **Strong seasonality:** Summer peaks (June-August)
- **Trend:** Increasing reports due to improved surveillance
- **Outbreak spikes:** Random contamination events
- **Missing values:** Minimal after cleaning
- **Temporal resolution:** Daily records, monthly aggregations

## CDC Data Sources

- **FoodNet:** https://www.cdc.gov/foodnet/
- **NORS:** https://www.cdc.gov/foodsafety/foods-linked-illness/
