# NSW & Sydney–Parramatta — Data Atlas

A spatial-demographic analysis of New South Wales and a "well-resourced" scoring model for
every SA2 region in the **Sydney–Parramatta** SA4 zone, built with the Python data-science
stack and PostGIS.

> **About this repository.** This is **my individual work** from a University of Sydney
> data-science project (DATA2001): the NSW Population & Migration analysis and the complete
> Sydney–Parramatta resource-scoring pipeline that I built end to end.

## Live outputs

| Output | File |
|--------|------|
| Interactive dashboard | [`dashboard/index.html`](dashboard/index.html) — NSW demographic charts + clickable Leaflet map of the 34 scored SA2 regions |
| Written report | [`report/index.html`](report/index.html) |

Both are self-contained — open in any browser, or host free with GitHub Pages.

## What's inside

```
notebooks/
  01_task1_nsw_population_migration.ipynb   # Task 1 — load, clean, 5 derived statistics
  02_tasks234_parramatta_scoring.ipynb      # Tasks 2–4 — POI API → PostGIS → score → maps
data/                                        # cleaned CSV outputs
dashboard/index.html                         # interactive HTML dashboard (self-contained)
report/index.html                            # written analytical report
figures/                                     # exported score distribution + choropleth
```

## Methods

**Task 1 — NSW Region Summary.** Loaded the ABS NSW Region Summary with Pandas, cleaned it
(normalised column names, dropped sparse year columns, coerced numerics, extracted units),
and derived five statistics: population-growth decomposition, age-dependency ratios, the
COVID migration shock, Aboriginal & Torres Strait Islander population growth, and the shifting
regions of birth of NSW's overseas-born population.

**Tasks 2–4 — Sydney–Parramatta resource score.** Built a `nearbyPOI()` function against the
NSW Points of Interest API, looped over the 34 SA2 polygons (ABS boundaries via GeoPandas),
filtered POIs to inside-polygon with Shapely, and ingested **2,086 POIs** into a **PostGIS**
table. Each SA2's resource score is a sigmoid of its standardised POI count:

```
z_POI = (poi_count − μ) / σ
Score = 1 / (1 + e^(−z_POI))
```

Scores range **0.16–0.89** across 34 regions.

## Key findings

- By 2023, **net migration drove ~82%** of NSW population growth as natural increase faded.
- NSW is ageing: old-age dependency rose +2.24 pts (2019→2024) while youth dependency fell.
- The Aboriginal & Torres Strait Islander population grew **+61%** (2011–2021), ~3.3× the
  NSW rate.
- Highest-scoring SA2: **North Parramatta (0.89)**; lowest are non-residential land
  (Yennora/Smithfield industrial estates, Rookwood Cemetery) — the model's main limitation,
  since a raw POI count conflates "few amenities" with "not a residential area".

## Tech stack

Python · Pandas · GeoPandas · Shapely · PostgreSQL/PostGIS · Matplotlib · Leaflet · Chart.js

## Reproducing

The notebooks expect the CSVs in `data/`. Task 2's PostGIS cells require a local PostgreSQL
+ PostGIS instance and live NSW POI API access; the scored outputs are cached in
`data/sa2_scores_parramatta.csv` so the analysis and dashboard run without re-querying the API.

## Data sources

- Australian Bureau of Statistics — NSW Region Summary (Data by Region)
- NSW Points of Interest API & NSW Topographic Data Dictionary
