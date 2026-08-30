# Predicting COVID-19 cases from wastewater

MSc dissertation code · Sebastian Munteanu · Department of Mathematical Sciences, University of Bath

## What this is

Four notebooks that link England's 2021 wastewater surveillance data to reported COVID-19 case counts, and then test how well the wastewater signal predicts cases. Study period 1 June – 20 September 2021, covering 269 treatment works and 307 local authorities.

## Getting the data

The `data/` folder is **not included in this repository** — the input files total about 370 MB and every one of them is public. `data/SOURCES.md` lists each file, what it provides, and where to download it, including the traps worth knowing about (the wastewater sheet is missing one date column, the three case files use two different date formats, and the area file is Latin-1 encoded).

Download them into `data/` using the names given in `SOURCES.md` and the notebooks will find them. Everything in `output/` is committed, so the results can be inspected without re-running anything.

## Running it

Python 3.11 with `pandas`, `numpy`, `matplotlib`, `statsmodels`, `scikit-learn`, `xgboost` and `odfpy`.

Open the notebooks from inside the `notebooks/` folder and run them in order, 01 → 04. Each finds `data/` and `output/` relative to its own location, so the project folder can be moved or renamed without editing anything. Notebook 04 takes a few minutes; the others are quick.

```
wbe_analysis/
├── data/         all input files, plus SOURCES.md describing where each came from
├── notebooks/    01–04, run in this order
└── output/       everything the notebooks produce
```

## The notebooks

**01 — Linking treatment works to local authorities.** Wastewater is measured at treatment works, cases are reported by local authority, and the two geographies do not align. This builds the population-weighted link between them and validates it against the monitoring programme's own independently calculated catchment populations.

Writes `stw_ltla_bridge.csv`, `population_validation.csv`, `lad11_to_lad21.csv`.

**02 — Building the combined dataset.** Joins wastewater readings to case counts to produce the analysis panel, one row per authority per day. Four values are recomputed by hand from the raw files as a check on the pipeline.

Writes `rna_cases_panel.csv`, `ltla_population.csv`.

**03 — Local authority characteristics.** Age structure, population density, deprivation and vaccination uptake for each authority.

Writes `ltla_covariates.csv`, `ltla_vaccination.csv`.

**04 — Forecasting cases from wastewater.** Builds the predictor, justifies the specification on validation forecast origins, and reports forecast error against horizon for a linear model and for XGBoost.

Writes `horizon_results.csv`, `contemporaneous_model.csv`, `lag_profile.csv`.

## What the analysis finds

The wastewater signal is real: the contemporaneous elasticity of cases with respect to concentration is 0.36, so a tenfold rise in concentration goes with a 2.3-fold rise in recorded cases. This reproduces at local authority resolution the sub-proportional relationship reported elsewhere.

The forecast horizon determines the answer, for the linear model. At one week, simply reusing the last known case count beats it comfortably. The persistence benchmark degrades as the horizon lengthens while the linear model's error stays nearly flat, and the two cross between three and three and a half weeks. At four weeks the linear model is 14% ahead, using no case data at all.

Authority characteristics carry more than half the accuracy. Adding them to a wastewater-only model reduces its error by 56–58%; neither block works without the other.

Two controls qualify this. Where recent case counts are already available, adding wastewater does not reliably improve the forecast. And vaccination uptake — an unrelated measurement that happens to move smoothly over the same window — forecasts about as well as wastewater does, which means part of the apparent gain is trend-following rather than a local signal. Separating the two would need a study period spanning several waves.

XGBoost, with hyperparameters selected on the validation origins, beats the linear model by 37–43% at every horizon. It also beats the persistence benchmark everywhere — by 17% at one week rising to 48% at four — so the crossing point above is a property of the linear specification rather than of wastewater itself. The linear model's coefficients are still what gets reported, because the elasticity can be compared with the literature and interpreted; the ensemble measures how much the additive form leaves behind, and that gap is the substantive answer to the machine-learning-versus-regression question.

## Known limitations

A single epidemic wave. Eight test forecast origins, with error varying by about 0.01 between origins. Two catchments, Radlett and Plympton, disagree with the programme's own population figures and are still included. Standard errors are now cluster-robust, so the elasticity interval is quotable; the two-way estimator is approximate with only 112 days, so treat the widest interval as indicative.
