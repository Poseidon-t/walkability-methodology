# Components

Detailed specification of the four walkability components used in v1.0 of the SafeStreets methodology. Each component is scored on the 0 to 100 scale and combines into the composite via the weights given in [METHODOLOGY.md](./METHODOLOGY.md).

---

## D, Daily Reach (weight: 50%)

**What it measures:** Walking access to seven essential service categories within a 15-minute walking window from the address.

The seven categories are:

1. **Grocery** (supermarkets, grocery stores, food markets)
2. **Healthcare** (clinics, pharmacies, hospitals, dental)
3. **Education** (schools, kindergartens, libraries)
4. **Recreation** (parks, playgrounds, sports facilities, plazas)
5. **Dining** (restaurants, cafes, fast food)
6. **Shopping** (general retail, convenience, household)
7. **Civic** (government services, post offices, places of worship, community centres)

**How a category is scored:**

For each category C, the algorithm enumerates qualifying OpenStreetMap nodes and ways within a 1.2 km walking distance band (≈ 15 minutes at 80 m/min) of the address centroid. The category subscore C_i (0 to 100) is a saturation function of the count, asymptoting at the count typical of a strong inner-urban neighbourhood for that category. Saturation is per-category because reasonable counts differ (one good grocer matters more than counting 30 restaurants).

**How the component aggregates:**

D is the simple average of the seven category subscores. A category with zero qualifying destinations contributes 0; a category at saturation contributes 100.

A coverage signal (number of categories with non-zero count) is exposed alongside D for downstream interpretation but does not enter the composite. The reality cap in [METHODOLOGY.md §5.1](./METHODOLOGY.md#51-reality-cap) uses this coverage signal.

---

## S, Street Safety (weight: 20%)

**What it measures:** The built-environment safety of walking trips originating at the address. This is infrastructure safety, not crime.

**Subsignals:**

| Subsignal | Source | Range |
|-----------|--------|-------|
| Crossing density | OSM `highway=crossing`, `crossing=*` | crossings per km of road |
| Speed exposure | OSM `maxspeed`, road class | inferred where untagged |
| Sidewalk coverage | OSM `sidewalk=*` and `footway=sidewalk` | % of streets with sidewalk attribute |
| Traffic calming | OSM `traffic_calming=*` | count per km |
| Lighting coverage | OSM `lit=*` | % of streets with lit attribute |
| US street-design quality | EPA National Walkability Index | tract-level, US only |

For US addresses, EPA's National Walkability Index contributes a tract-level signal that captures intersection density, transit proximity, and land-use mix. For non-US addresses, this signal is replaced with an OSM-based proxy computed from the same OSM inputs (intersection density, dead-end ratio, betweenness centrality on the street graph).

**How the component aggregates:**

S is a weighted combination of the subsignals with weights tuned so that the dominant pedestrian-fatality predictor in the underlying literature, speed exposure, has the largest influence. Sidewalk coverage and crossing density are the next-largest.

S is bounded below by a floor that activates when sidewalk coverage is below 20% or modelled speeds exceed 60 km/h on through-streets, which prevents high tree-canopy or transit signals from masking a hostile pedestrian environment.

---

## T, Transit Reach (weight: 15%)

**What it measures:** How much transit extends the walking radius from the address.

**Subsignals:**

| Subsignal | Source |
|-----------|--------|
| Transit stop density | Transitland GTFS aggregator, OSM fallback |
| Route diversity | Transitland GTFS by `route_type` (queried in parallel) |
| Multi-modal bonus | Transitland: presence of two or more `route_type` classes |

**How it aggregates:**

T is computed from the count and class of transit stops within an 800 m walking radius. Multiple stops on the same route count once. A multi-modal bonus is applied when at least two of {bus, rail, tram/light-rail, ferry, subway} are represented within range.

T is rendered in the UI even when it equals 0; users moving from an address with strong T to one without should see the absence.

---

## C, Walking Comfort (weight: 15%)

**What it measures:** Whether the act of walking itself is comfortable enough to do repeatedly.

**Subsignals:**

| Subsignal | Source |
|-----------|--------|
| Tree canopy (NDVI proxy) | Sentinel-2 (ESA, 10 m resolution) |
| Slope | NASADEM elevation, ADA-accessibility band |
| Air quality (PM2.5, AQI) | Open-Meteo CAMS, OpenAQ |
| Heat stress modifier | Open-Meteo current-weather, applied to tree canopy |

**How it aggregates:**

C is a weighted combination with tree canopy as the largest single contributor. The heat-stress modifier reduces tree canopy's effective contribution where current weather conditions make shade less protective (cold climates) or critically required (heat waves). Air quality enters as a bounded multiplier: pristine air does not boost the score above its ceiling, but poor air can suppress it.

Slope contributes inversely: streets with sustained gradients above the ADA accessibility threshold lose comfort points proportional to the affected portion of the walking radius.

---

## Backward-compatibility aliases

The SafeStreets API continues to return the legacy 3-component keys for clients written before v1.0. They are aliases, not separate scores:

| Legacy key | Maps to |
|-----------|---------|
| `safety` | S |
| `densityContext` | D |
| `environmentalComfort` | C |
| `networkDesign` | S (US street-design subsignal) |

New consumers should use the v1.0 keys: `dailyReach`, `streetSafety`, `transitReach`, `walkingComfort`.
