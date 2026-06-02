# Dataset card: city-pedestrian-fatalities-2022-2024.csv

A 50-city snapshot of pedestrian fatality patterns in the United States, derived from NHTSA's Fatality Analysis Reporting System (FARS) for calendar years 2022 to 2024.

This is one of the bundled open datasets that accompany the [SafeStreets walkability methodology](../README.md). It is not used to compute walkability scores; it is published alongside the methodology because the same pipeline that built the live tool produced it, and because it answers a question journalists, planners, and researchers reach for repeatedly: *where are pedestrians being killed in this city, and what road class is doing the killing?*

## Quick facts

| | |
|---|---|
| **Rows** | 50 (one row per city) |
| **Time range** | Crash years 2022, 2023, 2024 (NHTSA FARS final files) |
| **Geographic scope** | United States |
| **Source** | [NHTSA FARS](https://www.nhtsa.gov/research-data/fatality-analysis-reporting-system-fars), public-domain dataset |
| **Filter** | `PER_TYP = 5` (pedestrian), `INJ_SEV in {4, 6}` (fatal injury, with the inclusion of cases later coded 6 for died prior to crash). |
| **Spatial assignment** | A fatality is assigned to a city if the crash location is within a 20 km radius of the city centroid. Cities with overlapping circles will share some fatalities; the dataset does not double-count within a row. |
| **License** | CC BY 4.0 for this derived CSV. Underlying NHTSA FARS is public domain. |
| **Format** | UTF-8 CSV, comma delimited, with header row. |

## Columns

| Column | Type | Description |
|--------|------|-------------|
| `city` | string | Human-readable city name |
| `state` | string | US state name (full) |
| `slug` | string | URL-safe city identifier used by SafeStreets internally |
| `total_ped_deaths_2022_2024` | integer | Pedestrian fatalities within 20 km of city centroid, summed across 2022, 2023, 2024 |
| `pct_on_surface_arterials` | integer | Percentage of those fatalities that occurred on surface arterial roads (not freeways, not minor residential streets) |
| `pct_after_dark_6pm_6am` | integer | Percentage that occurred between 6 pm and 6 am local time |
| `median_victim_age` | integer | Median age of fatally-injured pedestrians |
| `pct_age_65_plus` | integer | Percentage of fatally-injured pedestrians aged 65 or older |
| `top_corridor_1_road` … `top_corridor_5_road` | string | The five named roads with the most pedestrian fatalities in the city, in descending order |
| `top_corridor_1_deaths` … `top_corridor_5_deaths` | integer | Count of fatalities on the corresponding road |

Missing corridor rows are blank (some smaller cities have fewer than 5 named roads meeting the threshold).

## Known limitations

1. **NHTSA FARS lag.** FARS final files lag the calendar year by 12 to 18 months. The dataset reflects crashes through 2024 and was generated 2026-05-29; older years are stable, the most recent year may be revised in future FARS releases.
2. **20 km radius assignment.** Cities with overlapping radii share fatalities. The dataset shows where pedestrians die *near* each city centroid, not within strict municipal boundaries. For municipal-boundary analysis, re-spatially-join FARS to the relevant city-limit polygon.
3. **Road-class share is reported, not predictive.** `pct_on_surface_arterials` describes where deaths actually occurred. It does not, on its own, prove arterials cause the deaths; speed environment, lighting, and crossing density are the underlying mechanism. See SafeStreets's `/insights/street-design-and-survival` analysis for the speed-survival relationship.
4. **Top corridors are road names.** Some "top corridors" reflect long roads that traverse the whole city. Don't infer concentrated risk solely from a road appearing in the top 5; combine with mileage and AADT data for a corridor-level rate.
5. **Not adjusted for population.** This dataset reports counts, not rates. To convert to a fatality rate, divide by city population and the years covered (3); a separate file with rates is on the roadmap.

## Suggested citation

> Streets & Commons (2026). *City Pedestrian Fatalities 2022 to 2024 (US, 50 cities)*. In SafeStreets Walkability Methodology, v1.0. https://github.com/streetsandcommons/walkability-methodology/blob/main/data/city-pedestrian-fatalities-2022-2024.csv

When citing in journalism, please also link the underlying NHTSA FARS source so readers can verify.

## Reproducing

The pipeline that produces this CSV is:

1. Download NHTSA FARS `PERSON.csv` and `ACCIDENT.csv` for 2022, 2023, 2024.
2. Filter `PER_TYP = 5` (pedestrian) and `INJ_SEV in {4, 6}` (fatal).
3. Spatial-join the resulting crash points to city centroids within 20 km.
4. Aggregate by city: total deaths, share on surface arterials (`ROUTE in {3, 4}`), share after dark, median victim age, share aged 65+.
5. Group fatalities by road name (`TWAY_ID1`), take top 5.

Streets & Commons publishes the source code for steps 1 to 5 in the live tool's repository. Anyone with the FARS source files and a GIS environment can reproduce the table.
