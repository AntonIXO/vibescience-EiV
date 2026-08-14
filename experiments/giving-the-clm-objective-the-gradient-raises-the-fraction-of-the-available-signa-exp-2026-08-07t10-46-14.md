---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-07T10:46:14+00:00'
diagnostics_measured:
- after: 7.14
  before: 1.73
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
- after: 0.977
  before: 0.795
  diagnostic_id: eiv_gen_peak_corr
external_run: ''
gate_results: []
git_ref: ''
hypothesis_id: giving-the-clm-objective-the-gradient-raises-the-fraction-of-the-available-signa
id: giving-the-clm-objective-the-gradient-raises-the-fraction-of-the-available-signa-exp-2026-08-07t10-46-14
observed_effects:
- delta: 5.41
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
  direction: up
- delta: 0.182
  diagnostic_id: eiv_gen_peak_corr
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: true
    eiv_gen_peak_donor_gap_bpm: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[giving-the-clm-objective-the-gradient-raises-the-fraction-of-the-available-signa]].

## Measurements
- [[eiv_gen_peak_donor_gap_bpm]]: before 1.73 → after 7.14  (Δ +5.41, up ✓ predicted up)
- [[eiv_gen_peak_corr]]: before 0.795 → after 0.977  (Δ +0.182, up ✓ predicted up)

**Verdict:** supports

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
