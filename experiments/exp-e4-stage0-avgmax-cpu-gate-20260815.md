---
artifacts: []
closed: false
config_note: 'STAGE 0 ONLY (CPU/probe stage of the E4 plan). Evidence supplied by
  exp-e17-unified-readout-20260815 (raw canonical patches) and exp-e19-encoder-pooling-20260815
  + exp-e19b-pooling-recipe-level-20260815 (REAL trained Phase-B encoder tokens, 5
  checkpoints, shuffle control).


  PROCESS NOTE, RECORDED AGAINST MYSELF: E4 was proposed 2026-08-13 and predicts exactly
  the avg+max pooling effect I re-derived on 2026-08-15 as E17/E19 without noticing
  it existed. My recall() call used window/EMA/state keywords and did not surface
  it. E17/E19 are therefore INDEPENDENT REPLICATIONS of E4''s Stage-0 premise rather
  than novel hypotheses, and the credit belongs to E4. Lesson for the operating skill:
  before proposing an architecture hypothesis, recall on the INTERVENTION (pooling/avgmax/patching)
  as well as the symptom.


  Stage 1 (GPU end-to-end) is NOT run here and remains open as rb-w4-projector-maxpool-end-to-end-20260815.'
crash_reason: ''
created_at: '2026-08-15T06:55:14+00:00'
diagnostics_measured:
- after: 0.6849
  before: 0.003
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry: []
external_run: logs/e19_encoder_pooling.json + logs/e19b_pooling_across_checkpoints.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: e4-g0-cpu-excursion-increment-ge-0-25
  passed: true
git_ref: ''
hypothesis_id: e4-encoder-excursion-avgmax-20260813
id: exp-e4-stage0-avgmax-cpu-gate-20260815
key_numbers: {}
observed_effects:
- delta: 0.6819
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: exp-e19b-pooling-recipe-level-20260815
prediction_match: null
provenance: {}
verdict: null
---

## Summary
Tests hypothesis [[e4-encoder-excursion-avgmax-20260813]].
Branches from [[exp-e19b-pooling-recipe-level-20260815]].
External run: logs/e19_encoder_pooling.json + logs/e19b_pooling_across_checkpoints.json

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.003 → after 0.6849  (Δ +0.6819, up)

## Gates
- `e4-g0-cpu-excursion-increment-ge-0-25`: PASS

#cross-modal #collapse #deployment-distribution #optihealth #baseline
