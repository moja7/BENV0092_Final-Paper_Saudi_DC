# BENV0092_Final-Paper_Saudi_DC

# Optimal Siting of Hyperscale Data Centre Buildings in Saudi Arabia
### A GIS-Based Multi-Criteria Decision Analysis Framework

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Resolution](https://img.shields.io/badge/Resolution-1%20km²-orange)]()
[![Study Area](https://img.shields.io/badge/Study%20Area-1.93M%20km²-lightgrey)]()

---

## Overview

This repository contains the full Python implementation of a seven-phase GIS-MCDA (Geographic Information System — Multi-Criteria Decision Analysis) framework for identifying optimal hyperscale data centre building locations in Saudi Arabia. The study applies Analytic Hierarchy Process (AHP) weighting to seven spatial criteria, evaluates 1,930,888 km² of national land area at 1 km² resolution, and identifies 20 priority zones offering a 21.6% chiller COP advantage over existing DC locations.

The framework was developed as part of the MSc Energy Systems and Data Analytics programme at UCL Bartlett School of Environment, Energy and Resources (BENV0092 — Assessment 2).

---

## Key Findings

| Metric | Value |
|---|---|
| Study area | 1,930,888 km² |
| Buildable area after exclusion | 529,824 km² (27.4%) |
| Priority zones identified | 20 |
| AHP Consistency Ratio | CR = 0.0346 (< 0.10 threshold) |
| SI range (top 20 zones) | 74.5 – 79.3 |
| Mean COP at optimal zones | 4.67 |
| Mean COP at existing DC locations | 3.84 |
| COP improvement | +21.6% |
| Annual cooling energy saving | 1,199 GWh/yr (4.95 GW pipeline) |
| Annual cost saving | USD 95.6 million/yr |
| Lifetime saving per 200 MW facility | USD 79.7 million (20 years) |

---

## Repository Structure

```
saudi-dc-siting/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── notebooks/
│   ├── phase1_data_collection.ipynb        ← OSM, NASA POWER, GADM, SWCC downloads
│   ├── phase2_normalisation.ipynb          ← Min-max normalisation, criterion rasters
│   ├── phase3_boolean_exclusion.ipynb      ← 5 exclusion layers, buildable mask
│   ├── phase4_ahp_weighting.ipynb          ← Pairwise matrix, eigenvector, CR check
│   ├── phase5_suitability_index.ipynb      ← WLC surface computation
│   ├── phase6_site_extraction.ipynb        ← Greedy max-selection, top 20 zones
│   └── phase7_results.ipynb                ← COP analysis, figures, statistics
│
├── outputs/
│   ├── phase3/
│   │   ├── 01_exclusion_mask_map.png
│   │   └── 02_exclusion_breakdown.png
│   ├── phase4/
│   │   └── ahp_heatmap.png
│   └── phase7/
│       ├── 01_final_results_map.png
│       ├── 02_pue_comparison.png
│       ├── 04_spatial_mismatch.png
│       ├── 06a_cop_vs_temperature.png
│       ├── 06b_cooling_power_per_mw.png
│       ├── 06c_annual_cooling_cost.png
│       ├── top20_coordinates.csv
│       └── top20_sites_googlemaps.kml
│
├── web/
│   └── saudi_dc_explorer.html              ← Interactive site explorer (open in browser)
│
└── data/                                   ← ⚠ SEE DATA AVAILABILITY SECTION BELOW
```

---

## ⚠ Data Availability

> **The raw spatial datasets used in this study total approximately 1 GB and exceed GitHub's file size limits. Data files are therefore not included in this repository.**

Data can be provided upon reasonable request by contacting the author (see contact section below). Alternatively, all datasets can be reproduced independently using the download scripts in `phase1_data_collection.ipynb`, which programmatically retrieve data from the following open sources:

| Dataset | Source | Access |
|---|---|---|
| Province and country boundaries | GADM v4.1 | https://gadm.org |
| Climate rasters (T_mean, H35, GHI, wind) | NASA POWER | https://power.larc.nasa.gov |
| High-voltage transmission lines | OpenStreetMap | https://www.openstreetmap.org |
| Electrical substations | OpenStreetMap | https://www.openstreetmap.org |
| Fibre / optical network lines | OpenStreetMap | https://www.openstreetmap.org |
| Subsea cable landing stations | TeleGeography | https://www.submarinecablemap.com |
| Desalination plant locations | SWCC | https://www.swcc.gov.sa |
| Groundwater aquifer extents | WHYMAP / BGR | https://www.whymap.org |
| Seismic PGA values | Al-Haddad et al. (1994) | doi:10.1193/1.1585773 |
| MODON industrial cities | MODON | https://www.modon.gov.sa |
| Special Economic Zones | Saudi CST | https://www.sezone.gov.sa |
| SRTM Digital Elevation Model (90m) | OpenTopography | https://opentopography.org |
| Road network and populated places | OpenStreetMap | https://www.openstreetmap.org |

The Phase 1 notebook contains a fully automated download pipeline for all OSM and NASA POWER datasets. SRTM data requires a free OpenTopography API key (register at https://opentopography.org).

---

## Methodology

The framework follows seven sequential phases:

```
Phase 1 — Data Collection
         ↓
Phase 2 — Normalisation (min-max, 0–100 scale, inversion where required)
         ↓
Phase 3 — Boolean Exclusion (5 categories, 72.6% of land area excluded)
         ↓
Phase 4 — AHP Weighting (7×7 pairwise matrix, CR = 0.0346)
         ↓
Phase 5 — Suitability Index (WLC: SI = Σ wᵢ × Cᵢ)
         ↓
Phase 6 — Site Extraction (greedy maximum-selection from 1 km² surface)
         ↓
Phase 7 — Performance Analysis (Carnot COP framework)
```

**Cooling efficiency** is quantified using the Carnot COP framework:

```
COP_Carnot = T_cold / (T_hot − T_cold)       [temperatures in Kelvin]
COP_real   = 0.50 × COP_Carnot
T_cold     = 280K  (7°C chilled water setpoint, ASHRAE 2021)
T_hot      = T_ambient + 15°C  (air-cooled condenser approach, Goldstein et al. 2017)
```

**Boolean exclusion categories:**

| Code | Layer | Threshold |
|---|---|---|
| A+D | Country border buffers | Yemen < 50 km; all land borders < 100 km |
| B+G | Urban and settlement zones | Cities < 50/30 km; settlements < 10 km |
| C | High seismic risk | PGA > 0.15g |
| E | Remote and inaccessible terrain | Latitude < 20.5°N; elevation > 1,000 m or slope > 5° |
| F | Road inaccessibility | > 50 km from paved highway |

---

## Dependencies

```bash
pip install geopandas rasterio numpy scipy pandas matplotlib
pip install shapely pyproj fiona requests tabulate
```

All notebooks are designed to run on **Google Colab** with no local installation required. GPU is not needed — all computation is CPU-based raster operations.

---

## Interactive Explorer

An interactive web-based site explorer is included at `web/saudi_dc_explorer.html`. Open the file directly in any browser — no server or installation required. Features include:

- Clickable priority zone markers on a live map
- Per-site criterion scores (0–100) with weighted bar charts
- Real-time COP calculation per site
- Existing DC location overlay for spatial comparison
- Google Maps link for each priority zone

---

## License

Raw spatial data sourced from OpenStreetMap is available under the Open Database Licence (ODbL). NASA POWER data is in the public domain. GADM data is licensed for academic use only.
