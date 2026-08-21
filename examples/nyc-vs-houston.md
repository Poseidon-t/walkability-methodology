# Worked example: New York vs Houston

A side-by-side computation showing how the same composite formula produces very different walkability scores for two well-known US cities. Both addresses are residential. Both use the v1.1 methodology and identical source snapshot dates.

The point of the worked example is to make the math visible: the composite W is mechanical once the components are computed, and the components are themselves anchored in observable conditions.

## Addresses

| | New York | Houston |
|---|---------|---------|
| Address | Park Slope, Brooklyn, NY | Energy Corridor, Houston, TX |
| Coordinates | 40.6710, -73.9814 | 29.7733, -95.6324 |
| Country | US | US |

## Component scores

| Component | Weight | NY | Houston |
|-----------|--------|-----|---------|
| D, Daily Reach | 0.40 | 94 | 26 |
| S, Street Safety | 0.30 | 78 | 38 |
| T, Transit Reach | 0.15 | 89 | 22 |
| C, Walking Comfort | 0.15 | 72 | 54 |

## Composite calculation

**New York:**

```
W = 0.40 × 94 + 0.30 × 78 + 0.15 × 89 + 0.15 × 72
  = 37.6 + 23.4 + 13.35 + 10.8
  = 85.15
```

Round to integer: **W = 85** (display: **8.5**, tier: **Very walkable**).

Pedestrian-first gate check: W is 85 and the gate applies only above 90, so it does not bind. Had the composite cleared 90, a Street Safety score of 78 would have held it at 89. No reality cap binds either, since speeds and destination coverage sit within their thresholds.

**Houston:**

```
W = 0.40 × 26 + 0.30 × 38 + 0.15 × 22 + 0.15 × 54
  = 10.4 + 11.4 + 3.3 + 8.1
  = 33.2
```

Reality cap check: only 1 of 7 destination categories is present within a 15-minute walk, a single park. Two or fewer categories imposes a ceiling of 30, which binds here because the weighted sum of 33.2 sits above it. Speeds on through-streets in the Energy Corridor also exceed 55 km/h, which imposes a ceiling of its own. Neither speed ceiling falls below 30, so the destination ceiling governs.

Final: **W = 30** (display: **3.0**, tier: **Hostile**).

## What the numbers show

The 55-point gap between New York and Houston is concentrated in the two largest-weighted components, Daily Reach (94 vs 26) and Street Safety (78 vs 38). Walking Comfort, which is similar in both (72 vs 54), is the smallest contributor to the gap because its weight is smallest and because tree canopy and slope happen to be reasonable in both addresses.

This is the methodology working as intended: a place where daily needs are not reachable on foot and the street network is designed for speed cannot earn a high composite, even with abundant trees and reasonable air quality.

## Reproducing this

Anyone with the same source snapshots, the spec in [METHODOLOGY.md](../METHODOLOGY.md), and the component definitions in [COMPONENTS.md](../COMPONENTS.md) can reproduce these numbers. The live tool at [safestreets.streetsandcommons.com](https://safestreets.streetsandcommons.com) will return values within 1 to 2 points of these, depending on the OSM snapshot at query time.
