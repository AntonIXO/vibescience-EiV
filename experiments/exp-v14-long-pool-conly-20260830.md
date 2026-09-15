---
artifacts:
- outputs/adapters/v14long_c
- logs/v14_driver.log
- logs/v14_phase_c.log
- logs/emission_v14long_supa_summary.json
- logs/emission_v14long_dense_summary.json
- scratch/run_v14_long.sh
- scratch/_verify_v14long_profile.py
closed: true
config_note: 'v14long: lora_proxy_12ch_v14long = v13flat with dataset_filter cut 20->7
  long cohorts (HRmin p50>240: lifesnaps, pmdata, optihealth_supabase, big_ideas_glycemic,
  dreamt, mmash, dapper), all weights 1.0. Verified by dict diff: only dataset_filter+notes
  differ. Same frozen encoder outputs/checkpoints/v11full/phase_b, band04 law, masked_avg,
  fit256 prompt, seed 43, 2600 steps. Runner scratch/run_v14_long.sh; scoring supabase
  n=200 batch1 rng0 + dense-128.'
cost:
  gpu_hours: 1.0
  wall_hours: 0.97
crash_reason: ''
created_at: '2026-08-30T11:24:03+00:00'
diagnostics_measured:
- after: 4.954
  before: 4.658
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.116
  before: 0.13
  diagnostic_id: eiv_gen_peak_corr
- after: 22.161
  before: 24.611
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT re-run pool-composition variants at Phase C against this collapse (roster
  cuts, HR-coverage gates on the C pool, min_hr_coverage in InstructionDataset as
  a collapse FIX): a pool nearly identical to the healthy band04 one (its 6 cohorts
  + dapper, HRmin p05 jumps 29->~330-class) left all three gates failed (corr 0.116,
  skill -0.999, dense 22.2). Pool composition is eliminated WHOLESALE on top of sampler
  weights.'
- Do NOT read the runner's 'g3 roster assertion' from cloned run_v1X scripts without
  updating its expected-dataset count -- v14's inherited check asserted 20 datasets
  against a deliberately-7-dataset arm and printed a scary Traceback that was a stale
  check, not an arm defect.
external_run: ''
former_ids: []
gate_results:
- evidence: 'supabase hr_raw corr = 0.116 vs required >= 0.60 (v13flat read 0.130
    -- the cut moved corr by -0.014, nothing). top1_mass 0.730, distinct 7: still
    a near-constant emitter. Healthy reference on this bundle: +0.760.'
  gate_id: g1-long-pool-restores-correlation
  passed: false
- evidence: 'supabase hr_raw MAE 4.954 vs trivial 3.954 -> skill -0.999. WORSE than
    v13flat (4.658) and v12conly (4.384). Healthy: 2.692 (+1.262).'
  gate_id: g2-beats-trivial
  passed: false
- evidence: dense-128 hr_raw MAE 22.161 vs required < 8.0 (trivial 7.774). Moved 2.45
    of the ~14 bpm deficit -- the same ~20+ regime as v11full (21.277) and v12conly
    (21.585). corr 0.359 is the highest dense corr of any collapsed arm but skill
    remains -14.4. Pre-check confirmed the arm really trained on 7 datasets / 8,479
    rows / masked_avg / fit256 / seed 43 (the runner's inherited 'expected 20 datasets'
    assertion is a stale check from the v13 clone, not an arm defect; scoring proceeded
    on the correct adapter).
  gate_id: g3-dense-recovers
  passed: false
git_ref: ''
hypothesis_id: the-c-stage-collapse-is-caused-by-short-window-5859e2
id: exp-v14-long-pool-conly-20260830
interpretation:
  at: '2026-08-30T12:25:34+00:00'
  failure_mode: claim_false
  lesson: 'Pool composition is eliminated WHOLESALE: a C pool nearly identical to
    the healthy band04 one (its 6 cohorts + dapper, HRmin p05 back in the 330-class)
    still collapses (corr 0.116, top1 0.73, dense 22.2 vs trivial 7.8). FIVE levers
    are now dead: encoder, roster reach, frozen-encoder, sampler weights, pool admission.
    Sharper still, the vault''s own record weakens the remaining ''recipe knobs''
    fork: masked_avg pooling was HEALTHY on the old corpus (w6msk s43/44/45 corr 0.741-0.745
    supabase) and the fit256 prompt was HEALTHY on the old corpus (jsonfit_s42 corr
    0.809, skill +1.109). Every knob of the collapsed recipe has an individually-healthy
    precedent -- ON THE OLD CORPUS. What no healthy C arm has ever touched is the
    NEW bundle as C-stage TRAINING data (the healthy ab_ctrl reads the new bundle
    fine at INFERENCE). Next probe is free (CPU): draw training pairs from the v14long
    InstructionDataset with hr_augment as configured and verify the target JSON''s
    hr_raw/hr_peak against the (augmented) prefix tensor denormalised with the bundle''s
    own stats -- if targets are decoupled from the prefix (e.g. augment shifting in
    a stale z-space), constant emission near the augment floor (~45-56 bpm; the observed
    constant is 55.9) is exactly what CLM would learn.'
  transfers_to:
  - the-c-stage-collapse-is-caused-by-short-window-5859e2
  - the-c-stage-collapse-is-caused-by-upsampling-186858
