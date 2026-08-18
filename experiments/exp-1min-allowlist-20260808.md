---
artifacts: []
closed: true
config_note: ''
cost: null
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
do_not_retry: []
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: h1-dataset-allowlist-repairing-the-per-93cde5
id: exp-1min-allowlist-20260808
interpretation:
  at: '2026-08-18T20:56:52+00:00'
  failure_mode: claim_false
  lesson: 'Repairing the per-phase dataset allowlist for the 1-minute grid (dropping
    the datasets contributing zero usable days, admitting optihealth_supabase and
    mmash) HURT every representation metric it was predicted to improve: incremental
    peak-excursion R2 0.4086 -> 0.3378, held-out hr R2 0.9713 -> 0.9549, all predicted
    UP. More data from more sources is not automatically better here — admitting sparser/lower-coverage
    datasets degraded the readout. IMPORTANT CAVEAT: the peak-excursion delta of -0.0708
    is only ~1.5x the recipe-level band of 0.0457 later measured on this diagnostic
    (exp-e19b), so treat the direction as real but the magnitude as poorly resolved;
    re-run per-seed before quoting the size.'
  transfers_to: []
key_numbers: {}
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
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[h1-dataset-allowlist-repairing-the-per-93cde5]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.4086 → after 0.3378  (Δ -0.0708, down ✗ predicted up)
- [[eiv_heldout_hr_r2]]: before 0.9713 → after 0.9549  (Δ -0.0164, down ✗ predicted up)
- [[eiv_training_runtime_sec]]: before 902.32 → after 859.47  (Δ -42.85, down)

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

Repairing the per-phase dataset allowlist for the 1-minute grid (dropping the datasets contributing zero usable days, admitting optihealth_supabase and mmash) HURT every representation metric it was predicted to improve: incremental peak-excursion R2 0.4086 -> 0.3378, held-out hr R2 0.9713 -> 0.9549, all predicted UP. More data from more sources is not automatically better here — admitting sparser/lower-coverage datasets degraded the readout. IMPORTANT CAVEAT: the peak-excursion delta of -0.0708 is only ~1.5x the recipe-level band of 0.0457 later measured on this diagnostic (exp-e19b), so treat the direction as real but the magnitude as poorly resolved; re-run per-seed before quoting the size.

#dataset-whitelist #epoch-resolution #infonce #contrastive #collapse #wearables #baseline
