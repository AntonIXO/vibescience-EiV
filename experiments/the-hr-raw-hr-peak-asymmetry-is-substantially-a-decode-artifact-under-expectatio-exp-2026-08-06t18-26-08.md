---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-06T18:26:08+00:00'
diagnostics_measured:
- after: 1.36
  before: 0.48
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
- after: 0.22
  before: 0.126
  diagnostic_id: eiv_gen_peak_corr
- after: -0.09
  before: -1.91
  diagnostic_id: eiv_gen_peak_skill_bpm
external_run: ''
gate_results: []
git_ref: ''
hypothesis_id: the-hr-raw-hr-peak-asymmetry-is-substantially-a-decode-artifact-under-expectatio
id: the-hr-raw-hr-peak-asymmetry-is-substantially-a-decode-artifact-under-expectatio-exp-2026-08-06t18-26-08
observed_effects:
- delta: 0.88
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
  direction: up
- delta: 0.094
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: 1.82
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: true
    eiv_gen_peak_donor_gap_bpm: true
    eiv_gen_peak_skill_bpm: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-hr-raw-hr-peak-asymmetry-is-substantially-a-decode-artifact-under-expectatio]].

## Measurements
- [[eiv_gen_peak_donor_gap_bpm]]: before 0.48 → after 1.36  (Δ +0.88, up ✓ predicted up)
- [[eiv_gen_peak_corr]]: before 0.126 → after 0.22  (Δ +0.094, up ✓ predicted up)
- [[eiv_gen_peak_skill_bpm]]: before -1.91 → after -0.09  (Δ +1.82, up ✓ predicted up)

**Verdict:** supports

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
