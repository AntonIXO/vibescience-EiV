---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-08T06:59:59+00:00'
diagnostics_measured:
- after: 0.4182
  before: 0.4086
  diagnostic_id: eiv_peak_excursion_increment_r2
- after: 0.9777
  before: 0.9713
  diagnostic_id: eiv_heldout_hr_r2
- after: 2822.0
  before: 902.32
  diagnostic_id: eiv_training_runtime_sec
external_run: ''
gate_results: []
git_ref: ''
hypothesis_id: training-the-encoder-on-the-1-minute-canonical-tensor-n-23-patches-and-the-2-hou
id: exp-1min-end-to-end-20260808
observed_effects:
- delta: 0.0096
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
- delta: 0.0064
  diagnostic_id: eiv_heldout_hr_r2
  direction: up
- delta: 1919.68
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
Tests hypothesis [[training-the-encoder-on-the-1-minute-canonical-tensor-n-23-patches-and-the-2-hou]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.4086 → after 0.4182  (Δ +0.0096, up ✓ predicted up)
- [[eiv_heldout_hr_r2]]: before 0.9713 → after 0.9777  (Δ +0.0064, up ✗ predicted none)
- [[eiv_training_runtime_sec]]: before 902.32 → after 2822.0  (Δ +1919.68, up)

**Verdict:** supports

#epoch-resolution #wearables #baseline #dataset-whitelist
