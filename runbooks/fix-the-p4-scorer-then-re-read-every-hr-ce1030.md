---
created_at: '2026-08-07T20:55:27+00:00'
former_ids:
- fix-the-p4-scorer-then-re-read-every-hr-acc-ever-quoted-do-route-p4-s-true-hr-sr
gates: []
hypothesis_id: null
id: fix-the-p4-scorer-then-re-read-every-hr-ce1030
notes: []
priority: 7
problem_id: p4-bucket-probe-scores-hr-acc-against-a-d2175a
status: queued
topic_tags: []
updated_at: '2026-08-07T20:55:27+00:00'
---

## Prompt
FIX THE P4 SCORER, THEN RE-READ EVERY hr_acc EVER QUOTED.
DO: route p4's `true_hr` (src/probes/bucket_parser.py:242) through src/training/datasets.py:1183 `hr_label_bucket(hr_peak, hr_mean)` so the scorer and the training target are literally the same function. That is the same 'make divergence unrepresentable' argument hr_label_bucket's own docstring already makes about the sampler.
THEN: re-score the existing adapters (medgemma_12ch_v2_arm1, _arm2_nofilter, e40_clmbal) -- no retraining needed, the generations are cheap to redo at n=96 (~70 min each).
AND: fix the Stage-C tripwire in run_12ch_pipeline.sh that GATES on hr_acc. It is currently gating on a metric whose perfect-model ceiling (0.2604) is BELOW its majority-class baseline (0.5208), so it can never pass honestly. Treat it as broken alongside p3 and p6 until fixed.
ALSO IN THE SAME PROBE: hrv_acc and deep_sleep_acc are exactly 0.0000 on 96 samples for both arms with 0% parse failure, because true_hrv denormalises a rolling per-user z-score with population stats and assigns every subject a median of exactly 61.0 ms. That part is blocked on the re-merge runbook.

## Links
Queued for problem [[p4-bucket-probe-scores-hr-acc-against-a-d2175a]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
