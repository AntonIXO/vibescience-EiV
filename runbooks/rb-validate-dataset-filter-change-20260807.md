---
created_at: '2026-08-07T12:35:40+00:00'
former_ids: []
gates:
- gate-powered-n-and-ci
- gate-donor-prefix-null
- gate-strict-clean-population
hypothesis_id: null
id: rb-validate-dataset-filter-change-20260807
notes: []
priority: 7
problem_id: canonical-m-mask-marks-all-zero-889451
status: abandoned
topic_tags: []
updated_at: '2026-08-07T20:56:06+00:00'
---

## Prompt
VALIDATE THE dataset_filter CHANGE MADE 2026-08-07 (configs/training.yaml, lora_proxy_12ch).
optihealth_supabase: 1.0 was added. CONFIG-LEVEL VALIDATION ALREADY PASSED - the trainer logs
  'Dropped 1831/9059 ... Remaining: 7228' (was 6944)
  'dataset_filter={lifesnaps: 1.0, pmdata: 1.0, optihealth_supabase: 1.0}'
  realised marginal brady .167 / resting .285 / elevated .309 / tachy .239 (was .164/.289/.309/.238)
so the filter parses, the corpus grows 3.9%, and the marginal it exists to protect is unchanged.
NOT YET DONE: a full training run. The smoke died at cublasCreate with ~9.6 GiB free because desktop apps
(anki, showtime, gnome-browser-connector) held 2.5 GiB of the 12.2 GiB card; Phase C needs ~11.7 GiB.
CLOSE THEM BEFORE ANY PHASE C RUN - this is an environment precondition, not a code bug.

WHAT TO RUN: profile lora_proxy_12ch_clmbal on a SECOND SEED with the new filter. One run, three purposes:
  1. confirms exp-e40's 4-5x result is not a one-seed lucky draw. The Stage C bistability rate at
     hr_regression_weight=0.0 is UNMEASURED; the documented ~50% was taken at 0.002.
  2. validates the filter change end to end.
  3. first Phase C model ever trained on a cohort where hrv / respiratory_rate / skin_temperature / sleep
     are actually present (optihealth_supabase is 100% on all four; the scoring cohort is 0% on all four).
EVALUATE WITH scratch/probe_e37_pmf_decode.py (hr_raw), probe_e38_peak_pmf.py (hr_peak, free-running
context), probe_e41_heldout.py (held-out). All donor-controlled. Compare to e40: hr_raw in-domain gap
+7.14 / corr +0.977; held-out gap +3.50 / corr +0.871; hr_peak gap +7.28 / absolute skill +0.29.
DO NOT score `state` on supabase rows - 0% labels there, so it falls back to the inferred default, and
state is dataset-confounded anyway (pmdata 72.8% stress vs lifesnaps 68.8% neutral).

## Preconditions
GPU needs ~11.7 GiB free. ASK ANTON before relying on supabase inclusion: it is the production app's own user data and no privacy/leakage rationale is recorded either way.

## Links
Queued for problem [[canonical-m-mask-marks-all-zero-889451]].
Intended gates: `gate-powered-n-and-ci`, `gate-donor-prefix-null`, `gate-strict-clean-population`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
