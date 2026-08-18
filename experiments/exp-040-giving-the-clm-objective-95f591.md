---
artifacts: []
closed: true
config_note: ''
cost: null
crash_reason: ''
created_at: '2026-08-07T10:46:14+00:00'
diagnostics_measured:
- after: 7.14
  before: 1.73
  diagnostic_id: eiv_gen_peak_donor_gap_bpm
- after: 0.977
  before: 0.795
  diagnostic_id: eiv_gen_peak_corr
do_not_retry: []
external_run: ''
former_ids:
- giving-the-clm-objective-the-gradient-raises-the-fraction-of-the-available-signa-exp-2026-08-07t10-46-14
gate_results: []
git_ref: ''
hypothesis_id: giving-the-clm-objective-the-gradient-9b4f55
id: exp-040-giving-the-clm-objective-95f591
interpretation:
  at: '2026-08-18T20:56:52+00:00'
  failure_mode: null
  lesson: 'Removing the auxiliary objectives so the CLM objective gets the whole gradient
    (probe_weight 1.0 -> 0.0, hr_regression_weight 0.1 -> 0.0, aux-only warmup 100
    -> 0, otherwise identical) produced the largest prefix-attributable gain in this
    chain: donor gap 1.73 -> 7.14 bpm and correlation 0.795 -> 0.977, both matching
    prediction. The auxiliary probe/regression heads were COMPETING with the objective
    that actually drives emission. Default to pure CLM for Phase C, and treat any
    auxiliary head as something that must earn its gradient share against this result.'
  transfers_to: []
key_numbers: {}
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
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[giving-the-clm-objective-the-gradient-9b4f55]].

## Measurements
- [[eiv_gen_peak_donor_gap_bpm]]: before 1.73 → after 7.14  (Δ +5.41, up ✓ predicted up)
- [[eiv_gen_peak_corr]]: before 0.795 → after 0.977  (Δ +0.182, up ✓ predicted up)

**Verdict:** supports

## Interpretation
Removing the auxiliary objectives so the CLM objective gets the whole gradient (probe_weight 1.0 -> 0.0, hr_regression_weight 0.1 -> 0.0, aux-only warmup 100 -> 0, otherwise identical) produced the largest prefix-attributable gain in this chain: donor gap 1.73 -> 7.14 bpm and correlation 0.795 -> 0.977, both matching prediction. The auxiliary probe/regression heads were COMPETING with the objective that actually drives emission. Default to pure CLM for Phase C, and treat any auxiliary head as something that must earn its gradient share against this result.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
