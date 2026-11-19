# Water and Flood: Southern California NFIP Climate Panel

ECON 490 (CSULB) group project: **"Preparing for Climate-related Disruptions in Southern California: Evidence from National Flood Insurance Payouts."**

Team: Mike Aquino, Shery Awad, Jordan Fuller, Nathan Perish.

## Research question
Do annual rainfall, ENSO (El Niño/La Niña) intensity, and prior-year wildfire acreage predict National Flood Insurance Program (NFIP) claims within a county?

## Data
County-year panel: 7 Southern California counties (Los Angeles, Orange, Riverside, San Bernardino, San Diego, Santa Barbara, Ventura), 1980–2025, 322 observations.

| Source | Variables |
| --- | --- |
| FEMA NFIP claims (NfipClaimsV3) | claims paid, claim counts |
| FEMA NFIP policies (NfipPolicies v3) | active policies (2010–2025 only) |
| NOAA GSOY station data | rainfall, precipitation days, extreme precipitation, wind |
| NOAA Oceanic Niño Index | ENSO peak/min/absolute anomalies |
| CAL FIRE / FRAP perimeters | wildfire acreage and its one-year lag |

Full variable definitions: [`docs/Data_Log.md`](docs/Data_Log.md). Cleaning methods, assumptions, and limitations: [`docs/Cleaning_Memo.md`](docs/Cleaning_Memo.md).

## Method
Poisson pseudo-maximum likelihood (PPML), chosen because claims contain many zeros, are heavily skewed, and are heteroskedastic. Claims are adjusted to 2025 dollars with CPI-U (FRED). ENSO is a single regional value per year, so it can't be separated from year fixed effects.

## Key limitations
- Policy exposure exists only for 2010–2025.
- Weather-station gaps (notably San Bernardino 2003–2015 and 2024–2025, Ventura 1980 and 1991–1996; no wind data for Orange or San Bernardino).
- One station per county; only 7 clusters for standard errors.
- NFIP claims capture insured private-property losses only, not total flood damage.

## Repository contents
- `docs/` — Cleaning Memo and Data Log (Markdown and Word).
- `chat-notes/` — summaries of working sessions (figures, model setup, memo revisions, policy brief, and presentation prep).
