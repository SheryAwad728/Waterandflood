# Cleaning memo, descriptive analysis, and limitations (late Oct 2026)

Project: "Preparing for Climate-related Disruptions in Southern California: Evidence from National Flood Insurance Payouts" (ECON 490). Team: Mike Aquino, Shery Awad, Jordan Fuller, Nathan Perish.

## 1. Data Cleaning Memo
- Drafted and revised (final text in `docs/Cleaning_Memo.md`).
- Corrections made along the way: ENSO is absorbed by year fixed effects (not county FE); ONI measures ocean temperature anomalies, not storm intensity; wind-data date ranges fixed; source count made consistent (five sources, not four).

## 2. Descriptive Analysis Brief
- Trends covered: El Niño and claims, insurance take-up variation by county, rainfall–claims correlation, plus economic relevance.
- Numbers checked against R output before writing them up.

## 3. Limitations and Next Steps (regression results)
- Incomplete weather-station coverage.
- No county fixed effects in the model.
- Population is an imperfect exposure proxy.
- Interpretation issues with the wildfire coefficient.
- Caveats on converting coefficients to dollar effects.
- Wording fix: missing data means estimates "may not apply well" to some counties — not that they're "biased" for those counties, since the direction isn't known.

## R notes (CPI join)
- Generic column names like `cpi` collided with leftovers from earlier runs (`.x`/`.y` suffixes) and caused a "`claims_real` must be size 322 or 1, not 80" error.
- Fix: use distinct names (`cpi_fred`, `cpi_tbl`) and `select(-any_of(c("cpi", "claims_real", ...)))` before re-joining so the script can be rerun safely.
- FRED's direct CSV URL sometimes returns HTML (likely rate-limiting). Workaround: download the CSV manually and read it with `skip = 1` and explicit `col_names`.
