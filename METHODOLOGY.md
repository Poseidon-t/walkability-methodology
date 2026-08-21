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
| Daily Reach | 40% | D |
| Street Safety | 30% | S |
| Transit Reach | 15% | T |
| Walking Comfort | 15% | C |

Each component is itself computed on the 0 to 100 scale.

The composite score W is the weighted sum:

```
W = 0.40 × D + 0.30 × S + 0.15 × T + 0.15 × C
```

Component-level detail is in [COMPONENTS.md](./COMPONENTS.md).

## 4. Tier labels

The composite W is mapped to one of six tier labels using these cutoffs:

| Tier | W ≥ (internal) | Display ≥ | Plain language |
|------|---------------|-----------|----------------|
| Pedestrian-first | 93 | 9.3 | Walking is the default mode and the street design supports it |
| Very walkable | 83 | 8.3 | Nearly all daily needs on foot, with safe crossings |
| Walkable | 70 | 7.0 | Most daily needs accessible on foot |
| Moderate | 55 | 5.5 | Walking is viable, but some car trips are still needed |
| Car-dependent | 35 | 3.5 | A car is required for most daily needs |
| Hostile | < 35 | < 3.5 | Basic pedestrian safety infrastructure is missing |

Tier labels are presentational. They do not enter further computation. Compare tiers only across scores produced by the same methodology version.

## 5. Reality caps and bonuses

After the weighted-sum W is computed, two corrections are applied in order:

### 5.1 Reality caps

A composite score can otherwise reward a single dominant component, for example a high Walking Comfort drawn from tree canopy, in a place that is unwalkable in practice. Several ceilings apply after the weighted sum. Each is a ceiling rather than a subtraction, so the lowest applicable one governs.

Speed of the surrounding network, where a place with substantial pedestrian infrastructure receives the higher ceiling:

| Mean through-street speed | Ceiling with strong pedestrian infrastructure | Ceiling without |
|---------------------------|-----------------------------------------------|-----------------|
| 60 km/h and above | 50 | 30 |
| 50 to 59 km/h | 70 | 45 |

Destination access:

| Service categories reachable within 15 minutes | Ceiling |
|------------------------------------------------|---------|
| 2 or fewer of 7 | 30 |
| 3 of 7 | 45 |

Two further ceilings apply. Where Transit Reach is below 10 and Daily Reach is below 30, W is capped at 35. Where the streets themselves are hostile, W is capped at 40 for a Street Safety score below 25, and at 47 for a score below 40. The hostile-streets ceiling is suspended where destination access is near-complete, where calming data is absent, or where the location is both dense and transit-rich.

### 5.2 Pedestrian-first gate

The top band is reserved for places whose street design supports walking, rather than for places that merely hold many destinations. Where W exceeds 90 and Street Safety is below 85, W is set to 89.

This gate replaces the elite 15-minute city bonus used in v1.0, which raised W by up to 5 points for high destination access combined with strong transit. The bonus rewarded destination density alone, which is the behaviour the gate now prevents.

Corrections are applied after the weighted sum and before tier assignment.

## 6. International coverage

The methodology is global. Component definitions and weights do not change by country. Data inputs do change, because some sources (EPA, US Census, CDC, FEMA) are US-only.

For non-US addresses:

- Street Safety uses an OpenStreetMap proxy for the EPA street-design signal.
- Daily Reach uses the same OpenStreetMap amenity model as the US.
- Transit Reach uses Transitland GTFS where available, falling back to OpenStreetMap public-transport tags.
- Walking Comfort uses Sentinel-2 satellite NDVI for tree canopy, Open-Meteo CAMS for air quality, and NASADEM for slope.

The composite W produced in either case is on the same 0 to 100 scale and uses the same tier cutoffs. Scores from a US address and a non-US address are comparable subject to the caveats in [CAVEATS.md](./CAVEATS.md).

## 7. PersonaCards (derived, not composite)

Three lifestyle verdicts are computed from the same components and presented alongside the composite. They are not part of W. Each carries its own 0 to 100 score and a label drawn from that score:

| Persona | Reads on | Weighting |
|---------|----------|-----------|
| Families | Safe streets, parks, school runs | 0.70 Street Safety, 0.30 Daily Reach |
| Older Adults | Accessible services, flat terrain, safety | 0.40 Walking Comfort, 0.35 Street Safety, 0.25 Daily Reach |
| Car-Free Living | No vehicle, walking and transit only | Daily Reach and Transit Reach |

Labels: Excellent fit at 75 and above, Works well at 60, Some trade-offs at 45, Limited suitability at 30, Not recommended below 30.

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
