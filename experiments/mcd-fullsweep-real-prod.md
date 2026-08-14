---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-07-11T16:19:10+00:00'
diagnostics_measured:
- after: 0.782
  before: 0.796
  diagnostic_id: anomaly_auroc
do_not_retry: []
external_run: local:/root/vibe-experiments/exp_mcd_masking.py
former_ids: []
gate_results: []
git_ref: optihealth_analysis@day_vector.py
hypothesis_id: mcd-improves-auroc-allregimes
id: mcd-fullsweep-real-prod
key_numbers: {}
observed_effects:
- delta: -0.014
  diagnostic_id: anomaly_auroc
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    anomaly_auroc: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[mcd-improves-auroc-allregimes]].
At commit `optihealth_analysis@day_vector.py`.
External run: local:/root/vibe-experiments/exp_mcd_masking.py

## Measurements
- [[anomaly_auroc]]: before 0.796 → after 0.782  (Δ -0.014, down ✗ predicted up)

**Verdict:** refutes

## Notes
Full sweep incl eps=15%: mean dAUROC=-0.014. MCD REGRESSES ranking at high contamination on n=189 (subsampling variance). Blanket swap REFUTED; only the guarded moderate-contamination precision@k win stands.

#robust-covariance #anomaly-detection #masking #covariance-contamination
