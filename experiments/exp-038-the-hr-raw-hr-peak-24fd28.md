---
artifacts: []
closed: true
config_note: ''
cost: null
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
do_not_retry: []
external_run: ''
former_ids:
- the-hr-raw-hr-peak-asymmetry-is-substantially-a-decode-artifact-under-expectatio-exp-2026-08-06t18-26-08
gate_results: []
git_ref: ''
hypothesis_id: the-hr-raw-hr-peak-asymmetry-is-507782
id: exp-038-the-hr-raw-hr-peak-24fd28
interpretation:
  at: '2026-08-18T20:56:52+00:00'
  failure_mode: null
  lesson: 'Confirms the previous run''s implication on the preregistered direction:
    under expectation decoding hr_peak shows POSITIVE prefix-attributable skill —
    the real-minus-donor gap rose 0.48 -> 1.36 bpm, correlation 0.126 -> 0.22 and
    skill -1.91 -> -0.09, all three matching prediction. The donor-gap rise is the
    load-bearing number because it is the part attributable to the PREFIX rather than
    the template prior. Note the absolute level is still ~0, so this licenses ''the
    asymmetry is substantially a decode artifact'', not ''hr_peak works''.'
  transfers_to: []
key_numbers: {}
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
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-hr-raw-hr-peak-asymmetry-is-507782]].

## Measurements
- [[eiv_gen_peak_donor_gap_bpm]]: before 0.48 → after 1.36  (Δ +0.88, up ✓ predicted up)
- [[eiv_gen_peak_corr]]: before 0.126 → after 0.22  (Δ +0.094, up ✓ predicted up)
- [[eiv_gen_peak_skill_bpm]]: before -1.91 → after -0.09  (Δ +1.82, up ✓ predicted up)

**Verdict:** supports

## Interpretation
Confirms the previous run's implication on the preregistered direction: under expectation decoding hr_peak shows POSITIVE prefix-attributable skill — the real-minus-donor gap rose 0.48 -> 1.36 bpm, correlation 0.126 -> 0.22 and skill -1.91 -> -0.09, all three matching prediction. The donor-gap rise is the load-bearing number because it is the part attributable to the PREFIX rather than the template prior. Note the absolute level is still ~0, so this licenses 'the asymmetry is substantially a decode artifact', not 'hr_peak works'.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
