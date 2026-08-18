---
artifacts: []
closed: true
config_note: RHR-Diff(28d rolling median+MAD) + upper CuSum(k=0.5,h=4,min_run=2),
  corroboration_z=0.5, min_max_resid_sd=3.0, corroboration-shuffle null n=300
cost: null
crash_reason: ''
created_at: '2026-07-11T04:10:26+00:00'
diagnostics_measured:
- after: 2.0
  before: 0.0
  diagnostic_id: insights_yield
- after: 0.67
  before: 0.0
  diagnostic_id: corroboration
- after: 0.24
  before: 0.0
  diagnostic_id: fp_rate_null
- after: 0.0
  before: 0.0
  diagnostic_id: new_deps
do_not_retry: []
external_run: local:/opt/optihealth_analysis/src/test_resting_anomaly_realdata.py
former_ids: []
gate_results: []
git_ref: AntonIXO/optihealth_analysis@resting_anomaly-v1
hypothesis_id: rhr-cusum-adds-earlywarning
id: rhr-cusum-realdata-2026-07
interpretation:
  at: '2026-08-18T21:08:46+00:00'
  failure_mode: null
  lesson: 'A no-train, dependency-free RHR-Diff + CuSum detector (28d rolling median+MAD
    residual, upper CuSum k=0.5/h=4/min_run=2, corroboration_z=0.5, min_max_resid_sd=3.0)
    does surface early-warning episodes the existing day_vector module cannot: 2 episodes
    on real 365d data with corroboration 0.67 and 0 new deps — Dec 13-17 showing resting
    HR +10.1 robust-SD over 5d together with respiratory_rate +3.0 SD, and a second
    Jan 9-24 (16d) episode corroborated by hrv_rmssd. THE FALSE-POSITIVE PREDICTION
    FAILED and this is the actionable caveat: the detector was predicted to stay silent
    on null data but a first-cut count-based null gave fp=1.27, and even after redesigning
    the FP control to a corroboration-shuffle null (n=300) it still fires at fp=0.24.
    So a yield of 2 is NOT clean — roughly a quarter of that rate is reproducible
    on shuffled data, and any future work must report the specific null it used, because
    the fp number moved 1.27 -> 0.24 on null design alone. Tighten the magnitude floor
    or duration gate before treating episode counts as evidence.'
  transfers_to:
  - deep-vae-rhr-in-worker
key_numbers: {}
observed_effects:
- delta: 2.0
  diagnostic_id: insights_yield
  direction: up
- delta: 0.67
  diagnostic_id: corroboration
  direction: up
- delta: 0.24
  diagnostic_id: fp_rate_null
  direction: up
- delta: 0.0
  diagnostic_id: new_deps
  direction: none
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    corroboration: true
    fp_rate_null: false
    insights_yield: true
    new_deps: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[rhr-cusum-adds-earlywarning]].
At commit `AntonIXO/optihealth_analysis@resting_anomaly-v1`.
External run: local:/opt/optihealth_analysis/src/test_resting_anomaly_realdata.py

## Measurements
- [[insights_yield]]: before 0.0 → after 2.0  (Δ +2, up ✓ predicted up)
- [[corroboration]]: before 0.0 → after 0.67  (Δ +0.67, up ✓ predicted up)
- [[fp_rate_null]]: before 0.0 → after 0.24  (Δ +0.24, up ✗ predicted none)
- [[new_deps]]: before 0.0 → after 0.0  (Δ +0, flat ✓ predicted none)

**Verdict:** supports

## Interpretation
A no-train, dependency-free RHR-Diff + CuSum detector (28d rolling median+MAD residual, upper CuSum k=0.5/h=4/min_run=2, corroboration_z=0.5, min_max_resid_sd=3.0) does surface early-warning episodes the existing day_vector module cannot: 2 episodes on real 365d data with corroboration 0.67 and 0 new deps — Dec 13-17 showing resting HR +10.1 robust-SD over 5d together with respiratory_rate +3.0 SD, and a second Jan 9-24 (16d) episode corroborated by hrv_rmssd. THE FALSE-POSITIVE PREDICTION FAILED and this is the actionable caveat: the detector was predicted to stay silent on null data but a first-cut count-based null gave fp=1.27, and even after redesigning the FP control to a corroboration-shuffle null (n=300) it still fires at fp=0.24. So a yield of 2 is NOT clean — roughly a quarter of that rate is reproducible on shuffled data, and any future work must report the specific null it used, because the fp number moved 1.27 -> 0.24 on null design alone. Tighten the magnitude floor or duration gate before treating episode counts as evidence.

Transfers to: [[deep-vae-rhr-in-worker]]

## Notes
Dec 13-17 episode = clean illness signature: resting HR +10.1 robust-SD x5d WITH respiratory_rate +3.0 SD. Second episode Jan 9-24 (16d) corroborated by hrv_rmssd. First-cut count-based null gave fp=1.27 (prediction on fp_rate_null REFUTED); redesigned FP control to a corroboration-shuffle null -> fp=0.24. Magnitude floor min_max_resid_sd=3.0 added. 0 new deps. Method is additive: no existing module reports resting-physiology early-warning episodes.

#anomaly-detection #resting-hr #illness-early-warning #optihealth #wearables #n-of-1 #missing-early-warning #missing-circadian #missing-directionality
