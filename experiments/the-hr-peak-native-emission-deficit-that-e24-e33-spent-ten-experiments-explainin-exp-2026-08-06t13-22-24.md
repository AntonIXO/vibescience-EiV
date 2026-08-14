---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-06T13:22:24+00:00'
diagnostics_measured:
- after: -1.91
  before: -5.88
  diagnostic_id: eiv_gen_peak_skill_bpm
- after: 0.48
  before: 9.916
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
- after: 0.102
  before: 0.37
  diagnostic_id: eiv_gen_pred_bucket_entropy
- after: 0.126
  before: 0.276
  diagnostic_id: eiv_gen_peak_corr
- after: 4.52
  before: 20.758
  diagnostic_id: eiv_gen_between_sample_std
external_run: ''
gate_results: []
git_ref: ''
hypothesis_id: the-hr-peak-native-emission-deficit-that-e24-e33-spent-ten-experiments-explainin
id: the-hr-peak-native-emission-deficit-that-e24-e33-spent-ten-experiments-explainin-exp-2026-08-06t13-22-24
observed_effects:
- delta: 3.97
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
- delta: -9.436
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
  direction: down
- delta: -0.268
  diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: down
- delta: -0.15
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: -16.238
  diagnostic_id: eiv_gen_between_sample_std
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
    eiv_gen_peak_donor_gap_bpm: false
    eiv_gen_peak_skill_bpm: false
    eiv_gen_pred_bucket_entropy: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-hr-peak-native-emission-deficit-that-e24-e33-spent-ten-experiments-explainin]].

## Measurements
- [[eiv_gen_peak_skill_bpm]]: before -5.88 → after -1.91  (Δ +3.97, up ✗ predicted none)
- [[eiv_gen_peak_donor_gap_bpm]]: before 9.916 → after 0.48  (Δ -9.436, down ✗ predicted up)
- [[eiv_gen_pred_bucket_entropy]]: before 0.37 → after 0.102  (Δ -0.268, down ✗ predicted up)
- [[eiv_gen_peak_corr]]: before 0.276 → after 0.126  (Δ -0.15, down ✗ predicted up)
- [[eiv_gen_between_sample_std]]: before 20.758 → after 4.52  (Δ -16.238, down)

**Verdict:** refutes

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
