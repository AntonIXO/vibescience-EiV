---
artifacts: []
closed: true
config_note: ''
cost: null
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
do_not_retry: []
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: training-the-encoder-on-the-1-minute-031c19
id: exp-1min-end-to-end-20260808
interpretation:
  at: '2026-08-18T20:56:52+00:00'
  failure_mode: null
  lesson: 'The 1-minute peak gain SURVIVES end-to-end training rather than being an
    artifact of the isolated control: incremental peak-excursion R2 0.4086 -> 0.4182
    and held-out hr R2 0.9713 -> 0.9777, at 3.1x the runtime (902 -> 2822 s). Both
    the peak gain and the saturated mean readout hold, so the migration is safe to
    build on — the cost is wall-clock, not representation quality.'
  transfers_to: []
key_numbers: {}
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
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[training-the-encoder-on-the-1-minute-031c19]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.4086 → after 0.4182  (Δ +0.0096, up ✓ predicted up)
- [[eiv_heldout_hr_r2]]: before 0.9713 → after 0.9777  (Δ +0.0064, up ✗ predicted none)
- [[eiv_training_runtime_sec]]: before 902.32 → after 2822.0  (Δ +1919.68, up)

**Verdict:** supports

## Interpretation
The 1-minute peak gain SURVIVES end-to-end training rather than being an artifact of the isolated control: incremental peak-excursion R2 0.4086 -> 0.4182 and held-out hr R2 0.9713 -> 0.9777, at 3.1x the runtime (902 -> 2822 s). Both the peak gain and the saturated mean readout hold, so the migration is safe to build on — the cost is wall-clock, not representation quality.

#epoch-resolution #wearables #baseline #dataset-whitelist
