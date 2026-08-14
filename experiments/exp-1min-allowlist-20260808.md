---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-08T01:47:22+00:00'
diagnostics_measured:
- after: 0.3378
  before: 0.4086
  diagnostic_id: eiv_peak_excursion_increment_r2
- after: 0.9549
  before: 0.9713
  diagnostic_id: eiv_heldout_hr_r2
- after: 859.47
  before: 902.32
  diagnostic_id: eiv_training_runtime_sec
external_run: ''
gate_results: []
git_ref: ''
hypothesis_id: h1-dataset-allowlist-repairing-the-per-phase-dataset-filter-for-the-1-minute-gri
id: exp-1min-allowlist-20260808
observed_effects:
- delta: -0.0708
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: down
- delta: -0.0164
  diagnostic_id: eiv_heldout_hr_r2
  direction: down
- delta: -42.85
  diagnostic_id: eiv_training_runtime_sec
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_effective_rank: false
    eiv_heldout_hr_r2: false
    eiv_peak_excursion_increment_r2: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[h1-dataset-allowlist-repairing-the-per-phase-dataset-filter-for-the-1-minute-gri]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.4086 → after 0.3378  (Δ -0.0708, down ✗ predicted up)
- [[eiv_heldout_hr_r2]]: before 0.9713 → after 0.9549  (Δ -0.0164, down ✗ predicted up)
- [[eiv_training_runtime_sec]]: before 902.32 → after 859.47  (Δ -42.85, down)

**Verdict:** refutes

#dataset-whitelist #epoch-resolution #infonce #contrastive #collapse #wearables #baseline
