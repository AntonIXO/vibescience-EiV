---
artifacts:
- logs/g5_heldout_lifesnaps.json
- logs/g5_clean_lifesnaps.json
- logs/g5_clean_wesad.json
- logs/run_f_n2_cpu.log
- logs/run_f_n2_cpu.attempt1.log
- scratch/g5_pool_ceiling.py
- scratch/run_f_n2_cpu.sh
closed: true
config_note: 'scratch/run_f_n2_cpu.sh: g5_pool_ceiling.py --datasets lifesnaps on
  the heldout (442 rows) and clean pools, --datasets wesad on the clean pool, --fold-seeds
  5, all arms, CPU only (CUDA_VISIBLE_DEVICES=""). Rows built byte-identically to
  exp-c1 then filtered by dataset; nothing tuned.'
cost:
  gpu_hours: 0.0
  wall_hours: 5.5
crash_reason: ''
created_at: '2026-09-03T06:48:09+00:00'
diagnostics_measured:
- after: 0.0452
  before: 0.0
  diagnostic_id: eiv_state_auc_over_null_freeliving
- after: 0.4741
  before: 0.0
  diagnostic_id: eiv_state_auc_over_null_lab
do_not_retry:
- Do not gate anything on a pooled state AUC again; lab and free-living are separate
  diagnostics now.
- 'Do not read the 9-subject held-out LifeSnaps cell as ''free-living is at chance'':
  the 49-subject clean cell reads +0.045 at p=0.000. It is supply-limited at the feature
  level, not absent.'
- 'Do not run another Phase A objective arm hoping to lift the WESAD encoder cell:
  handcrafted features read 0.976 there and the trained encoder 0.63; the gap is the
  representation family, not the loss.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'raw_multiscale/rf on LifeSnaps-only rows: held-out pool (442 rows, 9
    subjects) over_null -0.0035, perm_p 0.50, fold sd 0.009 -- inside the 0.031 mde,
    a powered null at that n; clean pool (1,388 rows, 49 subjects, GroupKFold so still
    subject-held-out for a feature readout) +0.0452, perm_p 0.000 (0/48 nulls above
    it), fold sd 0.013 -- above the mde, below the +0.05 bar. Neither pool clears
    +0.05. Reading: free-living state from HR/HRV features is weakly real (AUC 0.54)
    once there are ~50 subjects to fit across, and invisible at 9.'
  gate_id: g1
  passed: false
- evidence: 'Clean LifeSnaps pool, arms >= +0.05 with perm_p <= 0.05: enc_ms/gb +0.0615,
    enc_v41/gb +0.0565, enc_v41/ridge +0.0559, union/ridge +0.0560, union_ms/gb +0.0504
    -- AND enc_ms@random/ridge +0.0646, enc_v41@random/ridge +0.0566: the random-init
    twin matches or beats the trained encoder under ridge (gb: trained +0.0565/+0.0615
    vs twin +0.0083/+0.0309). Held-out LifeSnaps: only enc_ms/ridge +0.0548 (p 0.01)
    clears, its gb sibling reads -0.0298 -- single-family, 9 subjects, not evidence.
    So what sees free-living state above the handcrafted +0.045 is the multi-scale
    POOLING GEOMETRY (the twin does it), not Phase A training; and the clean pool
    contains the encoder''s own training subjects, so the trained-vs-twin gb gap (+0.03..+0.05)
    is an in-sample number.'
  gate_id: g2
  passed: true
- evidence: 'WESAD-only (449 rows, 15 subjects): raw_multiscale/rf +0.4741 (AUC 0.976,
    z +13.95, fold sd 0.005) >= +0.35; raw_multiscale/gb +0.4786 (0.984). The lab
    cell reproduces when fit alone; the device split in exp-c1 was not a mixture-fit
    artefact. Also measured here: the trained encoder reads only +0.1224 (gb) / +0.0573
    (ridge) on the same rows, twin +0.0939 / +0.0900 -- a quarter of the handcrafted
    signal on the one cohort where state is nearly solved, and union_ms/gb +0.4803
    vs union_ms@random/gb +0.4765: adding the trained encoder to the features buys
    +0.004 over adding a random one.'
  gate_id: g3
  passed: true
