# Worked example: New York vs Houston

A side-by-side computation showing how the same composite formula produces very different walkability scores for two well-known US cities. Both addresses are residential. Both use the v1.0 methodology and identical source snapshot dates.

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
| D, Daily Reach | 0.50 | 94 | 26 |
| S, Street Safety | 0.20 | 78 | 38 |
| T, Transit Reach | 0.15 | 89 | 22 |
| C, Walking Comfort | 0.15 | 72 | 54 |

## Composite calculation

**New York:**

```
W = 0.50 × 94 + 0.20 × 78 + 0.15 × 89 + 0.15 × 72
  = 47.0 + 15.6 + 13.35 + 10.8
  = 86.75
```

Round to integer: **W = 87** (display: **8.7**, tier: **Walkable**).

Elite 15-minute city bonus check: D ≥ 90 (yes, 94) and T ≥ 70 (yes, 89). Bonus applies, capped to push W from 84 to 87. The reality cap does not apply (speeds and destination coverage are within thresholds).

**Houston:**

```
W = 0.50 × 26 + 0.20 × 38 + 0.15 × 22 + 0.15 × 54
  = 13.0 + 7.6 + 3.3 + 8.1
  = 32.0
```

Round: **W = 32** (display: **3.2**, tier: **Hostile**).

Reality cap check: speeds on through-streets in the Energy Corridor exceed 55 km/h on the majority of through-streets, and only 1 of 7 destination categories is present within a 15-minute walk (recreation, in the form of a single park). Both conditions would trigger the W ≤ 65 cap, but the unmodified W of 32 is already below the cap, so the cap is non-binding.

## What the numbers show

The 55-point gap between New York and Houston is concentrated in the two largest-weighted components, Daily Reach (94 vs 26) and Street Safety (78 vs 38). Walking Comfort, which is similar in both (72 vs 54), is the smallest contributor to the gap because its weight is smallest and because tree canopy and slope happen to be reasonable in both addresses.

This is the methodology working as intended: a place where daily needs are not reachable on foot and the street network is designed for speed cannot earn a high composite, even with abundant trees and reasonable air quality.

## Reproducing this

Anyone with the same source snapshots, the spec in [METHODOLOGY.md](../METHODOLOGY.md), and the component definitions in [COMPONENTS.md](../COMPONENTS.md) can reproduce these numbers. The live tool at [safestreets.streetsandcommons.com](https://safestreets.streetsandcommons.com) will return values within 1 to 2 points of these, depending on the OSM snapshot at query time.
