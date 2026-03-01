# Smart Electricity Demand Forecasting

A data science project that forecasts hourly electricity demand by combining electricity consumption records with weather data. The workflow covers end-to-end steps: data ingestion, cleaning, exploratory analysis, feature engineering, and regression modelling.

---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Getting Started](#getting-started)
- [Methodology](#methodology)
- [Results](#results)
- [Usage](#usage)

---

## Overview

Electricity demand is heavily influenced by weather conditions and temporal patterns (time of day, day of week, season). This project builds a supervised regression model to predict standardized hourly electricity demand using temperature and calendar-based features.

---

## Dataset

| Source | Format | Description |
|--------|--------|-------------|
| Electricity demand | JSON (one file per day) | Hourly sub-regional demand readings (MWh), covering 2022–2024 |
| Weather data | CSV (one file per period) | Hourly 2-metre air temperature (°C) for the same region |

Both datasets are merged on a common `datetime` key to produce a single analysis-ready DataFrame (`final_cleaned_merged_data.csv`).

---

## Project Structure

```
.
├── Data/
│   └── raw/
│       ├── electricity_raw_data/   # Hourly electricity JSON files
│       └── weather_raw_data/       # Hourly weather CSV files
├── final_cleaned_merged_data.csv   # Pre-processed merged dataset
├── 22I2647_BSE8B_AssNo.2.ipynb     # Main analysis notebook
└── README.md
```

---

## Requirements

- Python 3.8+
- numpy
- pandas
- matplotlib
- seaborn
- scikit-learn
- statsmodels

Install all dependencies with:

```bash
pip install numpy pandas matplotlib seaborn scikit-learn statsmodels
```

---

## Getting Started

1. Clone the repository:

   ```bash
   git clone https://github.com/Waleed-Ahmad20/Smart-Electricity-Demand-Forecasting.git
   cd Smart-Electricity-Demand-Forecasting
   ```

2. Install dependencies (see [Requirements](#requirements)).

3. Update the `BASE_PATH` variable inside the notebook to point to your local `Data/raw` directory.

4. Open and run the notebook:

   ```bash
   jupyter notebook "22I2647_BSE8B_AssNo.2.ipynb"
   ```

---

## Methodology

### 1. Data Loading & Integration
- Electricity JSON files are scanned with `glob`, loaded record-by-record, and concatenated into a single DataFrame.
- Weather CSV files are similarly loaded and concatenated.
- Both DataFrames are merged on the `datetime` column (inner join).

### 2. Data Cleaning
- **Duplicate removal** – applied independently to electricity and weather records.
- **Missing values** – rows with any `NaN` are dropped (low overall missingness rate).
- **Outlier removal** – electricity demand outliers detected via the IQR method; temperature outliers removed using a Z-score threshold of ±3.

### 3. Feature Engineering
| Feature | Description |
|---------|-------------|
| `hour` | Hour of the day (0–23) |
| `day` | Day of the month |
| `month` | Month of the year |
| `year` | Calendar year |
| `day_of_week` | Day index (0 = Monday) |
| `is_weekend` | Binary flag (1 = Saturday/Sunday) |

Both `electricity_demand_mwh` and `temperature_2m_c` are standardized using `StandardScaler`.

### 4. Exploratory Data Analysis
- Descriptive statistics including skewness and kurtosis.
- Time series plot of standardised demand.
- Distribution histograms and box plots.
- Pearson correlation heatmap with multicollinearity check (threshold > 0.8).
- Additive seasonal decomposition (weekly period = 7 days).
- Augmented Dickey-Fuller (ADF) stationarity test.

### 5. Regression Modelling
- **Algorithm:** Linear Regression (`sklearn.linear_model.LinearRegression`)
- **Predictors:** `temperature_2m_c`, `hour`, `day_of_week`, `is_weekend`
- **Target:** standardized `electricity_demand_mwh`
- **Split:** 80 % training / 20 % test (chronological, no shuffle)
- **Evaluation metrics:** MSE, RMSE, R²
- Actual vs. predicted plot and residual analysis are produced in the notebook.

---

## Results

All quantitative results (R², RMSE, ADF statistic, etc.) are printed inline when the notebook is executed. Key diagnostic visualisations generated:

- Electricity demand time series
- Demand distribution (histogram + box plot)
- Pearson correlation heatmap
- Seasonal decomposition (trend, seasonal, residual)
- Actual vs. predicted demand scatter/line plot
- Residual plot

---

## Usage

To run the full pipeline on the pre-processed CSV instead of the raw files, load `final_cleaned_merged_data.csv` directly and skip the data-loading section of the notebook:

```python
import pandas as pd

df = pd.read_csv("final_cleaned_merged_data.csv", parse_dates=["datetime"])
```

Then proceed from the **Regression Modelling** section onward.