- evidence: 'Rows built by the g5 harness byte-identically to exp-c1 (the dump arm
    reproduced union_ms/gb 0.6398 / +0.1117 exactly), then filtered by --datasets;
    within-window permutation null per arm; --fold-seeds 5 sd reported per cell. One
    code change, not a tuning: HistGradientBoosting''s binner raised on a fold where
    a LifeSnaps RMSSD column had < 2 distinct finite values, so the gb path now drops
    such columns per fold (attempt 1 crashed there; logs/run_f_n2_cpu.attempt1.log).'
  gate_id: g4
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: f-freeliving-state-from-multiscale-hrv-features-is-at-chance-on-lifesnaps-alone
id: exp-f-freeliving-feature-ceiling-20260903
interpretation:
  at: '2026-09-03T12:55:58+00:00'
  failure_mode: claim_false
  lesson: 'The prediction ''free-living state is at chance at the feature level''
    is wrong in the direction that matters: on 49 LifeSnaps subjects the 15 multi-scale
    HR/HRV features read AUC 0.54, +0.045 over null with 0/48 permutations above it,
    and the 9-subject held-out cell (-0.004) is a supply artefact, not a ceiling.
    Free-living momentary state from wrist HR/HRV is weak but real, and it is SUBJECT-supply-limited
    -- so the lever is more free-living subjects, not a representation. The same run
    kills the representation lever from the other side: on WESAD the handcrafted features
    read 0.976 while the trained PL60 encoder reads 0.63 (twin 0.59), and adding the
    trained encoder to the features buys +0.004 over adding a random one; on clean
    LifeSnaps the random twin matches the trained encoder under ridge. Lab and free-living
    are two diagnostics from here on, and the free-living one is the product''s real
    gap.'
  transfers_to:
  - c1-multiscale-union-readout-holds-on-the-splithonest-heldout-pool
  - h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale
  - e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling
key_numbers:
  fold_sd_clean: 0.0133
  lifesnaps_clean_enc_gb_over_null: 0.0565
  lifesnaps_clean_enc_twin_ridge_over_null: 0.0566
  lifesnaps_clean_n_subjects: 49.0
  lifesnaps_clean_perm_p: 0.0
  lifesnaps_clean_rawms_rf_auc: 0.5395
  lifesnaps_clean_rawms_rf_over_null: 0.0452
  lifesnaps_heldout_n_subjects: 9.0
  lifesnaps_heldout_rawms_rf_over_null: -0.0035
  mde: 0.031
  wesad_enc_gb_over_null: 0.1224
  wesad_enc_twin_gb_over_null: 0.0939
  wesad_rawms_rf_auc: 0.9759
  wesad_rawms_rf_over_null: 0.4741
  wesad_union_ms_gb: 0.4803
  wesad_union_ms_twin_gb: 0.4765
observed_effects:
- delta: 0.0452
  diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: up
- delta: 0.4741
  diagnostic_id: eiv_state_auc_over_null_lab
  direction: up
parent_experiment_id: exp-c1-heldout-pool-ceiling-20260903
prediction_match:
  overall: true
  per_diagnostic:
    eiv_state_auc_over_null_freeliving: true
    eiv_state_auc_over_null_lab: true
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: directional_only
---

