---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-07T23:03:47+00:00'
diagnostics_measured:
- after: 0.4104
  before: 0.0
  diagnostic_id: eiv_peak_excursion_increment_r2
- after: 0.9685
  before: 0.975
  diagnostic_id: eiv_heldout_hr_r2
- after: 902.32
  before: 0.0
  diagnostic_id: eiv_training_runtime_sec
do_not_retry: []
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: training-the-encoder-on-the-1-minute-031c19
id: exp-1min-control-20260808
key_numbers: {}
observed_effects:
- delta: 0.4104
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
- delta: -0.0065
  diagnostic_id: eiv_heldout_hr_r2
  direction: down
- delta: 902.32
  diagnostic_id: eiv_training_runtime_sec
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_heldout_hr_r2: false
    eiv_peak_excursion_increment_r2: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[training-the-encoder-on-the-1-minute-031c19]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.0 → after 0.4104  (Δ +0.4104, up ✓ predicted up)
- [[eiv_heldout_hr_r2]]: before 0.975 → after 0.9685  (Δ -0.0065, down ✗ predicted none)
- [[eiv_training_runtime_sec]]: before 0.0 → after 902.32  (Δ +902.32, up)

**Verdict:** supports

#epoch-resolution #wearables #baseline #dataset-whitelist
