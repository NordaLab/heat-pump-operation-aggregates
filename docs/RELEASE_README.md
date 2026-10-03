# Residential heat-pump operation by temperature bin and hour, with the audits of a withheld synthetic generator (DPO-F11)

Version 1.0.1 (CHANGELOG.md). This release carries binned aggregates of hourly heat-pump electrical input (and, for
heatpumpmonitor.org, heat output, COP and flow and return temperature) per 2 degC bin of the daily mean outdoor
temperature and hour of day, each published cell over at least 50 households, from two open custodians: HEAPO
(Switzerland, 89 households with a separate heat-pump meter) and heatpumpmonitor.org (UK, 200 systems). It also
carries the memorisation and fidelity audits of a synthetic-day generator trained on these two custodians and WPuQ
(Germany), the pooled held-out utility summary of the companion claim OP-F11c, and the code. It carries NO synthetic
days: the generator failed its memorisation audit for every custodian condition, so no generated day is released
(DATA_PROTECTION.md). Licence CC BY 4.0 (LICENSE.txt); attribution and changes in NOTICE.txt; safeguards and audit
numbers in DATA_PROTECTION.md.

## Verify this release
Run `python verify_release.py` in this folder (Python 3 with numpy and pandas). It checks every file against
proof/MANIFEST.json (sha256) and recomputes every stated number in proof/FACTS.json from the data files; exit code
0 means all agree. The memorisation and fidelity audits themselves need the (non-redistributable) measured data;
their numbers are carried in audit/ and their pass flags are re-derived from those numbers by the recompute.

## Files
- `data/aggregates_hourly_by_tbin.csv`: per custodian, daily-mean-temperature bin (lower edge, 2 degC) and hour:
  number of households and hours, electrical input (mean, p10, p50, p90, kW), mean y; heatpumpmonitor also heat
  output, COP (ratio of sums), flow and return temperature, each with its own household count and published only
  where at least 50 households contribute that variable. Cells with fewer than 50 households or a household
  share above 20 percent are suppressed (section "Temperature coverage and suppressed bins"). The p10, p50 and p90
  columns are quantiles over all household-hours pooled in a cell, so they mix differences in household size with
  variation within households; they are not the distribution of the coincident load of a number of households.
- `data/aggregates_household_scale.csv`: distribution of the household scale s (kW per K) as mean and sd of ln(s) per
  custodian (at least 50 households).
- `audit/audit_memorisation.json`, `audit/audit_fidelity.json`, `audit/generator_card.json`: the audits of the
  withheld generator; `audit/utility_OPF11c_summary.json`: held-out evaluation of claim OP-F11c, pooled numbers only.
