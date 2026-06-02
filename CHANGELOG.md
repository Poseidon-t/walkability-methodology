# Methodology Changelog

This changelog tracks substantive methodology changes. Bumps to the major version (1.0 → 2.0) indicate that scores produced under earlier versions are no longer directly comparable. Minor bumps (1.0 → 1.1) indicate refinements within the same conceptual framework.

The version of the live SafeStreets tool always matches the latest entry here.

---

## v1.0.1, 2026-06-02

Re-released to trigger Zenodo DOI archival. Content identical to v1.0.0.

## v1.0.0, 2026-06-02

First public, citable release of the methodology spec.

The underlying scoring engine has been live at [safestreets.streetsandcommons.com](https://safestreets.streetsandcommons.com) since **2026-04-06**, when the 4-component "15-minute city" framework replaced an earlier 3-component formulation. v1.0 of this repository documents that already-live engine; no scoring change accompanies the repository release.

**Composite formula:**

```
W = 0.50 × D + 0.20 × S + 0.15 × T + 0.15 × C
```

with reality cap (W ≤ 65 when speeds > 55 km/h or fewer than 2/7 destination categories present) and elite 15-minute city bonus (W += up to 5 when D ≥ 90 and T ≥ 70).

**Datasets bundled:** `city-pedestrian-fatalities-2022-2024.csv` (50 US cities, NHTSA FARS).

---

## Earlier (pre-repository, for historical context)

### Live engine, pre-2026-04-06

A 3-component formulation was in place before the 15-minute-city refactor:

- Network Design
- Environmental Comfort
- Accessibility (which folded what is now Daily Reach and Transit Reach together)

Scores produced before 2026-04-06 are **not directly comparable** to v1.0 scores. The backward-compatibility key aliases in the API (`safety`, `densityContext`, `environmentalComfort`, `networkDesign`) are presentational shims so existing UI code continues to render; they map to v1.0 components but do not restore the old composite.

### Transit Reach rewrite, 2026-04-11

After the 15-minute-city refactor, the `/api/transit-access` endpoint was rewritten to query Transitland by GTFS `route_type` in parallel. The previous implementation under-counted Manhattan, Paris, Tokyo, and similar high-density transit cities by returning 0 stops in cases where the aggregator response was misinterpreted. v1.0 of the methodology reflects the corrected behavior.

---

## Versioning policy

- **MAJOR**: scoring formula change, weight change, or addition/removal of a component. Existing scores are not comparable.
- **MINOR**: tightening of a subsignal definition, addition of a data source as a fallback, change to a reality cap threshold. Scores are approximately comparable; researchers should note the version.
- **PATCH**: documentation-only changes in this repository (typo fixes, link updates).
