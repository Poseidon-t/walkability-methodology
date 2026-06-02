# Walkability Methodology

Open, citable methodology and supporting datasets behind the [SafeStreets](https://safestreets.streetsandcommons.com) walkability score, by [Streets & Commons](https://streetsandcommons.com).

This repository is the canonical reference for how a SafeStreets walkability score is constructed, what data sources back it, and what its known limitations are. It exists so that researchers, journalists, policy makers, and software built on top of SafeStreets can cite a specific, versioned methodology rather than a moving web page.

## What this is

- **A 4-component walkability scoring framework** rooted in the 15-minute city model (Daily Reach, Street Safety, Transit Reach, Walking Comfort).
- **A standing inventory of open data sources** used to compute it worldwide.
- **Open datasets** derived from the same pipeline, including pedestrian fatality patterns for 50 US cities (2022 to 2024).
- **Worked examples** showing how the score is computed for real addresses.

The live tool that implements this methodology is at [safestreets.streetsandcommons.com](https://safestreets.streetsandcommons.com). It is free and requires no sign-up for the core score.

## How to cite

If you use this methodology or the datasets in research, journalism, or downstream tooling, please cite:

> Streets & Commons (2026). *SafeStreets Walkability Methodology, v1.0*. https://github.com/Poseidon-t/walkability-methodology

A machine-readable [`CITATION.cff`](./CITATION.cff) is included so GitHub renders a "Cite this repository" button. A DOI will be minted via Zenodo on the first tagged release.

## Repository contents

| File | What it covers |
|------|---------------|
| [METHODOLOGY.md](./METHODOLOGY.md) | Full scoring spec: components, weights, tiers, score transformations |
| [COMPONENTS.md](./COMPONENTS.md) | Per-component detail (Daily Reach, Street Safety, Transit Reach, Walking Comfort) |
| [DATA_SOURCES.md](./DATA_SOURCES.md) | Every input data source, license, coverage, refresh cadence |
| [CAVEATS.md](./CAVEATS.md) | Known limitations, validity range, what the score does and does not capture |
| [CHANGELOG.md](./CHANGELOG.md) | Versioned methodology changes (current: v1.0) |
| [examples/brooklyn-sample.json](./examples/brooklyn-sample.json) | Sample API response for a Brooklyn address |
| [examples/nyc-vs-houston.md](./examples/nyc-vs-houston.md) | Worked comparison of two scores |
| [data/city-pedestrian-fatalities-2022-2024.csv](./data/city-pedestrian-fatalities-2022-2024.csv) | 50 US cities, pedestrian fatality patterns from NHTSA FARS |
| [data/README.md](./data/README.md) | Dataset card for the CSV above |

## At a glance: the framework

A composite 0 to 100 score, displayed as 0 to 10:

| Component | Weight | Measures |
|-----------|--------|----------|
| Daily Reach | 50% | Walking access to 7 service categories within 15 minutes: grocery, healthcare, education, recreation, dining, shopping, civic |
| Street Safety | 20% | Crossing density, speed exposure, sidewalk coverage, traffic calming, lighting |
| Transit Reach | 15% | Transit stop density and route diversity, with a multi-modal bonus |
| Walking Comfort | 15% | Tree canopy, slope, air quality, heat stress |

Five tier labels at standard cutoffs:

| Tier | Display (0 to 10) | Internal (0 to 100) |
|------|-------------------|---------------------|
| Walkable | ≥ 8.0 | ≥ 80 |
| Moderate | ≥ 6.5 | ≥ 65 |
| Car-adjacent | ≥ 5.0 | ≥ 50 |
| Car-dependent | ≥ 3.5 | ≥ 35 |
| Hostile | < 3.5 | < 35 |

Full detail in [METHODOLOGY.md](./METHODOLOGY.md).

## Coverage

Works in 190+ countries. US addresses gain additional layers from EPA, US Census ACS, CDC PLACES, and FEMA. International addresses fall back to OpenStreetMap and Sentinel-2 satellite-derived proxies for the same components, with comparable score semantics.

## License

- All documentation and datasets in this repository are licensed under [CC BY 4.0](./LICENSE). Reuse is permitted with attribution to Streets & Commons.
- Any code samples that ship alongside (none in v1.0) are MIT licensed.

## Contact

- Live tool: [safestreets.streetsandcommons.com](https://safestreets.streetsandcommons.com)
- Organization: [streetsandcommons.com](https://streetsandcommons.com)
- Issues and methodology questions: GitHub Issues on this repository.
