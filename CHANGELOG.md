# Methodology Changelog

This changelog tracks substantive methodology changes. Bumps to the major version (1.0 → 2.0) indicate that scores produced under earlier versions are no longer directly comparable. Minor bumps (1.0 → 1.1) indicate refinements within the same conceptual framework.

The version of the live SafeStreets tool always matches the latest entry here.

---

## v1.1.0, 2026-08-21

Brings the specification back in line with the engine that has been live since 2026-08-17. The repository had continued to describe the weights and tiers used before the score recalibration, so scores produced by the live tool did not match scores computed from this document.

**Composite formula:**

```
W = 0.40 × D + 0.30 × S + 0.15 × T + 0.15 × C
```

Daily Reach falls from 50% to 40% and Street Safety rises from 20% to 30%. Destination density alone no longer carries a place to the top of the scale.

**Tier labels:** six rather than five. Pedestrian-first is added above Very walkable, Car-adjacent is removed, and the remaining cutoffs move. A score of 8.0 was labelled Walkable under v1.0 and is labelled Walkable under v1.1 only above 7.0, so tier labels are not comparable across versions.

**Reality caps:** section 5.1 previously described a single ceiling of 65. The engine applies a graduated set of ceilings by network speed and destination access, plus a hostile-streets ceiling driven by Street Safety. All are now documented.

**Pedestrian-first gate replaces the elite bonus.** v1.0 raised W by up to 5 points where Daily Reach and Transit Reach were both high. That bonus rewarded destination density on its own. It is replaced by a gate: where W exceeds 90 and Street Safety is below 85, W is set to 89.

**PersonaCards:** renamed to Families, Older Adults and Car-Free Living, each carrying a score and a five-step label rather than a Yes / Borderline / Unlikely verdict.

Scores produced under v1.0 and v1.1 are not directly comparable.

---

## v1.0.2, 2026-06-02

Wired the Zenodo DOI back into the repository: README badge, citation block, CITATION.cff `identifiers:` entry, and data card citation. DOI is concept-level and resolves to the latest version.

DOI: [10.5281/zenodo.20506270](https://doi.org/10.5281/zenodo.20506270)

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