- `code/harmonise_sources.py`: rebuilds the hourly schema from the original sources (pointers inside);
  `code/lab_build/`: the exact build scripts and TASK_SPEC.json (they reference the lab's paths); r03_reseal_1_0_1.py
  wrote the documents of version 1.0.1.
- `CHANGELOG.md`: versions of this release.

## Temperature coverage and suppressed bins
Bins are 2 degC wide and named by their lower edge. Every suppressed cell failed the 50-household rule; the
20 percent household-share rule suppressed 0 cells. A suppressed bin is suppressed for all 24 hours.

| custodian | bins with data | lower edges (degC) | published bins | suppressed | reason |
|---|---|---|---|---|---|
| CH_HEAPO | 20 | -12 to 26 | 17 (-6 to 26, contiguous: yes) | 3 bins, 72 cells | fewer than 50 households |
| UK_heatpumpmonitor | 28 | -20 to 36 | 15 (-4 to 24, contiguous: yes) | 13 bins, 312 cells | fewer than 50 households |

Suppressed bins (no row of the table carries them):

| custodian | bin | cells suppressed | reason |
|---|---|---|---|
| CH_HEAPO | [-12, -10) degC | 24 of 24 | fewer than 50 households |
| CH_HEAPO | [-10, -8) degC | 24 of 24 | fewer than 50 households |
| CH_HEAPO | [-8, -6) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [-20, -18) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [-18, -16) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [-16, -14) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [-14, -12) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [-12, -10) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [-10, -8) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [-8, -6) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [-6, -4) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [26, 28) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [28, 30) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [30, 32) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [34, 36) degC | 24 of 24 | fewer than 50 households |
| UK_heatpumpmonitor | [36, 38) degC | 24 of 24 | fewer than 50 households |

Colder days than the coldest published bin exist in both sources, but in fewer than 50 households. The UK bins
at or below -10 degC and at or above 30 degC hold at most 3 systems each; daily means as extreme as
-20 degC and 36 to 38 degC are not credible UK weather in the snapshot window and most
likely come from faults of the systems' own outdoor sensors (all of them are suppressed).

The two coldest published bins per custodian rest on these days (counted on the lab's store; the table carries no
date column):

| custodian | bin | households (max per cell) | household-days | distinct dates | runs of consecutive dates | years |
|---|---|---|---|---|---|---|
| CH_HEAPO | [-6, -4) degC | 86 | 770 | 51 | 31 | 2020-2024 |
| CH_HEAPO | [-4, -2) degC | 88 | 1408 | 92 | 47 | 2020-2024 |
| UK_heatpumpmonitor | [-4, -2) degC | 121 | 357 | 81 | 40 | 2022-2025 |
| UK_heatpumpmonitor | [-2, 0) degC | 185 | 1308 | 167 | 52 | 2022-2025 |

## Household scale s
For each household, s = sum of electrical input / sum of (15 degC - outdoor temperature) over its hours with
outdoor temperature between its own 5th percentile and 15 degC (kW per K). y = electrical input / s.

## Generator (its synthetic days are withheld)
Conditional denoising diffusion model (MLP, 6 residual blocks, 512 hidden, 1000 diffusion steps, 30000 training steps,
EMA 0.999) on day vectors [t_out, log1p(y), asinh(q_norm), t_flow, t_ret] with masks for channels a custodian does not
measure; condition = daily mean temperature, season and custodian. Trained on 261 households (80 percent of
each custodian's eligible households); the other 65 households are the audit holdout. No day it generated is in
this release; it is described because the audits in audit/ are about it.

## Fidelity (synthetic vs holdout households; reference = training vs holdout households)
| custodian | discriminator AUC synth | AUC reference | KS daily peak synth | KS reference | W1 y synth | W1 y reference |
|---|---|---|---|---|---|---|
| CH_HEAPO | 0.974 | 0.979 | 0.077 | 0.089 | 0.811 | 0.502 |
| DE_WPuQ | 0.979 | 0.949 | 0.051 | 0.054 | 0.578 | 0.663 |
| UK_heatpumpmonitor | 0.963 | 0.912 | 0.106 | 0.048 | 0.605 | 0.348 |

## Limitations
- No coincidence or diversity information: the quantile columns are pooled household-hours, so after-diversity
  maximum demand, a feeder peak for a given number of connections, or a per-heat-pump contribution to a feeder peak
  cannot be derived from this table. The mean column is a large-number average conditional on the bin and hour.
- No stratification by heat-pump type (air or ground source), floor area, building age or emitter type. Floor area
  and property age were dropped at ingest as a data-protection safeguard (DATA_PROTECTION.md); heat-pump type is not a
  dimension of the table. HEAPO publishes household metadata with its data, so its own release has these covariates.
- The HEAPO rows use only the 89 households (of 93 with a separate heat-pump meter) that met the eligibility rule;
  HEAPO's households with a whole-house smart meter only were not used. All three sources are published openly at
  household level (NOTICE.txt), so every number here can be recomputed, with more detail, from them; this release
  adds the harmonisation, the suppression rule and the audits.
- The two custodians are not on the same axes. UK bins use each system's own outdoor sensor (see the implausible
  extremes above), HEAPO bins the custodian-assigned MeteoSwiss station; the UK meter can include domestic hot water
  and, for some systems, backup heaters or pumps, the HEAPO meter the heat pump only. Do not compare the custodians
  side by side at equal bin labels.
- Selection: heatpumpmonitor.org systems are self-selected (owners and installers who share data); the snapshot is
  the first 200 qualifying systems in ascending id of the 2026-09-28 public list, window 2022-10 to 2025-09. The HEAPO
  rows are its separately metered households. No weighting to an installed base is offered, so the COP and input
  columns are not mass-market planning numbers without a bias correction.
- Heat output and circuit temperatures exist only for heatpumpmonitor.org; HEAPO carries electrical input and
  outdoor temperature only.
- No date, year or season column and no split between space heating and domestic hot water: years are pooled
  (HEAPO 2019 to 2024, heatpumpmonitor.org 2022 to 2025), and cold-spell persistence (day 3 of a cold spell against day 1)
  is not represented.
- Held-out utility (companion claim OP-F11c): decision REFUTED; see audit/utility_OPF11c_summary.json.

## Cite
Cite this release and the three sources listed in NOTICE.txt.
