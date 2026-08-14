---
created_at: '2026-08-07T20:55:14+00:00'
gates: []
hypothesis_id: null
id: measure-hr-peak-before-after-the-corpus-update-e34-native-emission-harness-why-t
priority: 5
problem_id: canonical-mask-marks-degenerate-zero-channels-as-present-20260805
status: queued
topic_tags: []
updated_at: '2026-08-07T20:55:14+00:00'
---

## Prompt
MEASURE hr_PEAK BEFORE/AFTER THE CORPUS UPDATE (e34 native-emission harness).
WHY: the 2026-08-07 corpus update is ruled out as an hr_MEAN improvement, but hr_mean is saturated (R2 0.98) and was never the damaged quantity -- the recorded LifeSnaps hourly-grid emulation moved daily PEAK -9.90 bpm on 73.1% of days while the MEAN moved -0.03. The update has NOT been measured on the axis it repaired.
RUN scratch/eval_e34_native_emission.py on BOTH adapters against data/eval_v2 (n=5334, hr_cov>=0.75, lifesnaps+pmdata+supabase; matched trivial baselines already computed: always-predict-median MAE 6.83 bpm, cohort-only ceiling MAE 5.85 / corr +0.486):
  OLD outputs/adapters/medgemma_12ch_e40_clmbal   NEW outputs/adapters/medgemma_12ch_v2_arm1
REPORT hr_peak skill vs always-predict-median, hr_raw skill, the DONOR-PREFIX control, the real-minus-donor prefix-attributable gap, and correlation. All three comparators in the same sentence.
PREREGISTER FIRST via propose_hypothesis: expectation is hr_peak improves and hr_raw does not (saturated).
PRECONDITION: e40's own +7.14/+3.50 were measured on the OVERWRITTEN corpus and cannot be reproduced. Do not compare to them directly; re-measure e40 on data/eval_v2 in the same run.

## Links
Queued for problem [[canonical-mask-marks-degenerate-zero-channels-as-present-20260805]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