## Summary
Tests hypothesis [[f-freeliving-state-from-multiscale-hrv-features-is-at-chance-on-lifesnaps-alone]].
Branches from [[exp-c1-heldout-pool-ceiling-20260903]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.

## Measurements
- [[eiv_state_auc_over_null_freeliving]]: before 0.0 → after 0.0452  (Δ +0.0452, up ✓ predicted up)
- [[eiv_state_auc_over_null_lab]]: before 0.0 → after 0.4741  (Δ +0.4741, up ✓ predicted up)

## Gates
- `g1`: **FAIL** — raw_multiscale/rf on LifeSnaps-only rows: held-out pool (442 rows, 9 subjects) over_null -0.0035, perm_p 0.50, fold sd 0.009 -- inside the 0.031 mde, a powered null at that n; clean pool (1,388 rows, 49 subjects, GroupKFold so still subject-held-out for a feature readout) +0.0452, perm_p 0.000 (0/48 nulls above it), fold sd 0.013 -- above the mde, below the +0.05 bar. Neither pool clears +0.05. Reading: free-living state from HR/HRV features is weakly real (AUC 0.54) once there are ~50 subjects to fit across, and invisible at 9.
- `g2`: PASS — Clean LifeSnaps pool, arms >= +0.05 with perm_p <= 0.05: enc_ms/gb +0.0615, enc_v41/gb +0.0565, enc_v41/ridge +0.0559, union/ridge +0.0560, union_ms/gb +0.0504 -- AND enc_ms@random/ridge +0.0646, enc_v41@random/ridge +0.0566: the random-init twin matches or beats the trained encoder under ridge (gb: trained +0.0565/+0.0615 vs twin +0.0083/+0.0309). Held-out LifeSnaps: only enc_ms/ridge +0.0548 (p 0.01) clears, its gb sibling reads -0.0298 -- single-family, 9 subjects, not evidence. So what sees free-living state above the handcrafted +0.045 is the multi-scale POOLING GEOMETRY (the twin does it), not Phase A training; and the clean pool contains the encoder's own training subjects, so the trained-vs-twin gb gap (+0.03..+0.05) is an in-sample number.
- `g3`: PASS — WESAD-only (449 rows, 15 subjects): raw_multiscale/rf +0.4741 (AUC 0.976, z +13.95, fold sd 0.005) >= +0.35; raw_multiscale/gb +0.4786 (0.984). The lab cell reproduces when fit alone; the device split in exp-c1 was not a mixture-fit artefact. Also measured here: the trained encoder reads only +0.1224 (gb) / +0.0573 (ridge) on the same rows, twin +0.0939 / +0.0900 -- a quarter of the handcrafted signal on the one cohort where state is nearly solved, and union_ms/gb +0.4803 vs union_ms@random/gb +0.4765: adding the trained encoder to the features buys +0.004 over adding a random one.
- `g4`: PASS — Rows built by the g5 harness byte-identically to exp-c1 (the dump arm reproduced union_ms/gb 0.6398 / +0.1117 exactly), then filtered by --datasets; within-window permutation null per arm; --fold-seeds 5 sd reported per cell. One code change, not a tuning: HistGradientBoosting's binner raised on a fold where a LifeSnaps RMSSD column had < 2 distinct finite values, so the gb path now drops such columns per fold (attempt 1 crashed there; logs/run_f_n2_cpu.attempt1.log).

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `claim_false`

The prediction 'free-living state is at chance at the feature level' is wrong in the direction that matters: on 49 LifeSnaps subjects the 15 multi-scale HR/HRV features read AUC 0.54, +0.045 over null with 0/48 permutations above it, and the 9-subject held-out cell (-0.004) is a supply artefact, not a ceiling. Free-living momentary state from wrist HR/HRV is weak but real, and it is SUBJECT-supply-limited -- so the lever is more free-living subjects, not a representation. The same run kills the representation lever from the other side: on WESAD the handcrafted features read 0.976 while the trained PL60 encoder reads 0.63 (twin 0.59), and adding the trained encoder to the features buys +0.004 over adding a random one; on clean LifeSnaps the random twin matches the trained encoder under ridge. Lab and free-living are two diagnostics from here on, and the free-living one is the product's real gap.

Transfers to: [[c1-multiscale-union-readout-holds-on-the-splithonest-heldout-pool]], [[h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale]], [[e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling]]

## Key numbers
- `fold_sd_clean`: 0.0133
- `lifesnaps_clean_enc_gb_over_null`: 0.0565
- `lifesnaps_clean_enc_twin_ridge_over_null`: 0.0566
- `lifesnaps_clean_n_subjects`: 49
- `lifesnaps_clean_perm_p`: 0
- `lifesnaps_clean_rawms_rf_auc`: 0.5395
- `lifesnaps_clean_rawms_rf_over_null`: 0.0452
- `lifesnaps_heldout_n_subjects`: 9
- `lifesnaps_heldout_rawms_rf_over_null`: -0.0035
- `mde`: 0.031
- `wesad_enc_gb_over_null`: 0.1224
- `wesad_enc_twin_gb_over_null`: 0.0939
- `wesad_rawms_rf_auc`: 0.9759
- `wesad_rawms_rf_over_null`: 0.4741
- `wesad_union_ms_gb`: 0.4803
- `wesad_union_ms_twin_gb`: 0.4765

## Artifacts
- `logs/g5_heldout_lifesnaps.json`
- `logs/g5_clean_lifesnaps.json`
- `logs/g5_clean_wesad.json`
- `logs/run_f_n2_cpu.log`
- `logs/run_f_n2_cpu.attempt1.log`
- `scratch/g5_pool_ceiling.py`
- `scratch/run_f_n2_cpu.sh`

## Do not retry
- Do not gate anything on a pooled state AUC again; lab and free-living are separate diagnostics now.
- Do not read the 9-subject held-out LifeSnaps cell as 'free-living is at chance': the 49-subject clean cell reads +0.045 at p=0.000. It is supply-limited at the feature level, not absent.
- Do not run another Phase A objective arm hoping to lift the WESAD encoder cell: handcrafted features read 0.976 there and the trained encoder 0.63; the gap is the representation family, not the loss.

## Notes
CPU only, run concurrently with the N2/N3 GPU inference (which slowed both). The clean LifeSnaps pool is subject-held-out for the FEATURE arms (GroupKFold) but contains the encoder's Phase A training subjects, so encoder cells there are in-sample; the held-out pool is honest for every arm but has only 9 LifeSnaps subjects.

#optihealth #readout #control #baseline #optihealth-eiv #representation-learning #matched-baseline
