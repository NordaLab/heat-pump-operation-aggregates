<!-- Written for this mirror from the release's DATA_PROTECTION.md; wording only (the authors' voice), every safeguard and number unchanged. -->
# Data protection statement (DPO-F11)

Household heat-pump series are ordinary personal data (not GDPR Article 9). Legal basis recorded by the authors: Article 6(1)(e) GDPR with Article 89(1) safeguards and the Finnish Data Protection Act 1050/2018 section 4.
The university data protection officer is notified before public deposit.

## Safeguards and how they were enforced
- Dropped AT INGEST and never written to disk: location and coordinates, installer names, floor area, property age.
  heatpumpmonitor.org: the public system list was reduced in memory to an allowlist of 18 fields before writing
  (the 86 other fields, including location, latitude, longitude, installer name/url/logo, floor_area, age, free-text
  notes and derived fields such as kwh_m2, were never stored). HEAPO: the Zenodo archive was held in memory only; its
  meta_data/ tables (building attributes including living area) and reports/ were never written; the station
  assignment (Weather_ID) was used in memory to join outdoor temperature and then discarded. WPuQ: the
  datapackage.json 'spatial' block (location) was removed before writing.
- Raw series stay on the lab's local store and are not in this release; heatpumpmonitor.org series are
  pointer-only (code/harmonise_sources.py fetches them from the custodian).
- This release carries binned aggregates and audits only; it holds no synthetic day and no measured series.
  Aggregates are published for HEAPO and heatpumpmonitor.org only, and a cell is published only if at least 50
  households contribute and no household contributes more than 20 percent of the cell's hours. WPuQ (38 households in
  one village) contributes no aggregate; its days were used only to train and audit the generator.
- Suppressed cells: 72 HEAPO cells and 312 heatpumpmonitor.org cells, whole bins in every case, all for fewer than
  50 households (CH_HEAPO: [-12, -10) degC, [-10, -8) degC, [-8, -6) degC; UK_heatpumpmonitor: [-20, -18) degC, [-18, -16) degC, [-16, -14) degC, [-14, -12) degC, [-12, -10) degC, [-10, -8) degC, [-8, -6) degC, [-6, -4) degC, [26, 28) degC, [28, 30) degC, [30, 32) degC, [34, 36) degC, [36, 38) degC). README.md lists them.
- Synthetic days ship only if the memorisation audit passes for every custodian condition. Result: FAIL - synthetic days NOT shipped; no generated day is in this release.

## Memorisation audit (dpkit.audit.memorisation_audit)
Records are day vectors [outdoor temperature (24 h), y (24 h)] (all five channels for heatpumpmonitor.org);
train = days of the generator's training households, holdout = days of households the generator never saw
(generator seed 0). PASS requires DCR ratio >= the real-split baseline (5th percentile of 1000 holdout
bootstrap ratios) and membership-inference AUC <= 0.55.

| audit | n train | n holdout | n synthetic | DCR ratio | real-split baseline p05 | MIA AUC | result |
|---|---|---|---|---|---|---|---|
| CH_HEAPO__t_out_y | 20000 | 13610 | 20000 | 0.971 | 0.990 | 0.515 | FAIL |
| DE_WPuQ__t_out_y | 20000 | 5684 | 20000 | 0.943 | 0.982 | 0.507 | FAIL |
| UK_heatpumpmonitor__t_out_y | 20000 | 20000 | 20000 | 0.926 | 0.992 | 0.531 | FAIL |
| UK_heatpumpmonitor__all_channels | 20000 | 20000 | 19669 | 0.904 | 0.994 | 0.540 | FAIL |

Household split: {"CH_HEAPO": {"households": 89, "generator_train_households": 71, "holdout_households": 18}, "DE_WPuQ": {"households": 37, "generator_train_households": 30, "holdout_households": 7}, "UK_heatpumpmonitor": {"households": 200, "generator_train_households": 160, "holdout_households": 40}}.

## What the audit does and does not show
- The memorisation audit tests closeness to training records only. A generator that produces implausible days
  also passes it (a builder test with an untrained generator passed with DCR ratios far above 1), so usefulness
  is shown by the fidelity audit (README.md, audit/audit_fidelity.json), not by this audit.
- Residual linkage on the local store (not in this release): HEAPO outdoor temperature is the custodian-assigned
  station series, so a household's harmonised temperature matches one of the custodian's station files. The
  station assignment itself was never written.
