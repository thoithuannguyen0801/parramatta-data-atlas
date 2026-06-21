# Building the Power BI dashboard (.pbix)

Power BI Desktop is Windows-only, so the `.pbix` is built on your own machine. All the
cleaned data you need is already in `../data/` — no Python required.

## 1. Load the data
1. Open **Power BI Desktop** → **Home → Get data → Text/CSV**.
2. Import these six files from the `data/` folder:
   - `sa2_scores_parramatta.csv` — the main fact table (34 SA2s)
   - `nsw_pop_growth_decomposition.csv`
   - `nsw_age_dependency.csv`
   - `nsw_migration_components.csv`
   - `nsw_atsi_population.csv`
   - `nsw_overseas_born_by_region.csv`
3. In the preview, check **Data type detection** picked up numbers as decimal/whole numbers,
   then **Load**.

## 2. Suggested page 1 — "Well-resourced score" (the hero page)
| Visual | Type | Fields |
|---|---|---|
| Map | **Map** (or ArcGIS Map) | Location = `centroid_lat` / `centroid_lon`, Size = `poi_count`, Legend/colour = `score` |
| Ranked bar | **Clustered bar chart** | Axis = `sa2_name`, Value = `score`, sorted descending |
| KPI cards | **Card** | `score` (Max), `poi_count` (Sum = 2086), count of `sa2_name` (= 34) |
| Histogram | **Column chart** | Create a `score_band` column (see DAX below), Value = count of SA2s |

**DAX for score bands** (New column on `sa2_scores_parramatta`):
```DAX
score_band =
SWITCH(TRUE(),
    'sa2_scores_parramatta'[score] >= 0.7, "0.70–0.89 High",
    'sa2_scores_parramatta'[score] >= 0.55, "0.55–0.70",
    'sa2_scores_parramatta'[score] >= 0.40, "0.40–0.55",
    'sa2_scores_parramatta'[score] >= 0.28, "0.28–0.40",
    "0.16–0.28 Low")
```

Apply a **diverging colour scale** (red → amber → teal/green) on `score` under
*Format → Data colors → Conditional formatting* so the map and bars read like the
notebook's RdYlGn choropleth.

## 3. Suggested page 2 — "NSW in motion"
- **Line chart**: `nsw_age_dependency` — Axis = `year`, Values = youth / old-age / total ratios.
- **Stacked column**: `nsw_pop_growth_decomposition` — `natural_increase` vs `total_mig` by year.
- **Waterfall or clustered column**: `nsw_migration_components` — net overseas vs net internal by year.
- **Bar**: `nsw_overseas_born_by_region` — `pct_2016` vs `pct_2021` by region.

## 4. Polish
- Title each page, add a short text box crediting the data source (ABS Region Summary + NSW POI API).
- **File → Save as** → `nsw_parramatta.pbix`.
- To share without Power BI, **File → Export → PDF**.

That's it — roughly 15 minutes once the data is loaded.
