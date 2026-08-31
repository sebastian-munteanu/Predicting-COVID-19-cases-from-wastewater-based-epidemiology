# Predicting COVID-19 cases from wastewater-based epidemiology

This repository provides the code and data supporting an MSc dissertation, Department of Mathematical Sciences, University of Bath, 2026. Supervisor: Julian Faraway.

## Summary

Reported COVID-19 case counts depend on testing behaviour and reporting practice; SARS-CoV-2 RNA in wastewater does not. This project asks how well the wastewater signal predicts recorded cases at local authority level, and whether machine learning does so better than linear regression. Wastewater is measured at treatment works and cases are reported by local authority, two geographies that do not align, so a population-weighted bridge between them is built and validated first. The resulting panel covers 307 authorities over 112 days, 1 June to 20 September 2021.

The concentration–incidence relationship is sub-proportional, with an elasticity of 0.36: ten times more RNA corresponds to roughly 2.3 times more cases. Forecast accuracy depends on the horizon — against a persistence benchmark the linear model is worse at one week and 14% better at four, while a tuned gradient-boosted ensemble beats the benchmark throughout. Authority characteristics account for 56–58% of accuracy. Where recent case counts are already available, adding wastewater does not reliably improve the forecast.

## Data

All files are public and included in [`data/`](data/).

* Wastewater RNA concentrations, 269 treatment works: [`Final_EMHP_concentration_October_2021_data.ods`](data/Final_EMHP_concentration_October_2021_data.ods), from the [EMHP programme](https://www.gov.uk/government/publications/monitoring-of-sars-cov-2-rna-in-england-wastewater-monthly-statistics-1-june-to-20-september-2021)
* Case counts by authority: [three CSVs](data/) from the [UKHSA dashboard archive](https://ukhsa-dashboard.data.gov.uk/covid-19-archive-data-download)
* Catchment geography: [`lsoa_catchment_lookup.csv`](data/lsoa_catchment_lookup.csv) and [`waterbase_catchment_lookup.csv`](data/waterbase_catchment_lookup.csv), from [Hoffmann et al.](https://github.com/tillahoffmann/wastewater-catchment-areas)
* Boundary lookups: [2021 vintage](data/LSOA11_WD21_LAD21_EW_LU_V2.xlsx) from the [ONS Open Geography Portal](https://open-geography-portalx-ons.hub.arcgis.com/documents/ff21f0cfbdcc4206906920b3a8858867), [2011 vintage](data/Output_Area_to_Lower_layer_Super_Output_Area_to_Middle_layer_Super_Output_Area_to_Local_Authority_District_%28December_2011%29_Lookup_in_England_and_Wales.csv) from [data.gov.uk](https://ckan.publishing.service.gov.uk/dataset/output-area-2011-to-lsoa-to-msoa-to-lad-december-2011-exact-fit-lookup-in-ew)
* Population by LSOA and age: [`SAPE22DT2-mid-2019...xlsx`](data/SAPE22DT2-mid-2019-lsoa-syoa-estimates-unformatted.xlsx), from [ONS](https://www.ons.gov.uk/peoplepopulationandcommunity/populationandmigration/populationestimates/datasets/lowersuperoutputareamidyearpopulationestimates)
* Land area by LSOA: [`SAM_LSOA_DEC_2011_EW.csv`](data/SAM_for_2011_Census_Areas/SAM_LSOA_DEC_2011_EW.csv), from [ONS](https://geoportal.statistics.gov.uk/datasets/c842615ec98b44209b655dd74a19dbdd)
* Deprivation: [`File_7_-_All_IoD2019_Scores...csv`](data/File_7_-_All_IoD2019_Scores__Ranks__Deciles_and_Population_Denominators_3.csv), from the [Indices of Deprivation 2019](https://www.gov.uk/government/statistics/english-indices-of-deprivation-2019)

Full provenance is in [`data/SOURCES.md`](data/SOURCES.md).

## Code

Four notebooks in [`notebooks/`](notebooks/), run in order.

* [`01_build_and_validate_bridge.ipynb`](notebooks/01_build_and_validate_bridge.ipynb) — links treatment works to 2021 local authorities by shared population, and validates the result against the monitoring programme's own catchment figures
* [`02_build_combined_dataset.ipynb`](notebooks/02_build_combined_dataset.ipynb) — applies the bridge to build the analysis panel, and checks four values by hand against the raw files
* [`03_ltla_covariates.ipynb`](notebooks/03_ltla_covariates.ipynb) — aggregates age structure, density and deprivation onto 2021 authorities
* [`04_models.ipynb`](notebooks/04_models.ipynb) — fits the linear model and the gradient-boosted ensemble, and evaluates both by rolling-origin forecasting

Everything they produce is committed to [`output/`](output/), so the results can be read without running anything.

## Running it

```
pip install -r requirements.txt
```

Then run the notebooks in order. Each finds `data/` and `output/` relative to its own location, so no paths need editing. Notebook 04 takes a few minutes; the rest are quick.
