# Climate-Driven Changes in Annual Precipitation and Water Stress Under RCP 4.5

## Overview

This repository contains the full analysis workflow for assessing projected changes in annual precipitation and water stress under the RCP 4.5 scenario for Indonesia and Russia using bias-corrected multi-model CMIP5 precipitation simulations.

The study was done as a part of the presentation at the International Scientific and Practical Conference "Earth Sciences - at the Service of Agro-Industrial Complex", the State University of Land Use Planning, Russia. It focuses on long-term hydroclimatic changes and their implications for freshwater quality through the **PRCPTOT climate index** and precipitation-derived water stress indicators. 

| | Indonesia | Russia |
|---|---|---|
| **Observed reference** | CHIRPS v2.0 (monthly) | CRU-TS v4.08 (monthly) |
| **Bias correction (BC) baseline period** | 1981–2005 | 1976–2005 |
| **Future period** | 2041–2070 | 2041–2070 |
| **Scenario** | RCP 4.5 | RCP 4.5 |
| **GCM resolution** | ~1–2° | ~1–2° |

## Models

| Model | Institution | Calendar | Historical files | RCP 4.5 files |
|---|---|---|---|---|
| **HadGEM2-AO** | NIMR/KMA | 360-day | Single file 1860–2005 | Single file 2006–2100 |
| **MPI-ESM-MR** | MPI-M | Standard | 4 × decadal files 1970–2005 | 4 × decadal files 2040–2079 |
| **IPSL-CM5A-LR** | IPSL | 360-day | Single file 1950–2005 | Single file 2006–2205 |

All files use the variable `pr` (kg m⁻² s⁻¹), converted to mm/day by multiplying by 86,400.

## Quick Start

1. Install dependencies:
```
pip install xarray numpy pandas matplotlib cartopy scipy dask distributed netCDF4
conda install -c conda-forge cartopy ← # Cartopy may require system-level dependencies
```
2. Edit `[PROJECT_DIR]` locations in every notebook to your desired locations
3. Run `multi-model_ensemble.ipynb`
4. Run `bias_correction_Indonesia.ipynb`
5. Run `bias_correction_Russia.ipynb`
6. Run `Final_Analysis.ipynb`

---

## Methodology

### 1. Data Preprocessing
- Daily `pr` aggregated to **monthly totals** (mm/month) via `resample(time='MS').sum()`
- GCM data clipped to regional bounds with a 2° padding beyond the display extent to ensure full coastal coverage after interpolation
- Observations regridded **down** to the coarse GCM grid (bilinear) because the EQM must operate at the GCM's native spatial scale

### 2. Empirical Quantile Mapping (EQM)
- **99 quantiles** computed separately for each calendar month (Jan↔Jan, …, Dec↔Dec) to preserve the seasonal cycle
- Transfer function fitted via **PCHIP interpolation** (monotone, no overshoot)
- Out-of-range values clipped to observed [min, max]; negative values clipped to zero
- Applied to both baseline and future GCM periods using the baseline-trained transfer function

### 3. PRCPTOT
Annual total precipitation from wet days recomputed from bias-corrected monthly values using `resample(time='YE').sum()`

$$
PRCPTOT = \sum RR_{ij}, \quad RR \geq 1\ \mathrm{mm/day}
$$

where:
* only wet days ≥ 1 mm/day are included
* annual totals are expressed in mm/year

### 4. Ensemble & CWSI
- 3-model ensemble mean and standard deviation computed on a common reference grid
- **Water Stress Index (WSI):**
  ```
  WSI = (PRCPTOT_future − PRCPTOT_baseline) / PRCPTOT_baseline × 100 %
  ```
- **CWSI classification:**

  | Class | Threshold |
  |---|---|
  | Low stress | WSI ≥ −10% |
  | Moderate stress | −20% ≤ WSI < −10% |
  | High stress | WSI < −20% |

  Percentages computed over **valid land pixels only** (ocean NaNs excluded from denominator).

## Notebooks

### `multi-model_ensemble.ipynb`
1. Imports & core scientific libraries
2. Configuration of model directories, file paths, and CMIP5 GCM lists
3. Visualisation helpers (`_add_ocean_mask`, `_upsample_bilinear`, `_upsample_nearest`, `_add_style`, `_pcolormesh_diverging`, `_pcolormesh_sequential`, `_apply_water_stress_style`)
4. Load and merge per-model NetCDF files (`HadGEM2-AO`, `MPI-ESM-MR`, `IPSL-CM5A-LR`)
5. Calendar harmonisation and temporal alignment across historical and RCP4.5 periods
6. Per-model annual PRCPTOT extraction for baseline and future climatologies
7. Multi-model ensemble mean calculation
8. Regional extraction for Indonesia and Russia
9. Climate Water Stress Index (CWSI) calculation (`calculate_water_stress_index`, `classify_water_stress`, `calculate_stress_percentages`)
10. Comparative time-series plots for all GCMs and ensemble means
11. Ensemble spatial maps (baseline, future, anomaly, uncertainty, water stress)
12. Summary statistics export (CSV)
13. Ensemble NetCDF export
14. Organise outputs into final results directory


