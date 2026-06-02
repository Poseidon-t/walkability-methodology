# Data Sources

Every external data source that contributes to a SafeStreets walkability score, with its license, coverage, refresh cadence, and the component(s) it feeds.

The methodology is open. Anyone with these sources and the spec in [METHODOLOGY.md](./METHODOLOGY.md) can reproduce a score.

---

## Global sources (used everywhere)

### OpenStreetMap

- **License:** Open Database License (ODbL), © OpenStreetMap contributors
- **URL:** [openstreetmap.org](https://www.openstreetmap.org)
- **Used for:** Street network, sidewalks, crossings, traffic calming, lighting, amenities (all seven Daily Reach categories), public-transport tags (transit fallback when GTFS is missing)
- **Feeds:** Daily Reach, Street Safety, Transit Reach (fallback)
- **Refresh:** Continuous. SafeStreets queries via Overpass API in real time, with a short-lived cache that does not cache empty responses.

### Sentinel-2

- **License:** Free, full and open (Copernicus Programme, European Space Agency)
- **URL:** [sentinel.esa.int/web/sentinel/missions/sentinel-2](https://sentinel.esa.int/web/sentinel/missions/sentinel-2)
- **Used for:** Tree canopy via NDVI (Normalised Difference Vegetation Index) at 10 m ground resolution
- **Feeds:** Walking Comfort
- **Refresh:** Seasonal composites. Cloud-masked.

### NASADEM

- **License:** Public domain (NASA)
- **URL:** [lpdaac.usgs.gov/products/nasadem_hgtv001](https://lpdaac.usgs.gov/products/nasadem_hgtv001/)
- **Used for:** Terrain slope, ADA-accessibility band
- **Feeds:** Walking Comfort

### Open-Meteo CAMS

- **License:** CC BY 4.0 (Open-Meteo), CAMS data attribution to Copernicus Atmosphere Monitoring Service
- **URL:** [open-meteo.com](https://open-meteo.com)
- **Used for:** Real-time air quality (PM2.5, PM10, NO2, O3, AQI), current weather for heat-stress modifier
- **Feeds:** Walking Comfort

### OpenAQ

- **License:** CC BY 4.0
- **URL:** [openaq.org](https://openaq.org)
- **Used for:** Supplementary air-quality readings where station coverage is dense
- **Feeds:** Walking Comfort

### Transitland

- **License:** CC BY 4.0 (aggregator) over individual transit-operator licenses (CC0, ODbL, agency-specific)
- **URL:** [transit.land](https://www.transit.land)
- **Used for:** Transit stops and routes, queried in parallel by GTFS `route_type` to fix earlier under-counting in dense cities
- **Feeds:** Transit Reach

---

## US-only sources (additional layers for US addresses)

### EPA National Walkability Index

- **License:** Public domain (US Environmental Protection Agency)
- **URL:** [epa.gov/smartgrowth/national-walkability-index-user-guide](https://www.epa.gov/smartgrowth/national-walkability-index-user-guide)
- **Used for:** Tract-level street-design quality (intersection density, transit proximity, land-use mix)
- **Feeds:** Street Safety (US street-design subsignal)
- **Refresh:** EPA publishes major revisions; check version in use against the EPA portal.

### US Census ACS (American Community Survey)

- **License:** Public domain (US Census Bureau)
- **URL:** [census.gov/programs-surveys/acs](https://www.census.gov/programs-surveys/acs)
- **Used for:** Commute modes by tract, demographics by tract (for Neighborhood Intelligence layer, not for the composite)
- **Feeds:** Neighborhood Intelligence (presented alongside the score, not part of W)

### CDC PLACES

- **License:** Public domain (US Centers for Disease Control and Prevention)
- **URL:** [cdc.gov/places](https://www.cdc.gov/places/)
- **Used for:** Health outcomes by census tract (obesity, diabetes, asthma rates), shown in Neighborhood Intelligence layer
- **Feeds:** Neighborhood Intelligence (presented alongside the score, not part of W)

### FEMA NFHL (National Flood Hazard Layer)

- **License:** Public domain (US Federal Emergency Management Agency)
- **URL:** [fema.gov/flood-maps/national-flood-hazard-layer](https://www.fema.gov/flood-maps/national-flood-hazard-layer)
- **Used for:** Flood zone classification, shown in Neighborhood Intelligence layer
- **Feeds:** Neighborhood Intelligence (presented alongside the score, not part of W)

### NHTSA FARS (Fatality Analysis Reporting System)

- **License:** Public domain (US National Highway Traffic Safety Administration)
- **URL:** [nhtsa.gov/research-data/fatality-analysis-reporting-system-fars](https://www.nhtsa.gov/research-data/fatality-analysis-reporting-system-fars)
- **Used for:** Source of the `data/city-pedestrian-fatalities-2022-2024.csv` open dataset bundled with this repository
- **Feeds:** Bundled dataset; not part of the live score

### NCES EDGE

- **License:** Public domain (US National Center for Education Statistics)
- **URL:** [nces.ed.gov/programs/edge](https://nces.ed.gov/programs/edge/)
- **Used for:** US public K-12 school geocodes for the School Route Safety feature
- **Feeds:** Premium School Route Safety feature in the live tool; not part of the composite walkability score W

---

## Address geocoding

Geocoding is required to turn a user-provided address into a latitude and longitude. It is upstream of all components.

- **Primary:** Google Places (new) where API quota is available
- **Fallback:** Nominatim ([nominatim.openstreetmap.org](https://nominatim.openstreetmap.org)), which is OpenStreetMap-based and therefore ODbL

Geocoding is not part of the score. Different geocoders may resolve the same address to slightly different centroids, which can shift the score within the noise floor of the underlying inputs.

---

## Reproducibility note

All sources above are open. The composite score W is deterministic given (address, methodology version, source snapshot dates). Researchers reproducing scores should record source snapshot dates alongside results.
