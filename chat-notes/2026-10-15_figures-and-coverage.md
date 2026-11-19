# Figures, coverage timeline, and baseline model (Oct 15, 2026)

## Data coverage timeline (`coverage_timeline.R`)
- Built a timeline-style chart of data coverage by variable with short "Gaps: [county codes]" labels, plus a separate `county_gap_detail.csv` with year-level gap detail.
- Reads the panel from Excel (`read_excel`). County abbreviations: LA, OC, RIV, SB, SD, SBA, VEN.
- Claims and wildfire acreage look complete because missing records were coded 0, not NA.
- Coverage check found memo gaps to fix: rainfall gaps also hit Orange (1990, 2003, 2005–2006, 2009) and Riverside (1987 onward); wind-speed gaps extend beyond Orange and San Bernardino (e.g., Santa Barbara).

## Figures (`socal_figures.R`)
- **Figure 1:** NFIP claims over time by county, El Niño years shaded (peak ONI ≥ 1.5°C).
- **Figure 2:** Rainfall vs. claims scatter with OLS trend lines.
- Both in 2025 dollars using CPI-U from FRED.
- Bug fix: FRED marks missing values with ".", so read CPI as character, then convert with `suppressWarnings(as.numeric(...))`; added `stop()` checks so failures don't silently plot blank charts.

## Why PPML instead of OLS
- Many zeros in the outcome, heteroskedasticity, and heavy skew in claims.

## Baseline PPML specification (outline)
- Claims on rainfall, ONI peak anomaly, log lagged wildfire acreage, county fixed effects, and a linear time trend (not year FE, since ONI doesn't vary by county).
- Concerns: multicollinearity among the four ONI variables and among precipitation measures; unreliable SEs with only 7 clusters (consider wild cluster bootstrap).
- Caveat: NFIP claims capture only insured private-property losses, not total flood damage.

## Output
- Project doc "Figure Talking Points: SoCal NFIP Climate Panel" — coverage timeline, Figure 1, Figure 2, and a Q&A table (inflation adjustment, log scale, OLS vs. PPML, year FE, calendar vs. water-year rainfall, SE reliability).

## R tips
- After an error, use Session → Restart R before rerunning.
- Open wide/multi-panel ggplots with Zoom or save with `ggsave` rather than relying on the Plots pane.
