---
created_at: '2026-08-07T12:14:18+00:00'
former_ids: []
gates:
- gate-strict-clean-population
- gate-powered-n-and-ci
- gate-donor-prefix-null
hypothesis_id: null
id: rb-data-repair-before-expansion-20260807
notes: []
priority: 5
problem_id: lifesnaps-hr-is-hourly-so-the-strict-15550c
status: queued
topic_tags: []
updated_at: '2026-08-07T12:14:18+00:00'
---

## Prompt
DATA REPAIR BEFORE MODEL TUNING, AND BEFORE DATASET EXPANSION. Ledger measured 2026-08-07:
  canonical corpus                      14013 days
  Phase C training filter                8774 (6944 after dropping degenerates)
  DENSE+CLEAN HR (only scorable cohort)  1641 = 11.7% of corpus, 1546 of it pmdata
  blocked by the M_mask zero defect      4423 dryad days with dense HR
  blocked by hourly sampling             6632 lifesnaps days
  -> repairing both yields 12696 dense clean days, 7.7x today's scorable pool.
Also: the 4-channel sleep block is DEAD (probability sum 0.027, p95 exactly 0.0000, ~37x too small);
hrv 83.9% / respiratory 49.4% / skin_temp 51.0% present corpus-wide but 0% on the scoring cohort.

WHY DATA AND NOT THE MODEL: after exp-e40 fixed the objective balance, EVERY remaining failing slot is a
data or target-construction defect, not a pipeline defect. hr_raw works (+3.50 bpm held-out, corr +0.871).
hr_peak fails because 76% of its training targets come from hourly data that understates the daily max by
9.90 bpm. heart_rate_bucket inherits that. hrv/rr/temp are absent on the eval cohort. The sleep percentages
are constant 0 because the channels are 37x too small. state is a saturated metadata heuristic (0.78 bits).

ORDER (repair before expansion; both repairs use data already on disk):
 1. M_mask zero defect (problem canonical-mask-marks-degenerate-zero-channels-as-present-20260805)
    -> unlocks 4423 dryad days. Largest single gain, no new downloads, no credentials.
 2. LifeSnaps resolution: verify whether mongo_rais_anonymized/ BSON carries intraday HR; if so re-derive
    at 15-min. Unlocks 6632 days AND removes the hr_peak label bias. If it does NOT, then instead gate the
    hr_peak slot off on hourly days so the model stops being taught a suppressed maximum.
 3. Sleep-channel scale bug: 4 of 12 channels currently carry no information.
 4. ONLY THEN expansion (configs/datasets.yaml configures just lifesnaps/globem/tiles; globem+tiles need
    external credentials, so they are slower and riskier than the two repairs above).

PRECONDITIONS THAT ARE PIPELINE WORK AND MUST NOT BE SKIPPED:
 * Confirm exp-e40 on a SECOND SEED first (~40 min). It is a 4-5x result from one seed and the Stage C
   bistability rate at hr_regression_weight=0.0 is unmeasured. If e40 is a lucky draw, every data
   experiment afterwards is scored against the wrong baseline.
 * Build a within-domain validation split. exp-e41 showed 49% retention (in-domain gap +7.14 vs held-out
   +3.50) and there is no way to separate memorisation from domain shift without one.
 * 7.7x more data makes the training budget WORSE, not better: e40 ran 0.75 epochs / 650 optimizer updates.
   Budget and epochs must scale with the corpus or the extra data will not be used.

EVALUATE EVERY DATA CHANGE WITH THE EXISTING APPARATUS: probe_e37 (hr_raw pmf), probe_e38 (hr_peak,
free-running context), probe_e41 (held-out cohort), all donor-controlled. Data work without that apparatus
would repeat the E24-E33 pattern of chasing an unpowered number.

## Preconditions
No GPU needed for the repairs themselves. Raw LifeSnaps CSVs are NOT currently on disk; the BSON intraday claim is UNVERIFIED and must be checked before it is relied on. Re-merge required after any schema/mask change, per CLAUDE.md.

## Links
Queued for problem [[lifesnaps-hr-is-hourly-so-the-strict-15550c]].
Intended gates: `gate-strict-clean-population`, `gate-powered-n-and-ci`, `gate-donor-prefix-null`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