key_numbers:
  dense_corr: 0.359
  dense_mae: 22.161
  dense_top1: 0.344
  n_train_rows: 8479.0
  supa_corr: 0.116
  supa_mae: 4.954
  supa_skill: -0.999
  supa_top1: 0.73
  train_final_loss: 0.459
observed_effects:
- delta: 0.296
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
- delta: -0.014
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: -2.45
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
parent_experiment_id: exp-v13-flat-weights-conly-20260830
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-c-stage-collapse-is-caused-by-short-window-5859e2]].
Branches from [[exp-v13-flat-weights-conly-20260830]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 4.658 → after 4.954  (Δ +0.296, up)
- [[eiv_gen_peak_corr]]: before 0.13 → after 0.116  (Δ -0.014, down ✗ predicted up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 24.611 → after 22.161  (Δ -2.45, down)

## Gates
- `g1-long-pool-restores-correlation`: **FAIL** — supabase hr_raw corr = 0.116 vs required >= 0.60 (v13flat read 0.130 -- the cut moved corr by -0.014, nothing). top1_mass 0.730, distinct 7: still a near-constant emitter. Healthy reference on this bundle: +0.760.
- `g2-beats-trivial`: **FAIL** — supabase hr_raw MAE 4.954 vs trivial 3.954 -> skill -0.999. WORSE than v13flat (4.658) and v12conly (4.384). Healthy: 2.692 (+1.262).
- `g3-dense-recovers`: **FAIL** — dense-128 hr_raw MAE 22.161 vs required < 8.0 (trivial 7.774). Moved 2.45 of the ~14 bpm deficit -- the same ~20+ regime as v11full (21.277) and v12conly (21.585). corr 0.359 is the highest dense corr of any collapsed arm but skill remains -14.4. Pre-check confirmed the arm really trained on 7 datasets / 8,479 rows / masked_avg / fit256 / seed 43 (the runner's inherited 'expected 20 datasets' assertion is a stale check from the v13 clone, not an arm defect; scoring proceeded on the correct adapter).

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

Pool composition is eliminated WHOLESALE: a C pool nearly identical to the healthy band04 one (its 6 cohorts + dapper, HRmin p05 back in the 330-class) still collapses (corr 0.116, top1 0.73, dense 22.2 vs trivial 7.8). FIVE levers are now dead: encoder, roster reach, frozen-encoder, sampler weights, pool admission. Sharper still, the vault's own record weakens the remaining 'recipe knobs' fork: masked_avg pooling was HEALTHY on the old corpus (w6msk s43/44/45 corr 0.741-0.745 supabase) and the fit256 prompt was HEALTHY on the old corpus (jsonfit_s42 corr 0.809, skill +1.109). Every knob of the collapsed recipe has an individually-healthy precedent -- ON THE OLD CORPUS. What no healthy C arm has ever touched is the NEW bundle as C-stage TRAINING data (the healthy ab_ctrl reads the new bundle fine at INFERENCE). Next probe is free (CPU): draw training pairs from the v14long InstructionDataset with hr_augment as configured and verify the target JSON's hr_raw/hr_peak against the (augmented) prefix tensor denormalised with the bundle's own stats -- if targets are decoupled from the prefix (e.g. augment shifting in a stale z-space), constant emission near the augment floor (~45-56 bpm; the observed constant is 55.9) is exactly what CLM would learn.

Transfers to: [[the-c-stage-collapse-is-caused-by-short-window-5859e2]], [[the-c-stage-collapse-is-caused-by-upsampling-186858]]

## Key numbers
- `dense_corr`: 0.359
- `dense_mae`: 22.161
- `dense_top1`: 0.344
- `n_train_rows`: 8479
- `supa_corr`: 0.116
- `supa_mae`: 4.954
- `supa_skill`: -0.999
- `supa_top1`: 0.73
- `train_final_loss`: 0.459

## Artifacts
- `outputs/adapters/v14long_c`
- `logs/v14_driver.log`
- `logs/v14_phase_c.log`
- `logs/emission_v14long_supa_summary.json`
- `logs/emission_v14long_dense_summary.json`
- `scratch/run_v14_long.sh`
- `scratch/_verify_v14long_profile.py`

## Do not retry
- Do NOT re-run pool-composition variants at Phase C against this collapse (roster cuts, HR-coverage gates on the C pool, min_hr_coverage in InstructionDataset as a collapse FIX): a pool nearly identical to the healthy band04 one (its 6 cohorts + dapper, HRmin p05 jumps 29->~330-class) left all three gates failed (corr 0.116, skill -0.999, dense 22.2). Pool composition is eliminated WHOLESALE on top of sampler weights.
- Do NOT read the runner's 'g3 roster assertion' from cloned run_v1X scripts without updating its expected-dataset count -- v14's inherited check asserted 20 datasets against a deliberately-7-dataset arm and printed a scary Traceback that was a stale check, not an arm defect.

## Notes
FIFTH refutation on this problem, and it exhausts the pool axis: the preregistered g1-FAIL fork now applies -- the only remaining deltas between every collapsed arm (v11full/v12conly/v13flat/v14long) and every healthy arm (v9band04/v10conly/ab_ctrl) are the C-recipe knobs: patch_pool_mode masked_avg (every collapsed arm has it; every healthy arm ran default avg), prompt_path fit256, seed 43-vs-42. patch_pool_mode goes first per the fork: W10 already documented a masked-pooling emission anomaly on supabase (h-w10 'instrument defect' hypothesis REFUTED -- the null was NOT an instrument artifact). Wall 58 min (14:22:45-15:20:23).

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
