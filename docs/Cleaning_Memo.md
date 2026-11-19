# Cleaning Memo: Southern California NFIP Climate Panel

**Re:** Construction of the Southern California NFIP Claims & Climate Panel (1980–2025)

**Prepared for:** ECON 490 project file

**Dataset files:** socal_nfip_climate_panel.xlsx / socal_nfip_climate_panel.csv; see accompanying Data Log for full variable documentation.

## Unit of Analysis

The dataset is structured as a county-year panel: each observation corresponds to one of seven Southern California counties (Los Angeles, Orange, Riverside, San Bernardino, San Diego, Santa Barbara, and Ventura) in a single calendar year, spanning 1980 through 2025 for 322 total observations. This unit of analysis follows directly from the research question, which concerns how annual rainfall, ENSO intensity, and the prior year's wildfire acreage relate to flood insurance claims within a county over time. County-year is also the finest resolution that all four underlying sources support jointly: NFIP claims carry a county code and a loss year, NOAA weather stations report annual summaries, CAL FIRE perimeters are tagged with a fire year, and ENSO is published as a single basin-wide seasonal signal. Matching the panel's resolution to what these four sources report keeps every variable on the same consistent annual, county-level basis.

## Sources, Cleaning, and Merging

Five source datasets were cleaned independently and then merged by matching on county and year:

- **NFIP claims** (NfipClaimsV3.csv, FEMA): filtered to the seven target counties by county code, then aggregated from individual claims to county-year totals by summing building damage, contents damage, and compliance costs, with an accompanying count of claims filed. Processed in R (NFIPClaims.R).
- **NFIP policy exposure** (FEMA OpenFEMA policy-level data, NfipPolicies v3): policy records were filtered to the seven target counties using the county portion of the censusGeoid field, then each policy's insured-unit count (policyCount, which exceeds 1 for Residential Condominium Building Association Policy master contracts) was attributed to every calendar year its effective-to-termination window overlapped and summed by county-year. This yields active_policies, an exposure measure intended to distinguish a rise in claims driven by worsening flood conditions from one driven simply by more insured properties.
- **Wildfire acreage** (CAL FIRE fire-perimeter geodatabase): each mapped fire perimeter was spatially overlaid on county boundaries, and where a fire spanned multiple counties, its acreage was apportioned in proportion to the share of its area falling within each county, so each county's total reflects only its own share of a cross-boundary fire. The resulting county-year totals were also shifted back one year to construct a prior-year wildfire variable, intended as a proxy for post-fire soil conditions.
- **Rainfall and related climate variables** (NOAA GSOY station data): a representative weather station was assigned to each county based on proximity to its population center and length of record. In years when the primary station lacked data, a documented secondary station supplied the value instead, with the contributing station recorded in a companion column for each climate variable. This substitution logic was applied separately to each variable, since wind speed (AWND) is reported only by automated airport-class stations and therefore follows a different coverage pattern than the precipitation measures.
- **ENSO intensity** (NOAA's Oceanic Niño Index): the twelve overlapping seasonal readings published each year were condensed into annual summary measures, including the peak and minimum seasonal anomaly. Because ENSO is measured as a single basin-wide oceanic signal, the same annual value is applied across all seven counties in a given year; this reflects the structure of the underlying phenomenon.

The five cleaned sources were left-merged onto a complete grid spanning all seven counties and all forty-six years, ensuring that every county-year appears in the final file regardless of whether a given source contributed a record for it.

## Missing Value Handling

- **NFIP claims:** treating FEMA's file as a complete administrative record, each county-year is coded with its summed claims total where a matching record exists, and zero otherwise.
- **Wildfire acreage:** the same convention applies, with each county-year coded zero when the spatial join produced no overlapping fire perimeter for that year. The prior-year wildfire variable begins in 1981, the first year for which a preceding year's acreage figure exists within the panel.
- **NFIP policy exposure:** FEMA's policy-level data covers policyEffectiveDate 2010 onward only. Within that window, a county-year with no matching policy record is coded zero, the same administrative-completeness convention as claims and wildfire. Outside that window, 1980 through 2009, active_policies and n_policy_records are coded as missing, not zero, since no source data exists for those years and the earliest available year, 2009, undercounts true exposure due to left-censoring (see Exclusions and Assumptions).
- **Climate variables (rainfall, wind, days with precipitation, extreme precipitation):** each value reflects an actual reading from the assigned station for that county-year. An earlier version of this panel filled gap years through linear interpolation; that approach was replaced with this reading-only standard, so every reported value traces to a real observation. Coverage by county and variable is detailed under Data Quality Limitations below.

## Exclusions and Assumptions

- **2026 was excluded** from the panel despite the availability of partial-year ENSO data, since the wildfire and rainfall sources extend only through 2025 and a partially populated final year would compromise usability.
- **2009 was excluded from the policy exposure variables** even though it is the first year FEMA's policy data technically covers, because a policy effective in late 2008 and still active into early 2009 is not captured, understating that year's true count relative to 2010 onward. The usable exposure window is therefore 2010 through 2025.
- **Policy exposure is used two ways:** active_policies and n_policy_records are merged into the full 1980-2025 panel for completeness and for any analysis that does not require them, and a separate specification restricted to 2010-2025 is planned as a robustness check for results that do.
- **The station assignments for Orange and San Bernardino Counties were updated** after review identified stronger alternatives. Orange County's rainfall is now sourced from a Santa Ana fire station, and San Bernardino's from three stations applied in a documented priority order.
- **Each county is represented by a single primary weather station,** selected near its population center. This simplification carries the most weight for large or geographically varied counties such as San Bernardino and Riverside, where station-level rainfall may diverge from countywide conditions.
- **Claims figures are reported in nominal dollars.** Comparisons of claims_total_paid across distant years should account for inflation separately.
- **A single ENSO value applies to all counties within a given year,** consistent with its basin-wide measurement (see Sources section above).

## Data Quality Limitations

- **Policy exposure has the largest coverage gap in the panel:** active_policies and n_policy_records carry real values for only 16 of the panel's 46 years, 2010 through 2025, because FEMA's policy-level data does not extend further back in either its current or prior published version. Any specification using these variables on the full 1980-2025 panel loses roughly two-thirds of its observations to missingness; the restricted 2010-2025 specification planned as a robustness check avoids that loss but trades away most of the panel's time span and its earlier, lower-participation years.
- **San Bernardino County** has the narrowest climate coverage in the panel: rainfall, precipitation-day, and extreme-precipitation values are recorded for twenty-six of the panel's forty-six years, drawn from the three-station priority list described in Sources above. The uncovered years form a continuous thirteen-year span from 2003 to 2015, plus 2024 and 2025. Wind-speed values follow the pattern described below for Orange and San Bernardino.
- **Ventura County** has rainfall and related values recorded for thirty-nine of the panel's forty-six years; 1980 and 1991 through 1996 fall outside what its assigned stations cover.
- **Wind speed (AWND) is reported only by automated airport-class stations,** and Orange and San Bernardino Counties are each assigned a non-airport station, so their AWND values are unavailable for every year of the panel.
- **Values for the climate variables above reflect only years with an actual station reading** (see Missing Value Handling). Analyses using these variables should apply listwise deletion, model the missingness explicitly, or restrict the sample to counties and years with complete coverage, reporting the resulting change in sample size.
- **Nominal claims figures** (see Assumptions above) can inflate apparent growth in claims_total_paid toward the later years of the panel.
- **The wildfire-acreage apportionment method assumes uniform burn severity** and uniform soil impact across a fire's mapped extent, a simplification of actual post-fire hydrological conditions.
