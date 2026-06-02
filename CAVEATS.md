# Caveats and Validity

What this methodology is honest about not capturing well. Cite this section when reporting limits of any analysis built on SafeStreets scores.

## 1. OpenStreetMap completeness varies

Every component except Walking Comfort and the US-only neighborhood-intelligence layer depends on OpenStreetMap. OSM coverage is unevenly complete:

- **Strong:** Western Europe, North America (urban), Japan, parts of Latin America with active mapping communities.
- **Weaker:** Many cities in the Global South for sidewalk, crossing, and traffic-calming tags specifically. Streets may exist in OSM without their pedestrian infrastructure being tagged.

A neighborhood with under-tagged pedestrian infrastructure will score lower than its ground-truth walkability. The score should be read as a lower bound in such cases.

## 2. Sidewalk presence is not sidewalk usability

OSM tags whether a sidewalk exists. It does not tell us:

- Whether the sidewalk is in good repair.
- Whether it is blocked by parked vehicles, vendors, debris, or missing sections.
- Whether enforcement of sidewalk-protection rules is consistent.

Scores in cities with high sidewalk-encroachment rates may overstate walkability where sidewalks exist on paper but are frequently obstructed in practice. SafeStreets's "Note on ground reality" appears in the UI for this reason.

## 3. Speeds are often inferred, not posted

OSM has a `maxspeed` tag, but many ways in many countries do not carry it. Where it is missing, the methodology infers a speed from the road class (`highway=motorway` → 100+, `highway=residential` → 30). Inference is conservative, but it can miss locally posted limits.

## 4. Tree canopy via NDVI is a proxy

Sentinel-2 NDVI captures vegetation greenness at 10 m resolution. It conflates:

- Tree canopy along streets (what we want for shade).
- Lawns, agricultural land, parks, and other non-shading vegetation.

The methodology adjusts NDVI within walking-radius buffers around the road network, but in heavily landscaped suburbs the comfort score may be biased upward.

## 5. The score is built-environment safety, not crime

Street Safety measures infrastructure: crossings, speeds, sidewalks, lighting, traffic calming. It does not measure assault, theft, or other interpersonal risks. Treating the SafeStreets score as a "safe to walk at night" verdict is incorrect. Pair it with local crime data if that is the question.

## 6. Address centroid sensitivity

The score is computed at a single (lat, lon). For large parcels (corporate campuses, university blocks, irregular plots), the geocoded centroid may not represent the building entrance. For most residential addresses, the difference is within the noise floor.

## 7. Comparability across methodology versions

W computed under v1.0 is not directly comparable with W computed under any earlier 3-component formulation. Always cite the methodology version when comparing scores over time. See [CHANGELOG.md](./CHANGELOG.md).

## 8. Data refresh lags

- **OpenStreetMap:** continuous, real-time at query time.
- **Sentinel-2:** seasonal composites; tree canopy is not a same-day signal.
- **EPA NWI, US Census ACS, CDC PLACES, FEMA NFHL:** annual or less.
- **Transitland GTFS:** depends on each transit agency's publishing cadence.

Scores will shift when underlying sources are refreshed even if the address has not changed. Researchers should record snapshot dates.

## 9. Crash and fatality data refresh lag

The pedestrian fatality dataset bundled in this repo is NHTSA FARS for the calendar years 2022 to 2024. NHTSA FARS final-file releases lag the calendar year by 12 to 18 months. Newer crash data, where freshness matters more than nationwide consistency, is available from state-level open portals (Chicago, PennDOT, NYC, TxDOT CRIS, California TIMS).

## 10. The score is not a property valuation

Walkability and property value are correlated in many markets, but the SafeStreets score is not a real-estate signal and should not be used as one. Downstream analysis that needs valuations should combine the score with appropriate housing data.

## 11. PersonaCards are interpretive

The three PersonaCard verdicts (Yes / Borderline / Unlikely for car-free, safe for kids, aging in place) are derived from components for accessibility to non-expert users. They are not separately validated against survey data and should not be treated as research-grade outputs. For research, cite the composite W and its components directly.
