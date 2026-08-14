---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-06T14:46:50+00:00'
diagnostics_measured:
- after: 0.163
  before: 0.126
  diagnostic_id: eiv_gen_peak_corr
- after: -1.31
  before: -1.91
  diagnostic_id: eiv_gen_peak_skill_bpm
- after: 3.51
  before: 4.52
  diagnostic_id: eiv_gen_between_sample_std
do_not_retry: []
external_run: ''
former_ids:
- salience-is-not-the-binding-constraint-increasing-the-magnitude-of-the-soft-toke-exp-2026-08-06t14-46-50
gate_results: []
git_ref: ''
hypothesis_id: salience-is-not-the-binding-constraint-54b713
id: exp-036-salience-is-not-the-binding-90d600
key_numbers: {}
observed_effects:
- delta: 0.037
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: 0.6
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
- delta: -1.01
  diagnostic_id: eiv_gen_between_sample_std
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_between_sample_std: false
    eiv_gen_peak_corr: false
    eiv_gen_peak_skill_bpm: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[salience-is-not-the-binding-constraint-54b713]].

## Measurements
- [[eiv_gen_peak_corr]]: before 0.126 → after 0.163  (Δ +0.037, up ✗ predicted none)
- [[eiv_gen_peak_skill_bpm]]: before -1.91 → after -1.31  (Δ +0.6, up ✗ predicted none)
- [[eiv_gen_between_sample_std]]: before 4.52 → after 3.51  (Δ -1.01, down ✗ predicted up)

**Verdict:** refutes

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
