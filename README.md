# Residential heat-pump operation aggregates from 3 open sources with the audits of a synthetic generator (0 synthetic days released)

Version 1.0.1. This repository holds the data and metadata of the release, file by file: every file is byte-identical to the release, and its SHA-256 is listed in `MIRROR_MANIFEST.json`. The archival release, with the code that builds and verifies it, is deposited on Zenodo; its DOI is added here when it is published.

Version 1.0.1 (CHANGELOG.md). This release carries binned aggregates of hourly heat-pump electrical input (and, for heatpumpmonitor.org, heat output, COP and flow and return temperature) per 2 degC bin of the daily mean outdoor temperature and hour of day, each published cell over at least 50 households, from two open custodians: HEAPO (Switzerland, 89 households with a separate heat-pump meter) and heatpumpmonitor.org (UK, 200 systems). It also carries the memorisation and fidelity audits of a synthetic-day generator trained on these two custodians and WPuQ (Germany), the pooled held-out utility summary of the companion claim OP-F11c, and the code. It carries NO synthetic days: the generator failed its memorisation audit for every custodian condition, so no generated day is released (DATA_PROTECTION.md). Licence CC BY 4.0 (LICENSE.txt); attribution and changes in NOTICE.txt; safeguards and audit numbers in DATA_PROTECTION.md. Files: - `data/aggregates_hourly_by_tbin.csv`: per custodian, daily-mean-temperature bin (lower edge, 2 degC) and hour: number of households and hours, electrical input (mean, p10, p50, p90, kW), mean y; heatpumpmonitor also heat output, COP (ratio of sums), flow and return temperature, each with its own household count and published only where at least 50 households contribute that variable. Cells with fewer than 50 households or a household share above 20 percent are suppressed (section "Temperature coverage and suppressed bins"). The p10, p50 and p90 columns are quantiles over all household-hours pooled in a cell, so they mix differences in household size with variation within households; they are not the distribution of the coincident load of a number of households. - `data/aggregates_household_scale.csv`: distribution of the household scale s (kW per K) as mean and sd of ln(s) per custodian (at least 50 households). - `audit/audit_memorisation.json`, `audit/audit_fidelity.json`, `audit/generator_card.json`: the audits of the withheld generator; `audit/utility_OPF11c_summary.json`: held-out evaluation of claim OP-F11c, pooled numbers only. - `code/harmonise_sources.py`: rebuilds the hourly schema from the original sources (pointers inside); `code/lab_build/`: the exact build scripts and TASK_SPEC.json (they reference the lab's paths); r03_reseal_1_0_1.py wrote the documents of version 1.0.1. - `CHANGELOG.md`: versions of this release. Limitations: - No coincidence or diversity information: the quantile columns are pooled household-hours, so after-diversity maximum demand, a feeder peak for a given number of connections, or a per-heat-pump contribution to a feeder peak cannot be derived from this table. The mean column is a large-number average conditional on the bin and hour. - No stratification by heat-pump type (air or ground source), floor area, building age or emitter type. Floor area and property age were dropped at ingest as a data-protection safeguard (DATA_PROTECTION.md); heat-pump type is not a dimension of the table. HEAPO publishes household metadata with its data, so its own release has these covariates. - The HEAPO rows use only the 89 households (of 93 with a separate heat-pump meter) that met the eligibility rule; HEAPO's households with a whole-house smart meter only were not used. All three sources are published openly at household level (NOTICE.txt), so every number here can be recomputed, with more detail, from them; this release adds the harmonisation, the suppression rule and the audits. - The two custodians are not on the same axes. UK bins use each system's own outdoor sensor (see the implausible extremes above), HEAPO bins the custodian-assigned MeteoSwiss station; the UK meter can include domestic hot water and, for some systems, backup heaters or pumps, the HEAPO meter the heat pump only. Do not compare the custodians side by side at equal bin labels. - Selection: heatpumpmonitor.org systems are self-selected (owners and installers who share data); the snapshot is the first 200 qualifying systems in ascending id of the 2026-09-28 public list, window 2022-10 to 2025-09. The HEAPO rows are its separately metered households. No weighting to an installed base is offered, so the COP and input columns are not mass-market planning numbers without a bias correction. - Heat output and circuit temperatures exist only for heatpumpmonitor.org; HEAPO carries electrical input and outdoor temperature only. - No date, year or season column and no split between space heating and domestic hot water: years are pooled (HEAPO 2019 to 2024, heatpumpmonitor.org 2022 to 2025), and cold-spell persistence (day 3 of a cold spell against day 1) is not represented. - Held-out utility (companion claim OP-F11c): decision REFUTED; see audit/utility_OPF11c_summary.json. Licence: CC-BY-4.0 for the whole release (LICENSE.txt); attribution to every source is in NOTICE.txt.

## Creator

Yueqiang Xu, University of Oulu. ORCID: https://orcid.org/0000-0002-3528-423X

## How to cite

Use `CITATION.cff` (GitHub shows it under "Cite this repository"), and cite the source data this release is derived from:

- Brudermüller, T., Fleisch, E., González Vayá, M., &amp; Staake, T. (2025). <i>HEAPO – An Open Dataset for Heat Pump Optimization with Smart Electricity Meter Data and On-Site Inspection Protocols</i> (Version v1) [Data set]. ETH Zurich. https://doi.org/10.5281/ZENODO.15056919
- Schlemminger, M., Ohrdes, T., Schneider, E., &amp; Knoop, M. (2021). <i>WPuQ</i> (Version 2.0) [Data set]. Zenodo. https://doi.org/10.5281/ZENODO.5642902
- https://heatpumpmonitor.org

## Licence

cc-by-4.0. The licence text and any attribution notice required by the sources are in the licence and notice files of this repository; they travel with every copy.

## Data protection

See `DATA_PROTECTION.md`. The release holds aggregates only, published under the thresholds stated there.

## Files in the repository

| path | bytes |
|---|---|
| audit/audit_fidelity.json | 4,496 |
| audit/audit_memorisation.json | 2,811 |
| audit/generator_card.json | 720 |
| audit/utility_OPF11c_summary.json | 2,310 |
| data/aggregates_hourly_by_tbin.csv | 81,819 |
| data/aggregates_household_scale.csv | 271 |
| LICENSE.txt | 18,653 |
| NOTICE.txt | 2,191 |
| proof/FACTS.json | 2,090 |
| proof/MANIFEST.json | 3,843 |
| docs/RELEASE_README.md | 9,626 |

## Files attached to the GitHub release (larger than 50 MB)

| path | asset name | bytes |
|---|---|---|
| (none) | | |

## Not in this repository

17 file(s) of the release are not here: code, build logs, change history and build provenance records. `MIRROR_MANIFEST.json` lists each one; all are in the archival release.
