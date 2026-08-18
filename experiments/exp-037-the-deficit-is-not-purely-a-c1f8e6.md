---
artifacts: []
closed: true
config_note: ''
cost: null
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
do_not_retry: []
external_run: ''
former_ids:
- the-deficit-is-not-purely-a-decode-rule-artifact-because-the-dominant-error-is-b-exp-2026-08-06t16-35-38
gate_results: []
git_ref: ''
hypothesis_id: the-deficit-is-not-purely-a-decode-rule-1ccb22
id: exp-037-the-deficit-is-not-purely-a-c1f8e6
interpretation:
  at: '2026-08-18T20:56:51+00:00'
  failure_mode: mechanism_wrong
  lesson: 'Decoding the full conditional pmf instead of the argmax recovers a LOT
    more than expected: correlation 0.55 -> 0.795 and skill -1.91 -> +1.86 bpm, i.e.
    the emitted value crossed from worse-than-constant to better-than-constant WITHOUT
    retraining anything. The verdict is `refutes` only because the hypothesis predicted
    the expectation would STILL lose to always-predict-median (predicted `none` on
    skill) and it did not — the claim was too pessimistic, not the method. Read this
    as: a material share of the apparent hr_peak deficit IS a decode-rule artifact,
    and any future measurement of conditioning must separate decode rule from representation
    before concluding the signal is absent.'
  transfers_to: []
key_numbers: {}
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
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-deficit-is-not-purely-a-decode-rule-1ccb22]].

## Measurements
- [[eiv_gen_peak_skill_bpm]]: before -1.91 → after 1.86  (Δ +3.77, up ✗ predicted none)
- [[eiv_gen_peak_corr]]: before 0.55 → after 0.795  (Δ +0.245, up ✓ predicted up)
- [[eiv_gen_between_sample_std]]: before 4.52 → after 2.92  (Δ -1.6, down ✗ predicted up)

**Verdict:** refutes

## Interpretation
**Failure mode:** `mechanism_wrong`

Decoding the full conditional pmf instead of the argmax recovers a LOT more than expected: correlation 0.55 -> 0.795 and skill -1.91 -> +1.86 bpm, i.e. the emitted value crossed from worse-than-constant to better-than-constant WITHOUT retraining anything. The verdict is `refutes` only because the hypothesis predicted the expectation would STILL lose to always-predict-median (predicted `none` on skill) and it did not — the claim was too pessimistic, not the method. Read this as: a material share of the apparent hr_peak deficit IS a decode-rule artifact, and any future measurement of conditioning must separate decode rule from representation before concluding the signal is absent.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
