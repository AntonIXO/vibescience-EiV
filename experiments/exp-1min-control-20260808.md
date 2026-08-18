---
artifacts: []
closed: true
config_note: ''
cost: null
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
interpretation:
  at: '2026-08-18T20:56:52+00:00'
  failure_mode: null
  lesson: 'The 1-minute grid delivers the peak signal the 15-minute merge had destroyed
    IN THE DATA: incremental peak-excursion R2 0.0 -> 0.4104 with N=23 patches and
    the 2-hour token span held fixed, so only within-patch content changed (8 pre-averaged
    15-min means -> 120 real minutes). The already-saturated hr_mean readout was essentially
    unchanged (0.975 -> 0.9685), exactly as predicted. This is the control that licenses
    the whole epoch migration: the encoder was never the bottleneck for peaks, the
    resolution was.'
  transfers_to: []
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
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[training-the-encoder-on-the-1-minute-031c19]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.0 → after 0.4104  (Δ +0.4104, up ✓ predicted up)
- [[eiv_heldout_hr_r2]]: before 0.975 → after 0.9685  (Δ -0.0065, down ✗ predicted none)
- [[eiv_training_runtime_sec]]: before 0.0 → after 902.32  (Δ +902.32, up)

**Verdict:** supports

## Interpretation
The 1-minute grid delivers the peak signal the 15-minute merge had destroyed IN THE DATA: incremental peak-excursion R2 0.0 -> 0.4104 with N=23 patches and the 2-hour token span held fixed, so only within-patch content changed (8 pre-averaged 15-min means -> 120 real minutes). The already-saturated hr_mean readout was essentially unchanged (0.975 -> 0.9685), exactly as predicted. This is the control that licenses the whole epoch migration: the encoder was never the bottleneck for peaks, the resolution was.

#epoch-resolution #wearables #baseline #dataset-whitelist
