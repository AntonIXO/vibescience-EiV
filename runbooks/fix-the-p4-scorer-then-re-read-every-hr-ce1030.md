---
created_at: '2026-08-07T20:55:27+00:00'
former_ids:
- fix-the-p4-scorer-then-re-read-every-hr-acc-ever-quoted-do-route-p4-s-true-hr-sr
gates: []
hypothesis_id: null
id: fix-the-p4-scorer-then-re-read-every-hr-ce1030
notes:
- at: '2026-08-20T00:40:04+00:00'
  text: '2026-08-20 triage: PARTIALLY SATISFIED BY LANDED CODE, REMAINDER MOOT. (1)
    The scorer fix landed as commit bba9bcf — bucket_parser.py:583-595 now grades
    the daily PEAK against the corpus cut points read from src.training.datasets (HR_BUCKET_TACHYCARDIC/ELEVATED),
    the same rule as training; the eiv_gen_bucket_acc_over_majority diagnostic carries
    the corrected reference values. (2) The ''gate on run_12ch_pipeline.sh tripwire''
    item is moot — the script no longer exists in scratch/. (3) The hrv_acc=0 sub-issue
    was blocked on the rolling-vs-population norm mismatch and is resolved at the
    root: the corpus has shipped population normalization since the day-anchor re-merge.
    (4) The remaining ask — re-score three RETIRED old-corpus adapters (v2_arm1, arm2_nofilter,
    e40_clmbal) at ~70 min each — is not worth GPU on adapters superseded by v9band04,
    whose corrected-scorer numbers exist on both draws. If a historical hr_acc quote
    is ever load-bearing, re-run the audit then.'
priority: 7
problem_id: p4-bucket-probe-scores-hr-acc-against-a-d2175a
status: abandoned
topic_tags: []
updated_at: '2026-08-20T00:40:04+00:00'
---

## Prompt
FIX THE P4 SCORER, THEN RE-READ EVERY hr_acc EVER QUOTED.
DO: route p4's `true_hr` (src/probes/bucket_parser.py:242) through src/training/datasets.py:1183 `hr_label_bucket(hr_peak, hr_mean)` so the scorer and the training target are literally the same function. That is the same 'make divergence unrepresentable' argument hr_label_bucket's own docstring already makes about the sampler.
THEN: re-score the existing adapters (medgemma_12ch_v2_arm1, _arm2_nofilter, e40_clmbal) -- no retraining needed, the generations are cheap to redo at n=96 (~70 min each).
AND: fix the Stage-C tripwire in run_12ch_pipeline.sh that GATES on hr_acc. It is currently gating on a metric whose perfect-model ceiling (0.2604) is BELOW its majority-class baseline (0.5208), so it can never pass honestly. Treat it as broken alongside p3 and p6 until fixed.
ALSO IN THE SAME PROBE: hrv_acc and deep_sleep_acc are exactly 0.0000 on 96 samples for both arms with 0% parse failure, because true_hrv denormalises a rolling per-user z-score with population stats and assigns every subject a median of exactly 61.0 ms. That part is blocked on the re-merge runbook.

## Log
- **2026-08-20T00:40:04+00:00** — 2026-08-20 triage: PARTIALLY SATISFIED BY LANDED CODE, REMAINDER MOOT. (1) The scorer fix landed as commit bba9bcf — bucket_parser.py:583-595 now grades the daily PEAK against the corpus cut points read from src.training.datasets (HR_BUCKET_TACHYCARDIC/ELEVATED), the same rule as training; the eiv_gen_bucket_acc_over_majority diagnostic carries the corrected reference values. (2) The 'gate on run_12ch_pipeline.sh tripwire' item is moot — the script no longer exists in scratch/. (3) The hrv_acc=0 sub-issue was blocked on the rolling-vs-population norm mismatch and is resolved at the root: the corpus has shipped population normalization since the day-anchor re-merge. (4) The remaining ask — re-score three RETIRED old-corpus adapters (v2_arm1, arm2_nofilter, e40_clmbal) at ~70 min each — is not worth GPU on adapters superseded by v9band04, whose corrected-scorer numbers exist on both draws. If a historical hr_acc quote is ever load-bearing, re-run the audit then.

## Links
Queued for problem [[p4-bucket-probe-scores-hr-acc-against-a-d2175a]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
