---
artifacts:
- logs/g5_heldout_pool.json
- logs/g5_heldout_pool.log
- scratch/g5_pool_ceiling.py
closed: true
config_note: 'scratch/g5_pool_ceiling.py --pool heldout --fold-seeds 5 --device-slice,
  CPU only (CUDA_VISIBLE_DEVICES= so it cannot contend with the seed-43 Phase C on
  the same card). Pool: subjects in val+test of data/canonical/split_subjects.json
  -> n=666 rows / 202 windows / 26 subjects / pos 0.508, against the clean pool''s
  2,136 / 666 / 113 / 0.512. Same row construction, same within-window permutation
  seeds, same GroupKFold headline; the 5 extra fold seeds shuffle SUBJECTS into folds
  and exist only to measure the noise floor. Cohorts present: lifesnaps 442, wesad
  148, popane_s6 24, neuro_stress_resilience_hci 23, case 15, dryad_ifh_affect 14.'
cost:
  gpu_hours: 0.0
  wall_hours: 0.85
crash_reason: ''
created_at: '2026-09-02T21:32:25+00:00'
diagnostics_measured:
- after: 0.1117
  before: 0.1649
  diagnostic_id: eiv_state_auc_pool_ceiling
- after: 0.1372
  before: 0.1515
  diagnostic_id: eiv_momentary_state_auc_over_null
- after: -0.0002
  before: 0.0145
  diagnostic_id: eiv_state_auc_trained_minus_random
do_not_retry:
- Do not quote a pooled momentary-state AUC on this corpus without the per-cohort
  slice. On held-out subjects the pooled +0.1117 decomposes into WESAD 0.9728 (n=148,
  a lab stressor protocol) and LifeSnaps 0.4744 (n=442, free-living, BELOW chance).
  The pooled number is a weighted average of a solved lab contrast and a null field
  contrast, and reads as neither.
- Do not spend GPU raising the ENCODER's momentary-state readout on the strength of
  the clean-pool +0.034 trained-minus-twin. On held-out subjects every encoder-only
  cell is at or below its own null (-0.0206 ridge, -0.0128 gb) and the union's trained-minus-random
  is -0.0002. Whatever Phase A/B put in does not cross a subject boundary.
- Do not read `union_ms/gb >= +0.11` as a pass without saying it cleared by 0.0017
  against a measured fold-seed sd of 0.0155, and that the gate's own both-families
  clause failed (union/ridge +0.0299, z +0.72).
external_run: logs/g5_heldout_pool.json
former_ids: []
gate_results:
- evidence: The NUMBER passes and the BOTH-FAMILIES clause fails, so the gate as written
    fails. union_ms/gb reads +0.1117 (AUC 0.6398, z +3.68) against the >= +0.11 bar
    -- clear by 0.0017, which is inside the 0.0155 fold-seed sd, so it is a coin-toss
    pass on magnitude alone. There is no union_ms/ridge cell in the harness; the nearest
    linear union, union/ridge, reads +0.0299 at z +0.72, far under the z >= 3 the
    gate demands of each family. Recording this as FAILED rather than quietly scoring
    the gb cell is the point of writing 'BOTH' before the run.
  gate_id: g1
  passed: false
- evidence: raw_multiscale/rf +0.1372 (AUC 0.6865, z +4.87, fold-seed sd 0.0294) against
    the >= +0.10 bar, with no encoder in the feature set. raw_shallow at one span
    also clears it (rf +0.1029, ridge +0.1050, gb +0.1247), so the claim 'the state
    slot is shippable without the chain' holds on held-out subjects.
  gate_id: g2
  passed: true
- evidence: 'The random twin was rebuilt from the same geometry and scored in the
    SAME invocation under both families. It says the encoder contributes nothing here:
    union_ms/gb +0.1117 vs union_ms@random/gb +0.1119 (delta -0.0002), and every encoder-ONLY
    cell sits at or below its own null -- enc_v41/ridge -0.0206, enc_ms/gb -0.0128,
    enc_ms/ridge +0.0179 (z +0.44), with the twins equally flat. Advisory gate, and
    the advice is that the +0.034 trained-minus-twin measured on the clean pool does
    not survive the subject split.'
  gate_id: g3
  passed: true
