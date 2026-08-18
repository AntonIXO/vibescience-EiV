---
artifacts: []
closed: true
config_note: ''
cost: null
crash_reason: ''
created_at: '2026-08-06T19:29:00+00:00'
diagnostics_measured:
- after: 0.659
  before: 0.138
  diagnostic_id: eiv_clm_value_gradient_share
do_not_retry: []
external_run: ''
former_ids:
- the-template-is-already-learned-so-completion-only-template-masked-loss-will-buy-exp-2026-08-06t19-29-00
gate_results: []
git_ref: ''
hypothesis_id: the-template-is-already-learned-so-eb13d1
id: exp-039-the-template-is-already-e265ed
interpretation:
  at: '2026-08-18T20:56:52+00:00'
  failure_mode: null
  lesson: 'Completion-only / template-masked loss is NOT the 7.2x lever the character-count
    argument implied: measured at the soft-token prefix, value tokens ALREADY supply
    the majority of the CLM gradient — eiv_clm_value_gradient_share 0.138 -> 0.659.
    The template is already learned, so masking it can only redistribute the remaining
    ~34%. Kill template-masking as a headline lever and reach for it only as a cheap
    hygiene rider with `direction: none` predicted on accuracy.'
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: 0.521
  diagnostic_id: eiv_clm_value_gradient_share
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_clm_value_gradient_share: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-template-is-already-learned-so-eb13d1]].

## Measurements
- [[eiv_clm_value_gradient_share]]: before 0.138 → after 0.659  (Δ +0.521, up ✓ predicted up)

**Verdict:** supports

## Interpretation
Completion-only / template-masked loss is NOT the 7.2x lever the character-count argument implied: measured at the soft-token prefix, value tokens ALREADY supply the majority of the CLM gradient — eiv_clm_value_gradient_share 0.138 -> 0.659. The template is already learned, so masking it can only redistribute the remaining ~34%. Kill template-masking as a headline lever and reach for it only as a cheap hygiene rider with `direction: none` predicted on accuracy.

#cross-modal #medgemma #sensor-text #optihealth-eiv #data-contract-violation #conditioning-collapse
