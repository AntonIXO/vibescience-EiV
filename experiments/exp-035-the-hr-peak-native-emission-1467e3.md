---
artifacts: []
closed: true
config_note: ''
cost: null
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
do_not_retry: []
external_run: ''
former_ids:
- the-hr-peak-native-emission-deficit-that-e24-e33-spent-ten-experiments-explainin-exp-2026-08-06t13-22-24
gate_results: []
git_ref: ''
hypothesis_id: the-hr-peak-native-emission-deficit-264f4f
id: exp-035-the-hr-peak-native-emission-1467e3
interpretation:
  at: '2026-08-18T20:56:51+00:00'
  failure_mode: mechanism_wrong
  lesson: 'The hr_peak emission deficit is REAL but its size was badly overstated
    by the earlier estimate, and the fix that improved skill did so by COLLAPSING
    the output rather than by reading the sensor. Skill moved -5.88 -> -1.91 bpm (+3.97)
    while between-sample std collapsed 20.758 -> 4.52 (-16.238), prediction-bucket
    entropy fell 0.37 -> 0.102, peak correlation FELL 0.276 -> 0.126, and the donor-prefix
    gap collapsed 9.916 -> 0.48 — i.e. almost none of the remaining skill is prefix-attributable.
    A skill gain bought by variance collapse is the model learning the marginal, not
    the conditional: always report skill WITH the between-sample std, the donor gap
    and the correlation, or a mode-collapse reads as progress.'
  transfers_to: []
key_numbers: {}
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
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-hr-peak-native-emission-deficit-264f4f]].

## Measurements
- [[eiv_gen_peak_skill_bpm]]: before -5.88 → after -1.91  (Δ +3.97, up ✗ predicted none)
- [[eiv_gen_peak_donor_gap_bpm]]: before 9.916 → after 0.48  (Δ -9.436, down ✗ predicted up)
- [[eiv_gen_pred_bucket_entropy]]: before 0.37 → after 0.102  (Δ -0.268, down ✗ predicted up)
- [[eiv_gen_peak_corr]]: before 0.276 → after 0.126  (Δ -0.15, down ✗ predicted up)
- [[eiv_gen_between_sample_std]]: before 20.758 → after 4.52  (Δ -16.238, down)

**Verdict:** refutes

## Interpretation
**Failure mode:** `mechanism_wrong`

The hr_peak emission deficit is REAL but its size was badly overstated by the earlier estimate, and the fix that improved skill did so by COLLAPSING the output rather than by reading the sensor. Skill moved -5.88 -> -1.91 bpm (+3.97) while between-sample std collapsed 20.758 -> 4.52 (-16.238), prediction-bucket entropy fell 0.37 -> 0.102, peak correlation FELL 0.276 -> 0.126, and the donor-prefix gap collapsed 9.916 -> 0.48 — i.e. almost none of the remaining skill is prefix-attributable. A skill gain bought by variance collapse is the model learning the marginal, not the conditional: always report skill WITH the between-sample std, the donor gap and the correlation, or a mode-collapse reads as progress.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