### `bias_correction_Indonesia.ipynb`
1. Imports & Dask client
2. Configuration & file paths
3. Visualisation helpers (`_smooth_and_pad`, `plot_div`, `plot_seq`, `plot_stress`)
4. EQM function
5. Load CHIRPS v2.0 observations
6. BC pipeline (all 3 GCMs)
7. Save per-model NetCDFs
8. Ensemble statistics & CWSI
9. Save ensemble NetCDFs
10. Multi-model ensemble map (9-panel figure)
11. Validation time series (BC baseline vs CHIRPS)
12. Summary CSV

### `bias_correction_Russia.ipynb`
Identical structure to the Indonesia notebook, with:
- CRU-TS v4.08 as the observational reference
- Baseline period 1976–2005
- `ccrs.PlateCarree(central_longitude=105)` for correct Russia map projection
- Adjusted colorbar `shrink` values for the wide aspect ratio

### `Final_Analysis.ipynb`
1. Imports
2. Configuration
3. Visualisation helpers (same as BC notebooks)
4. Load BC PRCPTOT NetCDFs
5. Build multi-model ensembles & CWSI
6. Seasonal decomposition (monthly BC files if available; annual proxy fallback)
7. **Fig 1:** 4-panel: Baseline · Future · Anomaly · Uncertainty
8. **Fig 2:** WSI continuous + CWSI categories
9. **Fig 3:** Individual GCM anomalies vs ensemble (1×4 panels)
10. **Fig 4:** Seasonal anomaly panels (DJF / MAM / JJA / SON)
11. **Fig 5:** Annual PRCPTOT time series with ensemble spread
12. **Fig 6:** Anomaly & model agreement maps
13. Summary statistics CSV
14. CWSI distribution table
15. Export final ensemble NetCDFs (12 files)
16. Freshwater quality implication narrative

---

## Requirements

```
python
xarray
numpy
pandas
matplotlib
cartopy
scipy
dask[distributed]
netCDF4
```

## Outputs

### Figures (300 dpi PNG)
| File | Description |
|---|---|
| `BC_MultiModel_{Region}_Analysis.png` | 9-panel multi-model ensemble map |
| `BC_Validation_{Region}.png` | BC baseline vs observed time series |
| `Fig1_{Region}_4panel.png` | Baseline / Future / Anomaly / Uncertainty |
| `Fig2_{Region}_WSI.png` | WSI continuous + CWSI categories |
| `Fig3_{Region}_ModelComparison.png` | Individual GCM anomalies vs ensemble |
| `Fig4_{Region}_Seasonal.png` | DJF / MAM / JJA / SON anomaly panels |
| `Fig5_TimeSeries_BothRegions.png` | Annual PRCPTOT time series |
| `Fig6_{Region}_ModelAgreement.png` | Anomaly & % model agreement |

### NetCDF outputs
Per-model BC PRCPTOT (annual, written by BC notebooks):
- `PRCPTOT_bc_baseline_{Model}_{Region}.nc`
- `PRCPTOT_bc_future_{Model}_{Region}.nc`

Final ensemble NetCDFs (written by Final_Analysis.ipynb):
- `Final_Ensemble_{Baseline|Future|Anomaly|Uncertainty|WSI|WaterStress}_{Region}.nc`

### CSV tables
- `BC_Summary_{Region}.csv` — per-model and ensemble BC statistics
- `Final_Summary_Statistics.csv` — combined Indonesia + Russia summary
- `Final_CWSI_Summary.csv` — WSI range and stress class distribution

## Scope of Interpretation

This study quantifies climate-driven precipitation changes and discusses freshwater implications through literature-based hydroclimatic interpretation.

It does **not** explicitly model:

* river discharge
* water chemistry
* pollutant transport
* water demand dynamics

## Known Limitations

- **Northern Siberia coverage:** The Arctic coastal strip above ~72–75°N appears uncoloured in Russia maps. This reflects the GCM land-sea mask at 1–2° resolution and the CRU-TS observational coverage because both treat these highly irregular coastal cells as ocean.
- **Seasonal analysis:** The BC notebooks export annual PRCPTOT only. `Final_Analysis.ipynb` will use an annual proxy for seasonal panels unless there are additions such as a monthly NetCDF export step to the BC notebooks.
- **Kaliningrad:** The Russian exclave is within the display extent but may render as a small uncoloured dot depending on GCM land coverage at that grid resolution.

---

## Citation

- **CHIRPS:** Funk et al. (2015). *The climate hazards infrared precipitation with stations — a new environmental record for monitoring extremes.* Scientific Data, 2, 150066. https://doi.org/10.1038/sdata.2015.66
- **CRU-TS:** Harris et al. (2020). *Version 4 of the CRU TS monthly high-resolution gridded multivariate climate dataset.* Scientific Data, 7, 109. https://doi.org/10.1038/s41597-020-0453-3
- **HadGEM2-AO:** The HadGEM2 Development Team (2011). *MOHC HadGEM2-AO model output.* CMIP5. https://doi.org/10.1594/WDCC/CMIP5.MOHCHAhi
- **MPI-ESM-MR:** Giorgetta et al. (2013). *MPI-M MPI-ESM-MR model output.* CMIP5. https://doi.org/10.1594/WDCC/CMIP5.MPIMMEhi
- **IPSL-CM5A-LR:** Dufresne et al. (2013). *IPSL IPSL-CM5A-LR model output.* CMIP5. https://doi.org/10.1594/WDCC/CMIP5.IPILILhi

## Author Note

This repository is designed as a reproducible hydroclimatic research workflow and may serve as the basis for future journal manuscript development.
