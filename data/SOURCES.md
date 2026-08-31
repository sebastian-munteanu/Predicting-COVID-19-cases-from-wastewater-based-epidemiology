# Where each input file came from

Every file here is public and free. If any are missing from this copy of the
project (they total about 360 MB, so they may have been stripped for sharing),
these are the places to get them again.

## Wastewater

**`Final_EMHP_concentration_October_2021_data.ods`**
Environmental Monitoring for Health Protection programme, published by UKHSA.
The sheet used is `Daily_concentrations`: 269 sewage treatment works, one column
per day from 2021-06-01 to 2021-09-20. Note there are 111 date columns, not 112 —
2021-07-17 is absent from the file.

## COVID-19 cases

All from the UKHSA COVID-19 dashboard archive:
https://ukhsa-dashboard.data.gov.uk/covid-19-archive-data-download

- `ltla_newCasesBySpecimenDate.csv`            (from cases.zip)
- `ltla_newCasesBySpecimenDateRollingSum.csv`  (from cases.zip)
- `ltla_newCasesBySpecimenDateRollingRate.csv` (from cases.zip)

Watch the date formats: `newCasesBySpecimenDate` and `RollingRate` are DD/MM/YYYY,
`RollingSum` is YYYY-MM-DD. The notebooks parse each with an explicit format
because inferring would silently swap day and month in some rows.

## Sewage catchment geography

From Hoffmann et al. (2023), *Wastewater catchment areas in Great Britain*:

- `lsoa_catchment_lookup.csv`      — catchment to LSOA overlaps, with areas
- `waterbase_catchment_lookup.csv` — catchment identifier to treatment works code

## ONS geography and population

- `LSOA11_WD21_LAD21_EW_LU_V2.xlsx` — LSOA (2011) to Ward (2021) to LAD (2021)
  https://open-geography-portalx-ons.hub.arcgis.com/documents/ff21f0cfbdcc4206906920b3a8858867

- `Output_Area_to_..._(December_2011)_Lookup_in_England_and_Wales.csv`
  The 2011-vintage equivalent. Used only to build a crosswalk between the two
  boundary vintages, which is needed because the case data mixes them.

- `SAPE22DT2-mid-2019-lsoa-syoa-estimates-unformatted.xlsx` — mid-2019 population
  by LSOA and single year of age. Sheet `Mid-2019 Persons`, data starts row 5.

- `SAM_for_2011_Census_Areas/SAM_LSOA_DEC_2011_EW.csv` — land area per LSOA,
  used for population density. Latin-1 encoded, not UTF-8.
  https://geoportal.statistics.gov.uk/datasets/c842615ec98b44209b655dd74a19dbdd

## Deprivation

**`File_7_-_All_IoD2019_Scores__Ranks__Deciles_and_Population_Denominators_3.csv`**
English Indices of Deprivation 2019, LSOA level.
https://www.gov.uk/government/statistics/english-indices-of-deprivation-2019

The LSOA-level file is used rather than the ready-made local authority summary
(File 10), because File 10 is published against 2019 council boundaries and would
reintroduce the boundary-vintage problem the project has to avoid.