- evidence: 5 seeded subject-to-fold partitions plus the plain GroupKFold partition
    on identical rows and nulls; union_ms/gb sd(ddof=1) = 0.0155 AUC over the 6, registered
    on eiv_state_auc_pool_ceiling as noise_source=measured, n=6, k=2 band +/-0.031.
    Per-cell sds are in every g5 output's fold_auc_sd field and range from 0.0053
    (raw_shallow/ridge) to 0.0391 (enc_v41/ridge) -- the linear arms on raw features
    are the stable ones and the encoder arms are the noisy ones.
  gate_id: g4
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: c1-multiscale-union-readout-holds-on-the-splithonest-heldout-pool
id: exp-c1-heldout-pool-ceiling-20260903
interpretation:
  at: '2026-09-02T22:40:34+00:00'
  failure_mode: test_invalid
  lesson: 'The gate asked whether the CEILING transfers across subjects; the answer
    is "mostly, and it does not matter, because the pooled ceiling was never one quantity".
    Held-out (26 subjects): union_ms/gb +0.1117 (AUC 0.6398) and raw_multiscale/rf
    +0.1372 (0.6865) -- close to the clean pool''s +0.1649/+0.1515, so on the face
    of it the labels do support a readout. The per-cohort slice says that pooled number
    is an average of two regimes that should never have been averaged: WESAD 0.9728
    (n=148, a scripted lab baseline-vs-stress protocol) and LifeSnaps 0.4744 (n=442,
    free-living EMA, BELOW chance). Every "momentary state" number this project has
    quoted -- +0.0666, +0.1178, +0.1515, +0.1649, and the 0.72 "pool ceiling" in the
    problem header -- is a mixture with the same shape, because the pools were always
    built by concatenating lab and field corpora. `failure_mode: test_invalid` is
    chosen deliberately: the prediction was refuted, but what refuted it is that the
    metric aggregates incomparable populations, not that a multi-scale readout stops
    working. ACT ON THIS THREE WAYS. (1) Every future state readout reports per-cohort
    AUC with n beside the pooled figure; a pooled g5 number alone is no longer admissible
    evidence. (2) The encoder question is settled harder than the clean pool suggested:
    on subjects the chain never saw, every encoder-only cell is at or below its own
    null (-0.0206 ridge, -0.0128 gb) and the union''s trained-minus-random is -0.0002
    -- the +0.034 measured on the clean pool does not cross a subject boundary, so
    any arm justified by "training put state in the encoder" (Track E included) must
    show that on HELD-OUT subjects or it is measuring memorisation. (3) The product
    claim splits: a state slot is defensible on lab-like protocols and is a coin flip
    on free-living days, which is exactly the population the ring ships into -- so
    Track N''s untested state clause would be narrating noise on LifeSnaps-like data,
    and shipping it needs the field number, not the pooled one.'
  transfers_to:
  - e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling
  - c3b-caption-label-clauses-are-what-put-state-in-the-encoder
  - n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path
  - h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale
key_numbers:
  enc_ms_gb_over_null: -0.0128
  enc_v41_ridge_over_null: -0.0206
  fold_seed_sd: 0.0155
  lifesnaps_auc: 0.4744
  n_rows: 666.0
  n_subjects: 26.0
  n_windows: 202.0
  raw_multiscale_rf_auc: 0.6865
  raw_multiscale_rf_over_null: 0.1372
  raw_shallow_gb_over_null: 0.1247
  trained_minus_random: -0.0002
  union_ms_gb_auc: 0.6398
  union_ms_gb_over_null: 0.1117
  union_ms_random_gb_over_null: 0.1119
  wesad_auc: 0.9728
observed_effects:
- delta: -0.0532
  diagnostic_id: eiv_state_auc_pool_ceiling
  direction: down
- delta: -0.0143
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: down
- delta: -0.0147
  diagnostic_id: eiv_state_auc_trained_minus_random
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_momentary_state_auc_over_null: false
    eiv_state_auc_pool_ceiling: false
    eiv_state_auc_trained_minus_random: false
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: refutes
---

