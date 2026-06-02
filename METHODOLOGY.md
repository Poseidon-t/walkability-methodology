# SafeStreets Walkability Methodology, v1.0

This document specifies the SafeStreets walkability score: what it is, how it is constructed, what cutoffs are used to label it, and how to interpret it. It is the canonical reference. The live implementation at [safestreets.streetsandcommons.com](https://safestreets.streetsandcommons.com) follows this spec; if the two ever drift, this document is correct and the implementation is to be reconciled.

Version 1.0 was deployed publicly on 2026-04-06 and supersedes an earlier 3-component formulation. See [CHANGELOG.md](./CHANGELOG.md) for the version history.

## 1. Scope and goal

A SafeStreets walkability score answers a single question:

> Given an address, how viable is walking as a primary mode of moving through and around this place?

"Viable" is operationalized as a weighted combination of four observable conditions that the literature and practice converge on: whether daily needs are reachable on foot, whether the walking environment is safe enough to use, whether transit extends walking's reach, and whether the walk itself is comfortable enough to make repeatedly.

The score is intentionally not a property valuation, a demographic profile, or a crime rate. It measures the built environment for walking.

## 2. Score range and display

- **Internal range:** 0 to 100 (continuous, returned by the SafeStreets API).
- **Display range:** 0 to 10 (one decimal place, computed as internal divided by 10).

Throughout this document, thresholds are stated in both forms.

## 3. The four components

| Component | Weight | Symbol |
|-----------|--------|--------|
| Daily Reach | 50% | D |
| Street Safety | 20% | S |
| Transit Reach | 15% | T |
| Walking Comfort | 15% | C |

Each component is itself computed on the 0 to 100 scale.

The composite score W is the weighted sum:

```
W = 0.50 × D + 0.20 × S + 0.15 × T + 0.15 × C
```

Component-level detail is in [COMPONENTS.md](./COMPONENTS.md).

## 4. Tier labels

The composite W is mapped to one of five tier labels using these cutoffs:

| Tier | W ≥ (internal) | Display ≥ | Plain language |
|------|---------------|-----------|----------------|
| Walkable | 80 | 8.0 | Most daily needs accessible on foot with strong infrastructure |
| Moderate | 65 | 6.5 | Walking is viable, but some car trips are still needed |
| Car-adjacent | 50 | 5.0 | Some walkable elements, but the car is the default mode |
| Car-dependent | 35 | 3.5 | A car is required for most daily needs |
| Hostile | < 35 | < 3.5 | Basic pedestrian safety infrastructure is missing |

Tier labels are presentational. They do not enter further computation. Compare tiers only across scores produced by the same methodology version.

## 5. Reality caps and bonuses

After the weighted-sum W is computed, two corrections are applied in order:

### 5.1 Reality cap

If either of the following holds, W is capped at 65 (display 6.5) regardless of components:

- Posted or modeled vehicle speeds in the immediate street network exceed 55 km/h (35 mph) on the majority of through-streets.
- Destination density in Daily Reach falls below a threshold equivalent to fewer than 2 of 7 categories being present within a 15-minute walk.

The cap exists because composite scores can otherwise reward a single dominant component (for example, a high Walking Comfort from tree canopy) in places that are demonstrably unwalkable in practice. The cap aligns the composite with conservative ground truth.

### 5.2 Elite 15-minute city bonus

If Daily Reach D ≥ 90 AND Transit Reach T ≥ 70, the composite W is increased by up to 5 points, not to exceed 100. The bonus rewards places that combine near-complete destination access with strong multi-modal transit.

In code, both corrections are applied after the weighted sum and before tier assignment.

## 6. International coverage

The methodology is global. Component definitions and weights do not change by country. Data inputs do change, because some sources (EPA, US Census, CDC, FEMA) are US-only.

For non-US addresses:

- Street Safety uses an OpenStreetMap proxy for the EPA street-design signal.
- Daily Reach uses the same OpenStreetMap amenity model as the US.
- Transit Reach uses Transitland GTFS where available, falling back to OpenStreetMap public-transport tags.
- Walking Comfort uses Sentinel-2 satellite NDVI for tree canopy, Open-Meteo CAMS for air quality, and NASADEM for slope.

The composite W produced in either case is on the same 0 to 100 scale and uses the same tier cutoffs. Scores from a US address and a non-US address are comparable subject to the caveats in [CAVEATS.md](./CAVEATS.md).

## 7. PersonaCards (derived, not composite)

Three lifestyle verdicts are computed from the same components and presented alongside the composite. They are not part of W. Each returns one of Yes / Borderline / Unlikely:

- **Can I go car-free here?** Drawn primarily from Daily Reach and Transit Reach.
- **Is it safe for kids to walk?** Drawn primarily from Street Safety and Daily Reach (school access subset).
- **Good for aging in place?** Drawn from Walking Comfort (slope, air quality), Street Safety (crossing density), and Daily Reach (healthcare access).

PersonaCards are an interpretive layer for non-expert users. They are documented for completeness; researchers citing the methodology should generally cite the composite W and its components, not the persona verdicts.

## 8. What the methodology deliberately does not include

- **Crime data.** Pedestrian safety in this framework is built-environment safety (speed, crossings, sidewalk presence, lighting), not interpersonal crime. The score does not attempt to predict assault risk or property crime.
- **Property values, rents, demographics.** These are not inputs and not outputs. They may be correlated with walkability in some markets, but correlation is left for downstream analysis.
- **Subjective sentiment.** The score does not include reviews, survey results, or self-reported impressions.
- **Indoor amenities.** Only outdoor pedestrian-relevant infrastructure is in scope.

## 9. Provenance and reproducibility

Every score returned by the SafeStreets API is computed deterministically from inputs that are themselves drawn from open data sources. Given the same address, the same upstream snapshots, and the same methodology version, the score will reproduce exactly.

In practice, OpenStreetMap is edited continuously, satellite imagery is refreshed seasonally, and US Census ACS is refreshed annually. Component-level scores may shift when underlying sources are refreshed. Methodology version is stable until incremented in [CHANGELOG.md](./CHANGELOG.md).

## 10. Citing this methodology

See [README.md](./README.md#how-to-cite) for the citation block. A DOI is minted on each tagged release via Zenodo.
