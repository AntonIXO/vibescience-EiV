---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-07-11T16:18:46+00:00'
diagnostics_measured:
- after: 0.259
  before: 0.209
  diagnostic_id: anomaly_precision_at_k
- after: 0.853
  before: 0.837
  diagnostic_id: anomaly_auroc
external_run: local:/root/vibe-experiments/exp_mcd_masking.py
gate_results: []
git_ref: optihealth_analysis@day_vector.py
hypothesis_id: mcd-beats-lw-under-masking
id: mcd-masking-real-prod
observed_effects:
- delta: 0.05
  diagnostic_id: anomaly_precision_at_k
  direction: up
- delta: 0.016
  diagnostic_id: anomaly_auroc
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    anomaly_auroc: true
    anomaly_precision_at_k: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[mcd-beats-lw-under-masking]].
At commit `optihealth_analysis@day_vector.py`.
External run: local:/root/vibe-experiments/exp_mcd_masking.py

## Measurements
- [[anomaly_precision_at_k]]: before 0.209 → after 0.259  (Δ +0.05, up ✓ predicted up)
- [[anomaly_auroc]]: before 0.837 → after 0.853  (Δ +0.016, up ✓ predicted up)

**Verdict:** supports

## Notes
Real prod user 98f26806, n=189 day-vectors, p=13 dims, 30 seeds. MCD wins precision@k in 4/8 regimes and clearly at eps=8% (+5-6pp), but LOSES AUROC at eps=15% (small-n subsampling variance). Verdict is regime-dependent: promising for moderate contamination (the realistic top-5 operating point), NOT a safe blanket replacement. Guarded deploy only.

#robust-covariance #anomaly-detection #masking #covariance-contamination

#robust-covariance #anomaly-detection #masking #covariance-contamination
