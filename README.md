# 🌿 EO-based Dynamic Performance Benchmarking for Nature-based Carbon Removal

**Ghana ARR Project | VM0047 · Open Forest Protocol · Gold Standard**

[![Streamlit App](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://dpb-ghana-carbon.streamlit.app)
[![Python 3.10](https://img.shields.io/badge/python-3.10-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

## About This Project

This repository contains the public dashboard and supporting code for an MSc internship project carried out at **RAMANI B.V.** (The Netherlands) in collaboration with the **Faculty of Geo-Information Science and Earth Observation (ITC), University of Twente**.

The project implements Verra's **VM0047 Dynamic Performance Benchmarking (DPB)** methodology using Earth Observation data from **5,754 cashew farms** across the Bono and Bono East regions of Ghana — validating additionality for an Afforestation, Reforestation, and Revegetation (ARR) carbon removal project.

> **Central question:** Has the project area accumulated more vegetation biomass than it would have without the intervention — and can this be proven against a statistically valid, satellite-derived counterfactual?

---

## 🌐 Live Dashboard

The interactive results dashboard is publicly accessible — no login or installation required:

**[dpb-ghana-carbon.streamlit.app](https://dpb-ghana-carbon.streamlit.app)**

The dashboard includes:

| Section | Content |
|---|---|
| 0. Introduction | Project overview, DPB explanation, data description |
| 1. Farm locations | Interactive satellite map of all 5,754 farms |
| 2. NDFI time series | Annual vegetation trends 2017–2023 |
| 3. Performance Benchmark | Annual PB with 90% confidence interval |
| 4. Per-farm PB map | Spatial distribution of farm-level additionality |
| 5. Uncertainty | Bootstrap CI and sensor error propagation |
| 6. Input data uncertainty | Landsat 8 radiometric error propagation (FlowCell framework) |
| 7. Vocabulary map (OWL) | Interactive cross-standard semantic concept map |
| 8. VM0047 summary | Final monitoring report and cross-standard verdict |

---

## 📁 Repository Structure

```
dpb-ghana-carbon/
│
├── app.py                                              # Streamlit dashboard (main entry point)
├── requirements.txt                                    # Python dependencies
├── README.md                                           # This file
│
├── farm_polygons_High_Deforestation_All_Farms.geojson  # Donor pool farm boundaries (2,082 farms)
├── farm_polygons_Low_Deforestation_All_Farms.geojson   # Project farm boundaries (3,672 farms)
│
├── per_farm_pb_2023.csv                                # Per-farm Performance Benchmark values (2023)
├── dpb_semantic_outputs_owl.jsonld                     # JSON-LD results with OWL cross-standard URIs
├── carbon_standards_skos_owl.ttl                       # SKOS + OWL vocabulary (SemFlow compatible)
│
├── vocabulary_map_owl.html                             # Interactive OWL vocabulary visualisation
└── uncertainty_interactive.html                        # Interactive sensor uncertainty map
```

---

## 🔬 Methodology Overview

The DPB workflow follows Verra's VM0047 standard and is implemented as a 12-step reproducible Python pipeline:

```
Step 1   Load farm polygons          GeoPandas · compute centroids · standardise columns
Step 2   Farmer survey join          Merge on Record_id · add region, district, tenure
Step 3   Spatial overview map        Folium interactive map · ESRI satellite basemap
Step 4   NDFI computation (GEE)      Annual Landsat 8 cloud-free median composite 2017–2023
Step 5   NDFI time series            reduceRegion mean · 45m buffer per centroid
Step 6   Categorical filter          Region match · latitude-band fallback (±0.2°)
Step 7   k-NN matching (k=3)         Euclidean on standardised (lon, lat) · NDFI excluded
Step 8   Performance Benchmark       PB = project NDFI − mean matched control NDFI
Step 9   Per-farm spatial analysis   Interactive PB map · distribution histogram
Step 10  Bootstrap uncertainty       n=500 resamples · 90% CI · annual and per-farm level
Step 11  Error propagation           Sensor uncertainty (±0.004 NDFI) · 300 runs · FlowCell
Step 12  Semantic uplift (OWL)       10 concepts · 30 relations · VM0047 + OFP + Gold Standard
```

### Key stocking index: NDFI

```
NDFI = (NIR − SWIR1) / (NIR + SWIR1)
```

NDFI (Normalised Difference Fraction Index) is derived from Landsat 8's near-infrared (Band 5) and shortwave infrared (Band 6) bands. It is preferred over NDVI for tropical agroforestry because it remains sensitive at high biomass levels where NDVI saturates.

---

## 📊 Key Results

| Metric | Value |
|---|---|
| Project farms | 3,672 (low deforestation) |
| Donor pool farms | 2,082 (high deforestation) |
| Monitoring period | 2017–2023 (7 years) |
| Overall Performance Benchmark | +0.00378 NDFI units |
| 90% Bootstrap CI | [+0.00150, +0.00576] |
| Years confirmed | 6 / 7 |
| **Overall verdict** | **✅ ADDITIONALITY CONFIRMED** |

### Annual breakdown

| Year | PB (NDFI) | CI lower | CI upper | Additionality |
|---|---|---|---|---|
| 2017 | −0.00397 | −0.00514 | −0.00289 | ❌ Not confirmed (expected — trees too young) |
| 2018 | +0.00193 | +0.00057 | +0.00332 | ✅ Confirmed |
| 2019 | +0.00455 | +0.00332 | +0.00572 | ✅ Confirmed |
| 2020 | +0.00758 | +0.00628 | +0.00887 | ✅ Confirmed |
| 2021 | +0.00525 | +0.00431 | +0.00630 | ✅ Confirmed |
| 2022 | +0.00541 | +0.00430 | +0.00664 | ✅ Confirmed |
| 2023 | +0.00571 | +0.00433 | +0.00697 | ✅ Confirmed |

> **Note:** PB values are in NDFI index units (dimensionless spectral proxy). Conversion to tCO₂e/ha requires a locally calibrated NDFI-to-AGB regression from field measurements (Phase 4b).

---

## 🔗 Cross-Standard Semantic Vocabulary

A formal OWL vocabulary maps every DPB concept to its equivalent in all three major carbon standards simultaneously. This enables cross-standard interoperability without producing three separate reports.

| DPB Concept | VM0047 | OWL Relation | OFP | OWL Relation | Gold Standard | OWL Relation |
|---|---|---|---|---|---|---|
| Performance Benchmark | Performance Benchmark | `owl:equivalentClass` | Project vs Baseline Performance | `rdfs:subClassOf` | Additionality Indicator | `rdfs:subClassOf` |
| Stocking Index (NDFI) | Stocking Index | `owl:equivalentClass` | Vegetation Index | `rdfs:subClassOf` | Vegetation Cover Proxy | `rdfs:subClassOf` |
| Donor Pool | Donor Pool | `owl:sameAs` | Reference Area Pool | `owl:equivalentClass` | Baseline Reference Pool | `owl:equivalentClass` |
| Matched Controls | Matched Control Plots | `owl:sameAs` | Reference Plots | `owl:equivalentClass` | Baseline Scenario Plots | `owl:equivalentClass` |
| Categorical Filter | Categorical Variables | `owl:sameAs` | Stratification Variables | `owl:equivalentClass` | Baseline Stratification | `owl:equivalentClass` |
| k-NN Matching | Multivariate Matching | `owl:equivalentClass` | Plot Matching | `rdfs:subClassOf` | Comparative Site Analysis | `rdfs:subClassOf` |
| 90% CI | 90% CI (Tier 2) | `owl:sameAs` | 90% CI | `owl:sameAs` | 90% CI | `owl:sameAs` |
| Additionality | Additionality (DPB) | `owl:ObjectProperty` | Additionality (counterfactual) | `owl:ObjectProperty` | Additionality (CDM tool) | `owl:ObjectProperty` |
| Balance Check | Std diff < 0.25 | `owl:equivalentClass` | Covariate Balance Assessment | `rdfs:subClassOf` | Baseline Comparability | `rdfs:subClassOf` |
| Dynamic Baseline | Dynamic Performance Baseline | `owl:equivalentClass` | No equivalent | — | No equivalent | — |

**57% of DPB outputs are directly interoperable across all three standards.** The Dynamic Baseline is the only VM0047-specific concept with no cross-standard equivalent.

---

## 🛠️ Running the Dashboard Locally

### Prerequisites

- Python 3.10 or later
- A Google Earth Engine account (for re-running the satellite extraction cells in the notebook only — the dashboard does not require GEE)

### Installation

```bash
git clone https://github.com/yourname/dpb-ghana-carbon.git
cd dpb-ghana-carbon
pip install -r requirements.txt
streamlit run app.py
```

The dashboard will open at `http://localhost:8501`

### Requirements

```
streamlit
streamlit-folium
folium
geopandas
pandas
numpy
matplotlib
scikit-learn
scipy
Pillow
requests
python-calamine
```

---

## 📓 Jupyter Notebook

The full 12-step DPB analysis is documented in `DPB_Real_Farm_Data.ipynb`. The notebook runs on RAMANI's FlowCell JupyterLab environment with access to Google Earth Engine.

**Sections:**

| Section | Description |
|---|---|
| 1–3 | Data loading, survey join, spatial overview |
| 4–6 | GEE NDFI extraction and time series |
| 7–8 | k-NN matching, DPB calculation, per-farm analysis |
| 9–10 | Bootstrap CI uncertainty, VM0047 monitoring summary |
| 11.1 | Input data uncertainty — sensor error propagation |
| 11.2 | Descriptive statistics of key variables |
| 11.3 | Dependent and independent variable analysis |
| Semantic | OWL vocabulary mapping and JSON-LD export |

> **Reproducibility note:** The satellite extraction (Sections 4–6) requires a Google Earth Engine account. The farm polygon GeoJSON files are proprietary RAMANI data and are included in this repository for dashboard functionality. All analysis code is open source under the MIT licence.

---

## 📂 Data Sources

| Dataset | Source | Role |
|---|---|---|
| Farm polygon GeoJSON (6 files) | RAMANI B.V. | Farm boundaries, deforestation attributes |
| Farmer survey records (~7,000) | RAMANI B.V. via Insyt/Esoko | Region, tenure, crop type |
| Landsat 8 Collection 2 SR | Google Earth Engine (`LANDSAT/LC08/C02/T1_L2`) | Annual NDFI composites 2017–2023 |
| Hansen Global Forest Change | Global Forest Watch | Defor_Percent and Forest_2000_ha fields |

---

## ⚠️ Important Notes

**Units:** All Performance Benchmark values in this repository are in **NDFI index units** (dimensionless, range −1 to +1). They are a spectral proxy for vegetation density — **not tonnes of CO₂**. Conversion to tCO₂e/ha requires a locally calibrated NDFI-to-AGB regression from permanent field plot measurements (Phase 4b work, not yet available).

**Reference year:** The carbon stock baseline is anchored to **year 2000** using the Hansen Global Forest Change dataset (`Forest_2000_ha` field).

**Defor_Percent:** This field represents **cumulative** deforestation from 2001 to the most recent Hansen update (2023). It is not a single-year figure.

---

## 🗺️ Study Area

- **Country:** Ghana
- **Regions:** Bono and Bono East
- **Primary crop:** Cashew (*Anacardium occidentale*)
- **Coordinates:** Lon [−3.07, −1.27] · Lat [6.78, 8.30]
- **Monitoring period:** 2017–2023

---

## 👤 Author

**Collins Edem Hlordzie**
MSc Geoinformation Science and Earth Observation for Environmental Modelling and Management (M-GEO)
Faculty ITC, University of Twente · The Netherlands

**Internship host:** RAMANI B.V., The Netherlands
**Company supervisor:** Valentijn Venus
**ITC supervisor:** Rob Lemmens
**Period:** February 2026 – June 2026

---

## 📄 Licence

This project is licensed under the MIT Licence. See [LICENSE](LICENSE) for details.

The farm polygon data is proprietary to RAMANI B.V. and is included solely for dashboard visualisation purposes. It may not be redistributed or used for purposes beyond replicating this analysis without written permission from RAMANI B.V.

---

## 🔖 Citation

If you use this workflow or methodology in your own work, please cite:

```
Hlordzie, C.E. (2026). EO-based Dynamic Performance Benchmarking for Nature-based
Carbon Removal: Validating and Comparing EO-based Implementations of Verra's VM0047
Methodology for ARR Carbon Projects. MSc Internship Report, University of Twente / ITC
and RAMANI B.V.
```

---

## 🚀 What Comes Next

The following tasks are required before carbon credit issuance can proceed:

- [ ] **Field NFI measurements** — establish permanent sample plots co-located with Landsat pixels to calibrate the NDFI-to-AGB regression
- [ ] **Leakage assessment** — VM0047 requires displacement analysis before credits can be issued
- [ ] **SemFlow BoK upload** — upload `carbon_standards_skos_owl.ttl` to RAMANI's platform to make all concept URIs live and resolvable
- [ ] **Annual automation** — schedule GEE extraction and DPB recalculation every January
- [ ] **Sentinel-2 integration** — supplement Landsat 8 (30m) with Sentinel-2 (10m) for cloud-gap filling

---

*Built with 🌍 Earth Observation · Python · Google Earth Engine · Streamlit · Folium · OWL*
