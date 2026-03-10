# Climate-Driven Changes in Annual Precipitation and Water Stress Under RCP4.5

### Comparative Hydroclimatic Assessment of Indonesia and Russia Using Multi-Model CMIP5 Ensembles

## Overview

This repository contains the full analysis pipeline for assessing projected changes in annual precipitation and water stress under the RCP4.5 scenario for Indonesia and Russia using bias-corrected multi-model CMIP5 precipitation simulations.

The study focuses on long-term hydroclimatic changes and their implications for freshwater availability through the **PRCPTOT climate index** and precipitation-derived water stress indicators. It was done as a part of the presentation at the International Scientific and Practical Conference "Earth Sciences - at the Service of Agro-Industrial Complex", The State University of Land Use Planning, Russia.

----------

## Scientific Objective

The main objective of this project is to evaluate how future precipitation regimes may shift under moderate climate forcing and how these changes may influence long-term freshwater stress patterns in two contrasting climatic regions:

* Indonesia (tropical monsoon climate)
* Russia (continental and high-latitude climate)

----------

## Climate Models Used

* HadGEM2-AO
* MPI-ESM-MR
* IPSL-CM5A-LR

### Variable

* `pr` (daily precipitation)

### Experiment Types

* Historical
* RCP4.5

### Temporal Resolution

* Daily (CMOR daily table)

----------

## Study Periods

### Baseline Period

* 1976–2005

### Future Projection Period

* 2041–2070

These 30-year climatological windows were selected to reduce interannual variability and align with standard climate-normal practice.

----------

## Bias Correction

Bias correction was applied prior to impact interpretation using region-specific observational references.

### Indonesia

* CHIRPS
* Monthly resolution
* 0.05° spatial resolution

### Russia

* CRU TS v4.08
* Monthly resolution
* 0.5° spatial resolution

### Method

* Quantile Mapping

Bias correction was performed separately by region to preserve climatological consistency.

---

## Climate Index Calculation

### PRCPTOT

Annual precipitation total from wet days:

$$
PRCPTOT = \sum RR_{ij}, \quad RR \geq 1\ \mathrm{mm/day}
$$

where:

* only wet days ≥ 1 mm/day are included
* annual totals are expressed in mm/year

---

## Analytical Workflow

### 1. Model-wise Processing

For each climate model:

* precipitation unit conversion
* wet-day filtering
* annual PRCPTOT calculation
* regional masking
* baseline and future climatology extraction

---

### 2. Water Stress Assessment

Water stress was derived using precipitation-based climatic supply indicators to identify long-term hydroclimatic pressure.

This represents **climate-driven supply-side water stress**, not direct socio-hydrological demand.

---

### 3. Time Series Analysis

For each model:

* annual PRCPTOT time series
* baseline vs future comparison
* long-term trend inspection

---

### 4. Multi-Model Ensemble

Ensemble mean calculated as:

$$
Ensemble\ Mean = \frac{Model_1 + Model_2 + Model_3}{3}
$$

---

### 5. Uncertainty Estimation

Model spread used as uncertainty estimate:

* inter-model variability
* ensemble spread
* comparative uncertainty maps

---

### 6. Statistical Analysis

Includes:

* climatological mean comparison
* anomaly analysis
* inter-model consistency assessment

---

## Outputs

This repository includes:

* annual precipitation anomaly maps
* water stress distribution maps
* ensemble mean figures
* uncertainty comparison figures
* model-specific time series
* comparative country analysis

---

## Main Python Libraries

* xarray
* numpy
* pandas
* matplotlib
* cartopy

---

## Scope of Interpretation

This study quantifies climate-driven precipitation changes and discusses freshwater implications through literature-based hydroclimatic interpretation.

It does **not** explicitly model:

* river discharge
* water chemistry
* pollutant transport
* water demand dynamics

---

## Author Note

This repository is designed as a reproducible hydroclimatic research workflow and may serve as the basis for future journal manuscript development.
