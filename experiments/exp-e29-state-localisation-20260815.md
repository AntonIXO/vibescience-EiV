---
artifacts:
- scratch/e29_state_localisation_and_supply.py
- logs/e29_state_localisation_and_supply.json
- commit b34ed44
closed: true
config_note: 'CPU/GPU probe, no training. Repo /root/optiHealth-EiV, commit b34ed44.
  Script scratch/e29_state_localisation_and_supply.py, results logs/e29_state_localisation_and_supply.json.
  Checkpoint outputs/checkpoints/v5c/phase_b (encoder weights loaded with refusal-to-proceed
  on any missing key). Rows: E26''s exact within-window transition set, n=3226 epochs
  / 87 subjects / 660 windows, flow vs stress, pos rate 0.481, W=120 min. GroupKFold(5)
  by subject; RandomForestClassifier(n_estimators=300, min_samples_leaf=3, random_state=0).
  Three feature sets on identical rows/folds: raw_shallow (15 percentile feats), raw_patch_grid
  (8 feats, avgmax over patches covering [epoch-120, epoch) on the encoder''s own
  patch_len=120/stride=60 grid, zero learned parameters), enc_token (9216 feats, trained
  encoder tokens, same covering patches, same avgmax pool). Each arm scored against
  its OWN 24-permutation within-window shuffle null. Label supply counted via src.training.datasets.state_series.'
crash_reason: ''
created_at: '2026-08-15T10:20:39+00:00'
diagnostics_measured:
- after: 0.112
  before: -0.0293
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- 'Do NOT re-run the avg->avgmax / max pooling swap as an end-to-end training arm.
  Ruled out twice at the production site: ro-dual-avg-max-pooling-downstream-20260805
  (delta +0.0090) and ro-avgmax-pooling-production-site-20260815 (gap +0.0235 v5c
  / +0.0447 v5d, both under the +0.05 bar, with the encoder-site +0.4042 shown to
  be a wrong-site measurement). rb-w4 is abandoned.'
- Do NOT re-measure a pooling/representation lever on the TEMPORAL ENCODER's patch
  tokens and then treat the number as a production effect. Production pools AFTER
  query_proj + 128-prototype cross-attention (d_model 192 -> d_llm 2560), which already
  recovers most of the excursion. Always measure at the site the change will actually
  run.
- Do NOT set patch_pool_mode in a profile YAML expecting it to take effect. It is
  not plumbed through configs/training.yaml, cli_train.py or pipeline.py, AND it is
  a bit-identical no-op in Phase A/B (projector built without sensor_slots). Phase
  C, the only phase where it acts, freezes the projector. Plumbing it is a prerequisite
  for any future pooling arm.
- Do NOT hand-roll a momentary-state label reader. metadata.observed(epoch) has no
  literal 'state' key; state is derived via state_at(). Use src.training.datasets.state_series
  or you will silently measure ZERO labels corpus-wide.
- Do NOT quote a bare AUC on the within-window state task. The shuffle null is NOT
  0.5 and RISES with feature dimensionality (8 feats -> 0.5189, 9216 feats -> 0.5495).
  exp-e28's 0.5237/0.5325 look like weak positives and are actually below their own
  nulls. Always pair the AUC with its own within-window permutation null.
external_run: ''
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_patch_geometry_preserves_state
  passed: true
- evidence: ''
  gate_id: g2_encoder_tokens_reproduce_e28_null
  passed: true
git_ref: ''
hypothesis_id: e29-state-geometry-innocent-weights-guilty-20260815
id: exp-e29-state-localisation-20260815
key_numbers:
  enc_token_auc: 0.5202
  enc_token_dim: 9216.0
  enc_token_null_mean: 0.5495
  enc_token_null_std: 0.0351
  enc_token_perm_p: 0.7917
  enc_token_z: -0.84
  n_epochs: 3226.0
  n_subjects: 87.0
  n_transition_windows_scored: 660.0
  pos_rate: 0.481
  raw_patch_grid_auc: 0.6309
  raw_patch_grid_dim: 8.0
  raw_patch_grid_null_mean: 0.5189
  raw_patch_grid_null_std: 0.0422
  raw_patch_grid_perm_p: 0.0
  raw_patch_grid_z: 2.66
  raw_shallow_auc: 0.643
  raw_shallow_dim: 15.0
  raw_shallow_null_mean: 0.5441
  raw_shallow_z: 4.69
  shallow_minus_patchgrid_gap: 0.0121
  supply_state_epochs_total: 77081.0
  supply_state_flow: 4731.0
  supply_state_neutral: 62053.0
  supply_state_stress: 10297.0
  supply_subjects: 281.0
  supply_transition_windows_corpus: 1274.0
observed_effects:
- delta: 0.1413
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_momentary_state_auc_over_null: false
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[e29-state-geometry-innocent-weights-guilty-20260815]].

## Measurements
- [[eiv_momentary_state_auc_over_null]]: before -0.0293 → after 0.112  (Δ +0.1413, up ✗ predicted none)

## Gates
- `g1_patch_geometry_preserves_state`: PASS
- `g2_encoder_tokens_reproduce_e28_null`: PASS

**Verdict:** supports

