---
created_at: '2026-08-07T20:55:14+00:00'
former_ids:
- measure-hr-peak-before-after-the-corpus-update-e34-native-emission-harness-why-t
gates: []
hypothesis_id: null
id: measure-hr-peak-before-after-the-corpus-ca10dc
notes:
- at: '2026-08-20T00:40:44+00:00'
  text: '2026-08-20 triage: the question is ANSWERED BY THIS SESSION''S CAPTURES without
    a dedicated run. v9band04 (the only healthy adapter) on the rebuilt 12,547-corpus
    eval draw, batch-1, n=200 supabase / 128 dense: hr_peak skill is NEGATIVE on both
    populations (supa MAE 21.25 vs trivial 17.41, skill -3.84, corr 0.138; dense MAE
    18.95 vs 17.59, skill -1.36, corr 0.311) while hr_raw on the same captures is
    healthy (supa 3.04, skill +0.91). The corpus update did NOT repair emitted hr_peak
    — consistent with the whole E6-E10/e34 line that the peak failure lives at the
    READOUT stage, not the data. The old-adapter-vs-new-adapter comparison the runbook
    asked for is moot: both named adapters (e40_clmbal, v2_arm1) are retired, their
    corpora overwritten, and the runbook''s own precondition (e40''s numbers not reproducible)
    now applies to everything pre-v9band04. The living version of this question is
    the new-corpus-encoder problem (exp-051 running) and the readout runbook rb-e4-lora-lm-head.'
priority: 5
problem_id: canonical-m-mask-marks-all-zero-889451
status: abandoned
topic_tags: []
updated_at: '2026-08-20T00:40:44+00:00'
---

## Prompt
MEASURE hr_PEAK BEFORE/AFTER THE CORPUS UPDATE (e34 native-emission harness).
WHY: the 2026-08-07 corpus update is ruled out as an hr_MEAN improvement, but hr_mean is saturated (R2 0.98) and was never the damaged quantity -- the recorded LifeSnaps hourly-grid emulation moved daily PEAK -9.90 bpm on 73.1% of days while the MEAN moved -0.03. The update has NOT been measured on the axis it repaired.
RUN scratch/eval_e34_native_emission.py on BOTH adapters against data/eval_v2 (n=5334, hr_cov>=0.75, lifesnaps+pmdata+supabase; matched trivial baselines already computed: always-predict-median MAE 6.83 bpm, cohort-only ceiling MAE 5.85 / corr +0.486):
  OLD outputs/adapters/medgemma_12ch_e40_clmbal   NEW outputs/adapters/medgemma_12ch_v2_arm1
REPORT hr_peak skill vs always-predict-median, hr_raw skill, the DONOR-PREFIX control, the real-minus-donor prefix-attributable gap, and correlation. All three comparators in the same sentence.
PREREGISTER FIRST via propose_hypothesis: expectation is hr_peak improves and hr_raw does not (saturated).
PRECONDITION: e40's own +7.14/+3.50 were measured on the OVERWRITTEN corpus and cannot be reproduced. Do not compare to them directly; re-measure e40 on data/eval_v2 in the same run.

## Log
- **2026-08-20T00:40:44+00:00** — 2026-08-20 triage: the question is ANSWERED BY THIS SESSION'S CAPTURES without a dedicated run. v9band04 (the only healthy adapter) on the rebuilt 12,547-corpus eval draw, batch-1, n=200 supabase / 128 dense: hr_peak skill is NEGATIVE on both populations (supa MAE 21.25 vs trivial 17.41, skill -3.84, corr 0.138; dense MAE 18.95 vs 17.59, skill -1.36, corr 0.311) while hr_raw on the same captures is healthy (supa 3.04, skill +0.91). The corpus update did NOT repair emitted hr_peak — consistent with the whole E6-E10/e34 line that the peak failure lives at the READOUT stage, not the data. The old-adapter-vs-new-adapter comparison the runbook asked for is moot: both named adapters (e40_clmbal, v2_arm1) are retired, their corpora overwritten, and the runbook's own precondition (e40's numbers not reproducible) now applies to everything pre-v9band04. The living version of this question is the new-corpus-encoder problem (exp-051 running) and the readout runbook rb-e4-lora-lm-head.

## Links
Queued for problem [[canonical-m-mask-marks-all-zero-889451]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
