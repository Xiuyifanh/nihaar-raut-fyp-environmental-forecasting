# 🛰️ Comparative Forecasting of Satellite-Derived Environmental Time Series

> **A benchmark study across statistical, machine learning, and foundation model paradigms**
> BSc Computer Science Final Year Project · University of Greenwich · April 2026

```
╔══════════════════════════════════════════════════════════════════════╗
║  NDVI Amazon · NDVI Sahel · SST North Atlantic · SST Indian Ocean   ║
║  ARIMA → SARIMAX → Prophet → XGBoost → N-BEATS → TFT → Chronos     ║
║  300 months · 7 models · 4 series · 3 research questions            ║
╚══════════════════════════════════════════════════════════════════════╝
```

![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=flat-square&logo=python&logoColor=white)
![GEE](https://img.shields.io/badge/Google_Earth_Engine-4285F4?style=flat-square&logo=google&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-22c55e?style=flat-square)
![University](https://img.shields.io/badge/University_of_Greenwich-BSc_CS-8B0000?style=flat-square)
![Status](https://img.shields.io/badge/Status-Submitted_April_2026-f59e0b?style=flat-square)

---

## 🌍 What is this?

> Somewhere between **50 years of ARIMA** and **Amazon's Chronos foundation model**, there's a data regime boundary. This project finds it — empirically, on real satellite data, across four ecologically distinct regions of Earth.

This repository contains the complete implementation of a **systematic comparative forecasting benchmark** applied to four satellite-derived environmental time series extracted via **Google Earth Engine**:

| Series | Indicator | Region | Source |
|--------|-----------|--------|--------|
| 🌿 NDVI Amazon | Vegetation health | Amazon Basin, South America | MODIS MOD13A3 |
| 🌾 NDVI Sahel | Vegetation health | Sahel Belt, West Africa | MODIS MOD13A3 |
| 🌊 SST North Atlantic | Sea surface temp | North Atlantic Ocean | NOAA ERSSTv5 |
| 🌡️ SST Indian Ocean | Sea surface temp | Tropical Indian Ocean | NOAA ERSSTv5 |

All series span **January 2000 – December 2024** (300 monthly observations), split **80/20 chronologically** — 252 training, 48 test. No data leakage. No cross-validation tricks. Honest evaluation.

---

## 🏆 Key Results at a Glance

```
┌─────────────────────┬──────────────┬──────────┬──────────────────────────┐
│ Series              │ Best Model   │ Best MAE │ Runner-up                │
├─────────────────────┼──────────────┼──────────┼──────────────────────────┤
│ NDVI Amazon         │ SARIMAX V2   │  0.0085  │ TFT V1 (tied at 0.0085) │
│ NDVI Sahel          │ SARIMAX V2   │  0.0075  │ Prophet V3  (0.0080)    │
│ SST North Atlantic  │ Prophet V1   │  0.2855  │ SARIMAX V2  (0.3623)    │
│ SST Indian Ocean    │ TFT V2       │  0.1827  │ N-BEATS V3  (0.2119)    │
└─────────────────────┴──────────────┴──────────┴──────────────────────────┘
```

> **The headline finding:** Model-data structural alignment beats model complexity. Every time.
> But the data regime boundary experiment tells a more interesting story — read on.

---

## 🧠 The Seven Contenders

```
CLASSICAL STATISTICAL          GRADIENT BOOSTING         DEEP LEARNING
━━━━━━━━━━━━━━━━━━━━━         ━━━━━━━━━━━━━━━━━         ━━━━━━━━━━━━━
  ARIMA (p,d,q)                   XGBoost                   N-BEATS
  ├─ V1: fixed (1,1,1)            ├─ V1: lag_12             ├─ V1: default
  └─ V2: AIC auto_arima           ├─ V2: lag_24*            ├─ V2: interpretable
                                   └─ V3: lag_12 ✓          └─ V3: generic stack

  SARIMAX (p,d,q)(P,D,Q)₁₂                                  TFT
  ├─ V1: fixed seasonal           DECOMPOSITION             ├─ V1: hidden=32
  └─ V2: AIC seasonal ✓          ━━━━━━━━━━━━━━            └─ V2: hidden=64 ✓
                                   Prophet
                                   ├─ V1: defaults           FOUNDATION MODEL
                                   ├─ V2: CV grid            ━━━━━━━━━━━━━━━━
                                   └─ V3: changepoints        Chronos-Bolt
                                                              └─ zero-shot only
                                   
* XGBoost V2 excluded from primary comparison (lag asymmetry — see dissertation §3.7)
```

---

## 🧪 Experimental Structure

### Phase A — Univariate Benchmark
All 7 models trained on each series independently. Versioned tuning from V1 baseline → V2 → V3. Best version per series selected for final comparison.

### Phase B — CO₂ as Exogenous Variable
Four models (SARIMAX, Prophet, XGBoost, NBEATSx) tested with atmospheric CO₂ concentration as an exogenous predictor. The result? A **three-model directional consensus** — the most novel finding of the study.

```
CO₂ Effect Direction:

  NDVI Amazon    │ ████ HARMS  (all 3 models agree · p_chance ≤ 0.125)
  NDVI Sahel     │ ████ HELPS  (all 3 models agree · p_chance ≤ 0.125)
  SST N.Atlantic │ ████ HELPS  (all 3 models agree · p_chance ≤ 0.125)
  SST Indian Oc. │ ████ HARMS  (all 3 models agree · p_chance ≤ 0.125)
  
  Most striking: Amazon CO₂ coefficient p < 0.001 (highly significant)
                 yet degrades MAE by 129%. Stats significance ≠ forecast utility.
```

### Phase C — Synthetic Data Extension
The question nobody asks but everyone should: *is SARIMAX actually better, or just better at 252 observations?*

Extended training to **~851 observations** using SARIMAX simulation (NDVI) and empirical resampling (SST). Result:

```
N-BEATS:      ↓ 29% MAE on Amazon  │  ↓ 10% on Sahel
Chronos-Bolt: ↓ 13% MAE on Amazon  │  ~flat on Sahel
TFT:          ↑ 57% (encoder window limitation — doesn't benefit from extended history)

Conclusion: This is a DATA REGIME finding, not an architectural verdict.
            At thousands of observations (operational archives, Sentinel-2 density),
            the scaling signal is unambiguous.
```

---

## 📁 Repository Structure

```
fyp-environmental-forecasting/
│
├── 📂 data/
│   ├── raw/
│   │   ├── co2_monthly_global.csv          # NOAA GML Mauna Loa CO₂
│   │   ├── ndvi_monthly_regional.csv       # MODIS MOD13A3 extracted via GEE
│   │   └── sst_monthly_regional.csv        # NOAA ERSSTv5 extracted via GEE
│   └── processed/
│       ├── co2_monthly.csv                 # Aligned & split
│       ├── ndvi_amazon_train/test.csv
│       ├── ndvi_sahel_train/test.csv
│       ├── sst_atlantic_train/test.csv
│       └── sst_indian_train/test.csv
│
├── 📂 notebooks/
│   ├── 01_data_collection.ipynb            # GEE extraction + CO₂ download
│   ├── 02_preprocessing.ipynb              # ADF, STL, train/test split
│   ├── 03_arima.ipynb                      # ARIMA V1 + V2 (auto_arima)
│   ├── 04_sarimax.ipynb                    # SARIMAX V1/V2 + Phase B CO₂
│   ├── 05_prophet.ipynb                    # Prophet V1/V2/V3 + Phase B CO₂
│   ├── 06_xgboost.ipynb                    # XGBoost V1/V2/V3 + Phase B CO₂
│   ├── 07_nbeats.ipynb                     # N-BEATS V1/V2/V3 + NBEATSx CO₂
│   ├── 08_chronos.ipynb                    # Chronos-Bolt zero-shot
│   ├── 09_tft.ipynb                        # TFT V1/V2 + attention heatmaps
│   └── 10_synthetic_experiment.ipynb       # Phase C: data regime boundary
│
├── 📂 outputs/
│   └── figures/                            # All 90+ generated figures (PNG)
│
├── 📂 scripts/
│   └── gee_extraction/                     # GEE JavaScript extraction scripts
│
├── 📄 README.md                            # You are here
└── 📄 .gitignore
```

---

## ⚡ Quickstart

### Prerequisites

```bash
# Python 3.12+ required
git clone https://github.com/Xiuyifanh/fyp-environmental-forecasting.git
cd fyp-environmental-forecasting

# Create virtual environment
python -m venv venv

# Activate (Windows CMD — not PowerShell)
venv\Scripts\activate.bat

# Activate (macOS/Linux)
source venv/bin/activate
```

### Install Dependencies

```bash
pip install pandas numpy matplotlib seaborn scipy
pip install statsmodels pmdarima
pip install prophet
pip install xgboost scikit-learn
pip install neuralforecast
pip install pytorch-forecasting pytorch-lightning
pip install git+https://github.com/amazon-science/chronos-forecasting.git
pip install earthengine-api jupyter ipykernel
```

### Run the Notebooks (in order)

```bash
jupyter notebook

# Start from 01_data_collection.ipynb if extracting fresh data from GEE
# OR start from 03_arima.ipynb if using the pre-extracted CSVs in data/
```

> ⚠️ **GEE note:** Notebook 01 requires a Google Earth Engine account. Register at [earthengine.google.com](https://earthengine.google.com) and authenticate with `earthengine authenticate` before running.

> ⚠️ **Chronos note:** The `predict()` argument is `inputs=` not `context=`. This tripped me up for an embarrassingly long time.

> ⚠️ **Windows users:** Use CMD, not PowerShell. PowerShell doesn't support `&&` operators in the way you'd expect and will quietly ruin your day.

---

## 🔬 Model Configuration Reference

<details>
<summary><b>ARIMA / SARIMAX</b> — click to expand</summary>

```python
# V1 — fixed baseline
ARIMA(order=(1,1,1))
SARIMAX(order=(1,1,1), seasonal_order=(1,1,1,12))

# V2 — AIC-guided auto selection
import pmdarima as pm
auto_model = pm.auto_arima(series, m=12, seasonal=True, 
                            information_criterion='aic', stepwise=True)
# Library: statsmodels (Seabold & Perktold, 2010)
```
</details>

<details>
<summary><b>Prophet</b> — click to expand</summary>

```python
# V1 — defaults
model = Prophet(seasonality_mode='additive', yearly_seasonality=True)

# V2 — cross-validated grid search
from prophet.diagnostics import cross_validation, performance_metrics
param_grid = {'changepoint_prior_scale': [0.001, 0.01, 0.05],
              'seasonality_prior_scale': [1.0, 5.0, 10.0, 20.0]}

# Phase B — CO₂ exogenous
model.add_regressor('co2_mean')
# Library: prophet (Taylor & Letham, 2018)
```
</details>

<details>
<summary><b>XGBoost</b> — click to expand</summary>

```python
# Features: lag_1, lag_2, lag_3, lag_6, lag_12, month_sin, month_cos,
#           rolling_mean_3, rolling_mean_12, rolling_std_3, yoy_diff

# V3 (asymmetry-corrected, primary comparison version)
from xgboost import XGBRegressor
model = XGBRegressor(n_estimators=500, learning_rate=0.05, 
                     max_depth=5, subsample=0.8)
# Note: V2 (lag_24) excluded from primary table — lag asymmetry documented in §3.7
# Library: xgboost (Chen & Guestrin, 2016)
```
</details>

<details>
<summary><b>N-BEATS</b> — click to expand</summary>

```python
from neuralforecast import NeuralForecast
from neuralforecast.models import NBEATS

# V1 — interpretable stack (trend + seasonality)
models = [NBEATS(h=48, input_size=24, 
                 stack_types=['identity','trend','seasonality'],
                 n_blocks=[1,1,1], mlp_units=[[256,256]]*3,
                 max_steps=300)]

# V3 — generic identity stack
models = [NBEATS(h=48, input_size=36,
                 stack_types=['identity','identity','identity'],
                 n_blocks=[3,3,3], mlp_units=[[512,512]]*3,
                 max_steps=1000)]
# Library: neuralforecast/Nixtla (Olivares et al., 2022)
```
</details>

<details>
<summary><b>TFT</b> — click to expand</summary>

```python
from pytorch_forecasting import TemporalFusionTransformer

# V2 — final configuration
tft = TemporalFusionTransformer.from_dataset(
    training,
    hidden_size=64,
    attention_head_size=4,
    dropout=0.05,
    hidden_continuous_size=32,
    learning_rate=1e-3,
)
# Encoder length: 36 | Batch size: 32 | Early stopping patience: 10
# Library: pytorch-forecasting (Beitner, 2020)
```
</details>

<details>
<summary><b>Chronos-Bolt</b> — click to expand</summary>

```python
from chronos import ChronosBoltPipeline
import torch

pipeline = ChronosBoltPipeline.from_pretrained(
    "amazon/chronos-bolt-small",
    device_map="cpu",
    torch_dtype=torch.float32,
)

# Zero-shot — no training, no fine-tuning
forecast = pipeline.predict(
    inputs=[torch.tensor(train_series, dtype=torch.float32)],
    prediction_length=48,
    limit_prediction_length=False,
)
# The argument is inputs= not context= — you're welcome.
# Library: chronos (Ansari et al., 2024)
```
</details>

---

## 📊 Full Results Tables

### Phase A — Best Version per Model (MAE)

| Series | ARIMA | SARIMAX | Prophet | N-BEATS | TFT | Chronos |
|--------|-------|---------|---------|---------|-----|---------|
| NDVI Amazon | 0.0188 | **0.0085** | 0.0104 | 0.0104 | 0.0085 | 0.0140 |
| NDVI Sahel | 0.0430 | **0.0075** | 0.0080 | 0.0080 | 0.0104 | 0.0107 |
| SST N. Atlantic | 2.3463 | 0.3623 | **0.2855** | 0.3942 | 0.4135 | 0.7766 |
| SST Indian Ocean | 0.5816 | 0.2339 | 0.2510 | 0.2119 | **0.1827** | 0.3672 |

### Phase B — CO₂ Exogenous Effect (SARIMAX, Phase A base → Phase B MAE)

| Series | Phase A | Phase B | Δ% | Verdict |
|--------|---------|---------|-----|---------|
| NDVI Amazon | 0.0091 | 0.0208 | +129% | ❌ CO₂ harms |
| NDVI Sahel | 0.0092 | 0.0079 | −14% | ✅ CO₂ helps |
| SST N. Atlantic | 0.3904 | 0.3406 | −13% | ✅ CO₂ helps |
| SST Indian Ocean | 0.2339 | 0.2492 | +6.5% | ❌ CO₂ harms |

### Phase C — Synthetic Extension (Real vs +851-obs training)

| Series | Model | Real MAE | Synth MAE | Δ% |
|--------|-------|----------|-----------|-----|
| NDVI Amazon | N-BEATS | 0.0200 | 0.0142 | −29% |
| NDVI Amazon | Chronos | 0.0140 | 0.0122 | −13% |
| NDVI Sahel | N-BEATS | 0.0157 | 0.0142 | −10% |
| NDVI Sahel | TFT | 0.0104 | 0.0101 | −3% |

---

## 🗺️ Study Regions

```
         NDVI Sahel                    
    (-18°,10°) → (40°,20°)    
         🌾 Arid transition zone        
         Pronounced monomodal cycle     
                                        
NDVI Amazon                        SST North Atlantic
(-72°,-15°) → (-48°,5°)           (-70°,30°) → (0°,65°)
🌿 Strong annual greenness cycle   🌊 Stable sinusoidal annual cycle
   ENSO-driven inter-annual var.      AMO decadal variability
   
                         SST Indian Ocean
                         (45°,-30°) → (105°,15°)
                         🌡️ Fastest-warming ocean basin
                            Indian Ocean Dipole dynamics
```

All regions extracted via GEE using `ee.Geometry.Rectangle()` bounding boxes with `ee.Reducer.mean()` spatial aggregation. See `scripts/gee_extraction/` for the full JavaScript.

---

## 🔑 Key Findings

**1. Model-data structural match > model complexity**
Classical models dominate on series with stable periodic structure. TFT wins where inter-annual variability is highest. No universal winner. This is not a failure of deep learning — it's a demonstration that the question "which model is best?" is fundamentally the wrong question.

**2. The CO₂ directional consensus is a novel empirical contribution**
Three independent model classes agree on the direction of CO₂ effect for every series. P(agreement by chance) ≤ 0.125 per series. The Amazon finding — statistically significant coefficient that degrades forecasts by 129% — is a textbook case of statistical significance without forecast utility. ENSO and deforestation confound the linear CO₂ signal in the Amazon.

**3. This is a data regime boundary study, not an architectural competition**
SARIMAX was never tested at 851 observations. N-BEATS improving 29% with 3.4× more data establishes a clear scaling signal. Chronos-Bolt beating ARIMA with zero training is remarkable for a zero-shot model. The trajectory toward deep learning and foundation model dominance as satellite data density grows is empirically supported.

---

## 📚 Citation

If you use this code, methodology, or results in your own work:

```bibtex
@misc{raut2026comparative,
  author    = {Raut, Nihaar},
  title     = {Comparative Forecasting of Satellite-Derived Environmental 
               Time Series Using Google Earth Engine: A Benchmark Study 
               Across Statistical, Machine Learning, and Foundation Model Paradigms},
  year      = {2026},
  school    = {University of Greenwich},
  type      = {BSc Computer Science Final Year Project},
  url       = {https://github.com/Xiuyifanh/fyp-environmental-forecasting}
}
```

---

## 🛠️ Tech Stack

| Category | Tools |
|----------|-------|
| **Data extraction** | Google Earth Engine (JavaScript API), NOAA GML API |
| **Data processing** | Python 3.12, pandas, NumPy, SciPy |
| **Statistical models** | statsmodels, pmdarima |
| **ML models** | xgboost, scikit-learn |
| **Deep learning** | neuralforecast (Nixtla), pytorch-forecasting, PyTorch Lightning |
| **Foundation model** | Amazon Chronos-Bolt (`amazon/chronos-bolt-small`) |
| **Visualisation** | matplotlib, seaborn |
| **Environment** | VS Code, Jupyter, Git, CMD (not PowerShell — never PowerShell) |

---

## 👤 Author

**Nihaar Raut** · 001309432  
BSc (Hons) Computer Science · University of Greenwich  
Supervised by Dr. Ik Soo Lim  
April 2026

---

---

> *"Choosing a forecasting model for an environmental series is an act of scientific reasoning about the structure of that series, not a competition to be won by the largest architecture."*

**🛰️ Seven models. Four series. One data regime boundary.**
