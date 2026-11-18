# Predicting COVID-19 cases from wastewater-based epidemiology

## Summary

MSc Statistics and Data Science dissertation.

Reported COVID-19 case counts measure testing behaviour as much as infection, since a case exists only where somebody sought a test, obtained one, and had the result recorded. Concentrations of SARS-CoV-2 RNA in wastewater are not subject to that selection, which is why national monitoring programmes were established during the pandemic. This study evaluates how accurately wastewater concentrations predict reported case counts at local authority level in England, and whether a machine learning method outperforms linear regression given the same inputs.

Wastewater is sampled at treatment works, whose catchments follow the sewer network, while cases are reported by local authority, whose boundaries are administrative. The two do not line up, so a population-weighted link between them is built from small-area census units and checked against catchment populations the monitoring programme calculated separately, which it matches to within 0.3%. The panel covers 307 local authorities over 112 days, from 1 June to 20 September 2021.

The relationship is not proportional. Ten times more RNA in the wastewater corresponds to roughly 2.3 times more reported cases, so a reading indicates the rough size of an outbreak and the direction it is moving, but cannot be converted into a case count. Accuracy also depends heavily on how far ahead the prediction is made. Compared with reusing a case count from a few weeks earlier, linear regression is worse at one week and 14% better at four. A tuned gradient-boosted ensemble is 35 to 40% more accurate than the regression and beats that benchmark at every horizon tested. The characteristics of an authority matter as much as the wastewater reading. Its population, density, deprivation and age structure account for 56 to 58% of the accuracy, so the same concentration means something different in two different places.

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
