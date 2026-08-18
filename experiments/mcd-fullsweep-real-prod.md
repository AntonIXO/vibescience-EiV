---
artifacts: []
closed: true
config_note: ''
cost: null
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
interpretation:
  at: '2026-08-18T21:08:46+00:00'
  failure_mode: claim_false
  lesson: 'MCD is not a safe blanket replacement for Ledoit-Wolf in the Mahalanobis
    day-vector scorer: over the full k x eps sweep including eps=15%, mean AUROC went
    0.796 -> 0.782 (mean dAUROC = -0.014), so the ''improves AUROC across ALL contamination
    levels'' claim fails at its own decisive point. The regression is attributed in-record
    to subsampling variance at n=189 and no CI is reported, so -0.014 is a decision-grade
    negative rather than a precise effect estimate — do not quote it as a measured
    harm. What survives is the guarded, regime-conditional win only (moderate-contamination
    precision@k); if MCD is revisited, gate it per-contamination-regime and raise
    n before trusting any high-eps AUROC number.'
  transfers_to:
  - mcd-beats-lw-under-masking
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
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[mcd-improves-auroc-allregimes]].
At commit `optihealth_analysis@day_vector.py`.
External run: local:/root/vibe-experiments/exp_mcd_masking.py

## Measurements
- [[anomaly_auroc]]: before 0.796 → after 0.782  (Δ -0.014, down ✗ predicted up)

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

MCD is not a safe blanket replacement for Ledoit-Wolf in the Mahalanobis day-vector scorer: over the full k x eps sweep including eps=15%, mean AUROC went 0.796 -> 0.782 (mean dAUROC = -0.014), so the 'improves AUROC across ALL contamination levels' claim fails at its own decisive point. The regression is attributed in-record to subsampling variance at n=189 and no CI is reported, so -0.014 is a decision-grade negative rather than a precise effect estimate — do not quote it as a measured harm. What survives is the guarded, regime-conditional win only (moderate-contamination precision@k); if MCD is revisited, gate it per-contamination-regime and raise n before trusting any high-eps AUROC number.

Transfers to: [[mcd-beats-lw-under-masking]]

## Notes
Full sweep incl eps=15%: mean dAUROC=-0.014. MCD REGRESSES ranking at high contamination on n=189 (subsampling variance). Blanket swap REFUTED; only the guarded moderate-contamination precision@k win stands.

#robust-covariance #anomaly-detection #masking #covariance-contamination
