# Electricity Demand Forecasting — All-India Grid

### A Comparative Study with Regime Detection and Uncertainty Quantification

> \*\*DS4SG 2026 Submission\*\* · IIIT Dharwad · Time Series Forecasting and Applications  
> Supervised by Dr. Nataraj K

\---

## Overview

This project presents a comprehensive study of electricity demand forecasting for the **All-India national grid**, using data collected from [Grid India](https://grid-india.in) at two temporal resolutions — daily and 15-minute intervals.

Beyond standard point forecasting, the project introduces:

* **Automatic demand regime detection** — four distinct demand regimes identified from data alone
* **Regime-aware forecasting pipeline** — from hard routing to soft probabilistic ensembles
* **Calibrated prediction intervals** — regime-specific 80% PI with operational reserve recommendations

The work is submitted to the [**DS4SG 2026: Data Science for Social Good**](https://dsaa2026.dsaa.co/calls/special-sessions/) special session at IEEE DSAA 2026.

\---

## Key Results

|Model|MAE (MW)|RMSE (MW)|MAPE (%)|Prediction Interval|
|-|-|-|-|-|
|Global Random Forest (NB04)|4,151.56|5,377.17|**1.88**|❌|
|Global XGBoost (NB04)|4,386.03|5,646.74|1.97|❌|
|Regime-Aware Hard Routing (NB06)|5,427.41|7,056.12|2.38|❌|
|Soft Ensemble + Correction (NB07)|5,444.19|7,066.09|2.39|❌|
|**Final Model (NB09)**|**5,119.91**|**6,481.59**|**2.28**|✅ Calibrated|

**Key uncertainty finding:** Elevated Demand days require a **3.4× wider reserve buffer** than Low Demand days — information invisible to a single global model.

\---

## Repository Structure

```
Electricity-Demand-Forecasting/
│
├── data/
│   ├── raw/                        # Raw scraped XLS files from grid-india.in
│   └── processed/                  # Cleaned CSVs and model artefacts
│       ├── daily\_all\_india\_2023\_26.csv
│       ├── timeseries\_15min\_2025\_26.csv
│       ├── daily\_with\_regime.csv
│       ├── predictions\_nb06.csv
│       ├── predictions\_nb07.csv
│       ├── predictions\_nb08.csv
│       ├── predictions\_nb09.csv
│       └── \*.pkl                   # Fitted model artefacts
│
├── notebooks/
│   ├── 01\_data\_loading.ipynb       # Web scraping from grid-india.in
│   ├── 02\_data\_parsing.ipynb       # Preprocessing and feature engineering
│   ├── 03\_eda.ipynb                # Exploratory data analysis
│   ├── 04\_modelling.ipynb          # 14-model benchmark study
│   ├── 05\_regime\_detection.ipynb   # Automatic demand regime clustering
│   ├── 06\_regime\_aware\_forecasting.ipynb  # Hard-routing specialist models
│   ├── 07\_residual\_correction.ipynb       # Soft ensemble + residual correction
│   ├── 08\_uncertainty\_quantification.ipynb # Preliminary UQ exploration
│   └── 09\_final\_model.ipynb        # Final enriched model with calibrated PI
│
├── figures/                        # All generated plots (paper-ready)
│
├── paper/                          # LaTeX source for DS4SG 2026 submission
│
├── requirements.txt
└── README.md
```

\---

## Notebooks Guide

### `01\_data\_loading.ipynb` — Data Collection

Programmatic web scraping from [grid-india.in](https://grid-india.in) using `requests` and `BeautifulSoup`. Collects daily peak demand and 15-minute interval demand data for the All-India grid.

### `02\_data\_parsing.ipynb` — Preprocessing

Parses raw XLS files into clean CSVs. Adds calendar features, festival annotations (28 major Indian holidays), and lag/rolling features. Saves processed data to `data/processed/`.

### `03\_eda.ipynb` — Exploratory Data Analysis

Comprehensive analysis of both datasets including:

* Trend and seasonality decomposition
* Weekly and intraday demand patterns
* Festival vs non-festival demand comparison
* Bimodal seasonal patterns (summer peaks, winter heating)

### `04\_modelling.ipynb` — 14-Model Benchmark

Systematic comparison across five paradigms:

|Category|Models|
|-|-|
|Baselines|Mean, Naive, Seasonal Naive, Drift, SES, Holt, Holt-Winters|
|Linear AR|Autoregressive linear model|
|Classical TS|ARIMA, SARIMA, SARIMAX|
|Deep Learning|DLinear, NLinear|
|Machine Learning|Random Forest (**1.88% MAPE**), XGBoost|

Best result: **Random Forest — MAPE 1.88% (daily), 0.58% (15-min)**

### `05\_regime\_detection.ipynb` — Demand Regime Detection ⭐

**Novel contribution.** Automatically detects four distinct demand regimes from the data using KMeans clustering on rolling statistics and cyclical calendar features — with no manual season labelling.

|Regime|Typical Period|Mean Demand|
|-|-|-|
|High Demand|Jan–Feb, May|\~224,000 MW|
|Elevated Demand|Mar–Jun|\~222,000 MW|
|Moderate Demand|Jul–Sep|\~210,000 MW|
|Low Demand|Oct–Nov|\~204,000 MW|

Zero-leakage guarantee: scaler and KMeans fitted on training data only.

### `06\_regime\_aware\_forecasting.ipynb` — Hard Routing

Trains one specialist XGBoost per regime. Finds that hard routing **hurts** overall performance (MAPE 2.38% vs 2.28% global) because regime misclassification errors outweigh specialisation gains. This is a **genuine finding** — regime boundaries in Indian grid data are soft, not hard.

Key result: Hard routing improves High Demand (+0.32%) and Moderate Demand (+0.16%) but hurts Elevated Demand (−0.49%).

### `07\_residual\_correction.ipynb` — Soft Ensemble

Replaces hard routing with **probabilistic soft routing** — blends all specialists weighted by classifier confidence. Adds a residual correction layer. Partially recovers performance but does not beat the global baseline — the soft blending introduces its own bias from conflicting specialists.

### `08\_uncertainty\_quantification.ipynb` — Preliminary UQ

Explores quantile XGBoost with validation-based calibration. Moderate Demand regime achieves well-calibrated coverage (91.7%). Other regimes show poor calibration due to insufficient validation samples. Results motivate the improved approach in NB09.

### `09\_final\_model.ipynb` — Final Model ⭐

**The proposed model.** Combines:

* **Enriched feature set** (29 features: lags, rolling stats, trend features, cyclical encodings, festival flags)
* **Regime probability features** — classifier probabilities as soft gating signals
* **Three quantile outputs** — Q10, Q50, Q90 in a single pass
* **Per-regime calibration** — binary search on validation set to achieve \~80% coverage

Results:

* Point forecast MAPE: **2.28%**
* Elevated Demand PICP: **80.4%** ✅
* Reserve buffer ratio: **3.4× (Elevated vs Low Demand)**
* 3 of 4 regimes calibrated to \~80% coverage

\---

## Dataset

Both datasets were collected from the **Grid Controller of India's public platform** ([grid-india.in](https://grid-india.in)).

|Dataset|Period|Resolution|Rows|Target Variable|
|-|-|-|-|-|
|Daily|Apr 2023 – Mar 2026|1 day|1,086|`max\_demand\_met\_mw`|
|15-Minute|Apr 2025 – Mar 2026|15 min|33,307|`demand\_met\_mw`|

Festival annotations cover 28 major Indian national and regional holidays with pre/post flags.

\---

## Setup and Installation

### Prerequisites

* Python 3.10+
* Jupyter Notebook or JupyterLab

### Install dependencies

```bash
git clone https://github.com/YOUR\_USERNAME/Electricity-Demand-Forecasting.git
cd Electricity-Demand-Forecasting
pip install -r requirements.txt
```

### Run notebooks in order

```bash
jupyter notebook notebooks/01\_data\_loading.ipynb
```

> \*\*Note:\*\* Notebooks 01 and 02 scrape and process raw data. If you want to skip data collection, the processed CSVs are already available in `data/processed/`.

\---

## Requirements

```
pandas>=2.0.0
numpy>=1.24.0
matplotlib>=3.7.0
seaborn>=0.12.0
scikit-learn>=1.3.0
xgboost>=2.0.0
statsmodels>=0.14.0
requests>=2.31.0
beautifulsoup4>=4.12.0
openpyxl>=3.1.0
jupyter>=1.0.0
```

\---

## Methodology Summary

### No-Leakage Guarantee

All notebooks enforce strict chronological train/val/test splits (70/15/15). Rolling features use `.shift(1)` before `.rolling()` to prevent same-day demand leakage. All scalers, clustering models, and classifiers are fitted exclusively on training data. Val and test sets receive predictions only — never influence fitting.

### Evaluation Metrics

* **MAE** — Mean Absolute Error (MW)
* **RMSE** — Root Mean Squared Error (MW)
* **MAPE** — Mean Absolute Percentage Error (%)
* **PICP** — Prediction Interval Coverage Probability
* **PINAW** — Prediction Interval Normalized Average Width
* **CWC** — Coverage Width Criterion

### Social Good Connection

Accurate demand forecasting directly supports:

* **Grid reliability** — reducing blackouts that disproportionately harm low-income households
* **Cost efficiency** — regime-specific reserve margins prevent over-buffering on stable days
* **Renewable integration** — uncertainty-aware forecasts help manage supply-demand imbalances as India expands renewable capacity toward 2030 targets

\---

## Figures

All paper-ready figures are saved in `figures/`. Key figures:

|File|Description|
|-|-|
|`05\_regime\_demand\_series.png`|Demand series coloured by detected regime|
|`05\_regime\_heatmap.png`|Monthly regime heatmap (2023–2026)|
|`05\_regime\_boxplot.png`|Demand distribution per regime|
|`06\_feature\_importance.png`|Feature importance: global vs specialist models|
|`06\_per\_regime\_mape.png`|MAPE comparison per regime|
|`09\_final\_forecast.png`|Final model forecast with 80% PI band|
|`09\_regime\_intervals.png`|Regime-specific PI width and coverage|
|`09\_feature\_importance.png`|Final model top-20 feature importances|

\---

## Paper

This work is submitted to **DS4SG 2026: Data Science for Social Good**, a special session at [IEEE DSAA 2026](https://dsaa2026.dsaa.co), October 6–9, 2026.

Submission deadline: **May 30, 2026**  
Venue: IEEE DSAA 2026 Conference Proceedings (IEEE Xplore)

LaTeX source available in `paper/`.

\---

## Authors

|Name|Roll Number|Institution|
|-|-|-|
|Shriyans S Sahoo|24BCS142|IIIT Dharwad|
|S Divya|24BCS123|IIIT Dharwad|
||||

\---

## Acknowledgments

Data sourced from the **Grid Controller of India** ([grid-india.in](https://grid-india.in)).  
Supervised by **Dr. Nataraj K**, IIIT Dharwad.

\---

## License

This project is released for academic and research purposes.  
Dataset is sourced from a public government platform and is freely available.

\---

*Last updated: May 2026*