## Key numbers
- `enc_token_auc`: 0.5202
- `enc_token_dim`: 9216
- `enc_token_null_mean`: 0.5495
- `enc_token_null_std`: 0.0351
- `enc_token_perm_p`: 0.7917
- `enc_token_z`: -0.84
- `n_epochs`: 3226
- `n_subjects`: 87
- `n_transition_windows_scored`: 660
- `pos_rate`: 0.481
- `raw_patch_grid_auc`: 0.6309
- `raw_patch_grid_dim`: 8
- `raw_patch_grid_null_mean`: 0.5189
- `raw_patch_grid_null_std`: 0.0422
- `raw_patch_grid_perm_p`: 0
- `raw_patch_grid_z`: 2.66
- `raw_shallow_auc`: 0.643
- `raw_shallow_dim`: 15
- `raw_shallow_null_mean`: 0.5441
- `raw_shallow_z`: 4.69
- `shallow_minus_patchgrid_gap`: 0.0121
- `supply_state_epochs_total`: 77081
- `supply_state_flow`: 4731
- `supply_state_neutral`: 62053
- `supply_state_stress`: 10297
- `supply_subjects`: 281
- `supply_transition_windows_corpus`: 1274

## Artifacts
- `scratch/e29_state_localisation_and_supply.py`
- `logs/e29_state_localisation_and_supply.json`
- `commit b34ed44`

## Do not retry
- Do NOT re-run the avg->avgmax / max pooling swap as an end-to-end training arm. Ruled out twice at the production site: ro-dual-avg-max-pooling-downstream-20260805 (delta +0.0090) and ro-avgmax-pooling-production-site-20260815 (gap +0.0235 v5c / +0.0447 v5d, both under the +0.05 bar, with the encoder-site +0.4042 shown to be a wrong-site measurement). rb-w4 is abandoned.
- Do NOT re-measure a pooling/representation lever on the TEMPORAL ENCODER's patch tokens and then treat the number as a production effect. Production pools AFTER query_proj + 128-prototype cross-attention (d_model 192 -> d_llm 2560), which already recovers most of the excursion. Always measure at the site the change will actually run.
- Do NOT set patch_pool_mode in a profile YAML expecting it to take effect. It is not plumbed through configs/training.yaml, cli_train.py or pipeline.py, AND it is a bit-identical no-op in Phase A/B (projector built without sensor_slots). Phase C, the only phase where it acts, freezes the projector. Plumbing it is a prerequisite for any future pooling arm.
- Do NOT hand-roll a momentary-state label reader. metadata.observed(epoch) has no literal 'state' key; state is derived via state_at(). Use src.training.datasets.state_series or you will silently measure ZERO labels corpus-wide.
- Do NOT quote a bare AUC on the within-window state task. The shuffle null is NOT 0.5 and RISES with feature dimensionality (8 feats -> 0.5189, 9216 feats -> 0.5495). exp-e28's 0.5237/0.5325 look like weak positives and are actually below their own nulls. Always pair the AUC with its own within-window permutation null.

## Notes
Both preregistered gates pass and both predicted directions are confirmed.

WHAT THIS ESTABLISHES. E28 showed the trained encoder carries no state and refuted the triplet miner, but its null was AMBIGUOUS between two very different diagnoses: (a) the objective never asked for state, vs (b) this architecture/geometry cannot represent state. Under (b) the supervised head would be dead on arrival. E29 inserts the missing middle term and eliminates (b).

  arm              n_feats   AUC      own null         z       perm p
  raw_shallow      15        0.6430   0.5441+-0.0211   +4.69   0.0000
  raw_patch_grid    8        0.6309   0.5189+-0.0422   +2.66   0.0000
  enc_token      9216        0.5202   0.5495+-0.0351   -0.84   0.7917

Raw canonical HR read on the ENCODER'S OWN patch grid (patch_len=120, stride=60) retains essentially all the shallow signal -- 0.6309 vs 0.6430, a gap of 0.0121 -- from 8 features and ZERO learned parameters. The same geometry passed through the trained encoder collapses to 0.5202, below its own shuffle null. The information is present at the encoder's INPUT and absent at its OUTPUT. The patching is innocent; the InfoNCE-trained weights are guilty.

CONSEQUENCE FOR THE QUEUE: the supervised state head (E28's named residual) is JUSTIFIED. This is the opposite outcome to rb-w4, which was abandoned the same day when its lever was re-measured at the production site and shrank 9.1x -- and it is the same style of cheap CPU gate that produced both decisions. The gate was built to be able to kill this arm and did not.

SUPPLY IS NOT THE CONSTRAINT: 77081 state-labelled epochs over 281 subjects {neutral 62053, stress 10297, flow 4731} across 9 datasets (dreamt 53052, wearable_exam_stress 8099, lifesnaps 4418, dryad_ifh_affect 3926, nurse_stress 3028, wesad 1795, pmdata 1674, neuro_stress_resilience_hci 1067, consumer_grade_wearables 22). The real design issue for the head is CLASS IMBALANCE (neutral 80.5%), not label volume.

CAVEAT, do not overclaim: this shows the geometry CAN CARRY state under a RandomForest readout on 8 hand-built features. It does not show that a supervised head trained through the encoder will reach it, nor that the LM will consume it at generation -- the same probe-vs-generation distinction exp-e31 drew and rb-w4's own prompt warned about. Score the head on eiv_momentary_state_auc_over_null against this triple FIRST, and only then on the emission path.

METHOD TRAP: a hand-rolled reader looking for a literal 'state' key in metadata.observed(epoch) returned ZERO labels corpus-wide. Momentary state is derived through state_at(); src.training.datasets.state_series is the only correct accessor. Caught only because zero contradicted E26's known n=3226.

#n-of-1 #control #baseline #wearables #epoch-resolution #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
