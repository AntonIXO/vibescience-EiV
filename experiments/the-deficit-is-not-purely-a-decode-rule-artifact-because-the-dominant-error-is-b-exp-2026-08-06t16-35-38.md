---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-06T16:35:38+00:00'
diagnostics_measured:
- after: 1.86
  before: -1.91
  diagnostic_id: eiv_gen_peak_skill_bpm
- after: 0.795
  before: 0.55
  diagnostic_id: eiv_gen_peak_corr
- after: 2.92
  before: 4.52
  diagnostic_id: eiv_gen_between_sample_std
external_run: ''
gate_results: []
git_ref: ''
hypothesis_id: the-deficit-is-not-purely-a-decode-rule-artifact-because-the-dominant-error-is-b
id: the-deficit-is-not-purely-a-decode-rule-artifact-because-the-dominant-error-is-b-exp-2026-08-06t16-35-38
observed_effects:
- delta: 3.77
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
- delta: 0.245
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: -1.6
  diagnostic_id: eiv_gen_between_sample_std
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_between_sample_std: false
    eiv_gen_peak_corr: true
    eiv_gen_peak_skill_bpm: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-deficit-is-not-purely-a-decode-rule-artifact-because-the-dominant-error-is-b]].

## Measurements
- [[eiv_gen_peak_skill_bpm]]: before -1.91 → after 1.86  (Δ +3.77, up ✗ predicted none)
- [[eiv_gen_peak_corr]]: before 0.55 → after 0.795  (Δ +0.245, up ✓ predicted up)
- [[eiv_gen_between_sample_std]]: before 4.52 → after 2.92  (Δ -1.6, down ✗ predicted up)

**Verdict:** refutes

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