## Summary
Tests hypothesis [[c1-multiscale-union-readout-holds-on-the-splithonest-heldout-pool]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.
External run: logs/g5_heldout_pool.json

## Measurements
- [[eiv_state_auc_pool_ceiling]]: before 0.1649 → after 0.1117  (Δ -0.0532, down ✗ predicted up)
- [[eiv_momentary_state_auc_over_null]]: before 0.1515 → after 0.1372  (Δ -0.0143, down ✗ predicted up)
- [[eiv_state_auc_trained_minus_random]]: before 0.0145 → after -0.0002  (Δ -0.0147, down ✗ predicted none)

## Gates
- `g1`: **FAIL** — The NUMBER passes and the BOTH-FAMILIES clause fails, so the gate as written fails. union_ms/gb reads +0.1117 (AUC 0.6398, z +3.68) against the >= +0.11 bar -- clear by 0.0017, which is inside the 0.0155 fold-seed sd, so it is a coin-toss pass on magnitude alone. There is no union_ms/ridge cell in the harness; the nearest linear union, union/ridge, reads +0.0299 at z +0.72, far under the z >= 3 the gate demands of each family. Recording this as FAILED rather than quietly scoring the gb cell is the point of writing 'BOTH' before the run.
- `g2`: PASS — raw_multiscale/rf +0.1372 (AUC 0.6865, z +4.87, fold-seed sd 0.0294) against the >= +0.10 bar, with no encoder in the feature set. raw_shallow at one span also clears it (rf +0.1029, ridge +0.1050, gb +0.1247), so the claim 'the state slot is shippable without the chain' holds on held-out subjects.
- `g3`: PASS — The random twin was rebuilt from the same geometry and scored in the SAME invocation under both families. It says the encoder contributes nothing here: union_ms/gb +0.1117 vs union_ms@random/gb +0.1119 (delta -0.0002), and every encoder-ONLY cell sits at or below its own null -- enc_v41/ridge -0.0206, enc_ms/gb -0.0128, enc_ms/ridge +0.0179 (z +0.44), with the twins equally flat. Advisory gate, and the advice is that the +0.034 trained-minus-twin measured on the clean pool does not survive the subject split.
- `g4`: PASS — 5 seeded subject-to-fold partitions plus the plain GroupKFold partition on identical rows and nulls; union_ms/gb sd(ddof=1) = 0.0155 AUC over the 6, registered on eiv_state_auc_pool_ceiling as noise_source=measured, n=6, k=2 band +/-0.031. Per-cell sds are in every g5 output's fold_auc_sd field and range from 0.0053 (raw_shallow/ridge) to 0.0391 (enc_v41/ridge) -- the linear arms on raw features are the stable ones and the encoder arms are the noisy ones.

**Verdict:** refutes

## Interpretation
**Failure mode:** `test_invalid`

The gate asked whether the CEILING transfers across subjects; the answer is "mostly, and it does not matter, because the pooled ceiling was never one quantity". Held-out (26 subjects): union_ms/gb +0.1117 (AUC 0.6398) and raw_multiscale/rf +0.1372 (0.6865) -- close to the clean pool's +0.1649/+0.1515, so on the face of it the labels do support a readout. The per-cohort slice says that pooled number is an average of two regimes that should never have been averaged: WESAD 0.9728 (n=148, a scripted lab baseline-vs-stress protocol) and LifeSnaps 0.4744 (n=442, free-living EMA, BELOW chance). Every "momentary state" number this project has quoted -- +0.0666, +0.1178, +0.1515, +0.1649, and the 0.72 "pool ceiling" in the problem header -- is a mixture with the same shape, because the pools were always built by concatenating lab and field corpora. `failure_mode: test_invalid` is chosen deliberately: the prediction was refuted, but what refuted it is that the metric aggregates incomparable populations, not that a multi-scale readout stops working. ACT ON THIS THREE WAYS. (1) Every future state readout reports per-cohort AUC with n beside the pooled figure; a pooled g5 number alone is no longer admissible evidence. (2) The encoder question is settled harder than the clean pool suggested: on subjects the chain never saw, every encoder-only cell is at or below its own null (-0.0206 ridge, -0.0128 gb) and the union's trained-minus-random is -0.0002 -- the +0.034 measured on the clean pool does not cross a subject boundary, so any arm justified by "training put state in the encoder" (Track E included) must show that on HELD-OUT subjects or it is measuring memorisation. (3) The product claim splits: a state slot is defensible on lab-like protocols and is a coin flip on free-living days, which is exactly the population the ring ships into -- so Track N's untested state clause would be narrating noise on LifeSnaps-like data, and shipping it needs the field number, not the pooled one.

Transfers to: [[e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling]], [[c3b-caption-label-clauses-are-what-put-state-in-the-encoder]], [[n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path]], [[h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale]]

## Key numbers
- `enc_ms_gb_over_null`: -0.0128
- `enc_v41_ridge_over_null`: -0.0206
- `fold_seed_sd`: 0.0155
- `lifesnaps_auc`: 0.4744
- `n_rows`: 666
- `n_subjects`: 26
- `n_windows`: 202
- `raw_multiscale_rf_auc`: 0.6865
- `raw_multiscale_rf_over_null`: 0.1372
- `raw_shallow_gb_over_null`: 0.1247
- `trained_minus_random`: -0.0002
- `union_ms_gb_auc`: 0.6398
- `union_ms_gb_over_null`: 0.1117
- `union_ms_random_gb_over_null`: 0.1119
- `wesad_auc`: 0.9728

## Artifacts
- `logs/g5_heldout_pool.json`
- `logs/g5_heldout_pool.log`
- `scratch/g5_pool_ceiling.py`

## Do not retry
- Do not quote a pooled momentary-state AUC on this corpus without the per-cohort slice. On held-out subjects the pooled +0.1117 decomposes into WESAD 0.9728 (n=148, a lab stressor protocol) and LifeSnaps 0.4744 (n=442, free-living, BELOW chance). The pooled number is a weighted average of a solved lab contrast and a null field contrast, and reads as neither.
- Do not spend GPU raising the ENCODER's momentary-state readout on the strength of the clean-pool +0.034 trained-minus-twin. On held-out subjects every encoder-only cell is at or below its own null (-0.0206 ridge, -0.0128 gb) and the union's trained-minus-random is -0.0002. Whatever Phase A/B put in does not cross a subject boundary.
- Do not read `union_ms/gb >= +0.11` as a pass without saying it cleared by 0.0017 against a measured fold-seed sd of 0.0155, and that the gate's own both-families clause failed (union/ridge +0.0299, z +0.72).

## Notes
THE CEILING MOSTLY TRANSFERS AND THE ENCODER DOES NOT. Clean pool -> held-out (666 rows, 202 windows, 26 subjects): union_ms/gb +0.1649 -> +0.1117, raw_multiscale/rf +0.1515 -> +0.1372. The handcrafted multi-scale readout is what survives; the encoder collapses to chance (enc_v41/ridge -0.0206, enc_ms/gb -0.0128) and takes the union's advantage over its random twin with it (+0.0145 -> -0.0002).

THE FINDING THAT MATTERS MOST WAS NOT THE ONE THE GATE ASKED ABOUT. The device slice, added because Prajod & Andre 2022 (arXiv:2210.06225) says cross-device is where deep readouts fall behind features, shows the pooled number is an average of two incompatible regimes: WESAD 0.9728 (n=148) against LifeSnaps 0.4744 (n=442). WESAD is a scripted lab stressor with a clean baseline/stress contrast; LifeSnaps is free-living EMA. So "the pool supports AUC 0.72" -- and its held-out 0.64 -- is very largely the lab protocol being separable, and the free-living contrast this product actually ships against is at chance under every readout tried, handcrafted or learned.

WHAT THIS DOES TO THE PROGRAMME. (1) The state slot is shippable in the LAB sense and not in the FIELD sense; the narrator's state clause (Track N g3, untested) would be narrating a coin flip on LifeSnaps-like days. (2) Track E's premise -- distil EDA to lift the encoder's state readout -- is aimed at an encoder that carries nothing across subjects, and its clean-pool gate (+0.19 union) cannot detect that; E should be read against the HELD-OUT pool and the per-cohort slice before it is believed. (3) Amin 2025 (arXiv:2505.05694) reads 0.905-0.961 for wrist HRV-only on scripted lab stressors and 0.73 on the mixed set -- our WESAD 0.97 sits in that band, so nothing here contradicts the literature; what the literature does not report, and this does, is the free-living number beside it.

#optihealth #readout #control #baseline #optihealth-eiv #matched-baseline #subject-heldout #representation-learning
