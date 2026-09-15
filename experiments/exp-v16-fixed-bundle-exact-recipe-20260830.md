---
artifacts:
- outputs/adapters/v16fixed_c
- logs/v16_driver.log
- logs/v16_phase_c.log
- logs/emission_v16fixed_supa_summary.json
- logs/emission_v16fixed_dense_summary.json
- scratch/run_v16_fixed.sh
- data/eval_v3_1min/eval_manifest.json
closed: true
config_note: 'v16fixed: identical design to exp-v15 (byte-untouched lora_proxy_12ch_wl_band04,
  frozen v11full/phase_b encoder, C-only, seed 42) but on Anton''s FIXED bundle: commit
  ddb7498, tensor [14041,1440,12], 1620 subjects, 452,607 label-epochs, validate:
  no findings, normalization sha 24e731a63c97. Eval rebuilt pre-launch: eval_v3_1min
  sha 03d62723, 6918 rows / 519 subjects / 7 datasets (now incl. dapper 406). Suite
  972 passed on the new bundle. Text cache auto-warmed 7566->? pool rows (warm_if_needed).
  Runner scratch/run_v16_fixed.sh; prior arm artifacts (v15 on broken bundle) preserved
  for comparison.'
cost:
  gpu_hours: 1.0
  wall_hours: 0.98
crash_reason: ''
created_at: '2026-08-30T15:21:31+00:00'
diagnostics_measured:
- after: 10.058
  before: 14.268
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.231
  before: 0.355
  diagnostic_id: eiv_gen_peak_corr
- after: 24.519
  before: 24.814
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT expect label-side merger fixes to affect the C-stage collapse: ddb7498 (DAPPER
  anchor, dedup, fabricated-stress removal, WESAD/DREAMT fixes; validate clean; 2.8x
  label supply) left the identical healthy-recipe arm in the same collapse class (corr
  0.355->0.231, top1 0.805->0.810). The breaking property is corpus-side but NOT among
  these label defects.'
- Do NOT read v16's MAE 10.058 as 'improved from v15's 14.268' -- top1 0.81 both;
  the constant moved, the collapse did not.
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL: supabase hr_raw corr 0.231 vs the >= 0.60 bar, top1_mass 0.810
    (162/200 rows one value), distinct 8. Same collapse class as v15 on the broken
    bundle (0.355/0.805). The label-side merger fixes did NOT restore trainability.
    Suite 972 passed, RC=0, 0 tracebacks, pre-check confirmed the healthy recipe (6-ds/avg/default-prompt).'
  gate_id: g1-fixed-bundle-heals-training
  passed: false
- evidence: 'FAIL: supabase hr_raw MAE 10.058 vs trivial 3.954 -> skill -6.103 (v15
    was -10.313; the delta is where the constant sits, not health -- corr and top1
    say collapse). hr_peak MAE 30.774, skill -13.364.'
  gate_id: g2-beats-trivial
  passed: false
- evidence: 'Advisory FAIL: dense-128 hr_raw MAE 24.519 vs trivial 6.986 -> skill
    -17.5, corr 0.160, top1 0.305. Note the new-bundle dense trivial moved 7.774->6.986
    with the rebuilt eval draw.'
  gate_id: g3-dense-advisory
  passed: false
git_ref: ''
hypothesis_id: anton-s-merger-fixes-commit-ddb7498-dapper-day-d9fd67
id: exp-v16-fixed-bundle-exact-recipe-20260830
interpretation:
  at: '2026-08-30T16:19:27+00:00'
  failure_mode: claim_false
  lesson: 'The label-side merger defects were orthogonal to the C-stage collapse:
    on the ddb7498 bundle (validate: zero findings, DAPPER anchored, dedup done, fabricated
    stress gone) the byte-identical healthy recipe still collapses in the same class
    (corr 0.231, top1 0.810). Eleven of eleven fresh post-rebuild C arms are now collapsed.
    The breaking corpus property is one that (a) every post-rebuild bundle shares,
    (b) the old healthy corpus lacked, and (c) the validate suite does not measure
    -- which points at the TENSOR/PREFIX path: population_stats z-space, added-cohort
    prefix distributions, fill/coverage statistics over the C draw, or the standardised-on-disk
    representation. Next probe is free: diff C-pool prefix statistics between data/canonical_1min_conv
    (the old bundle the healthy json_s42 control trained on, still on disk) and the
    current bundle.'
  transfers_to:
  - anton-s-merger-fixes-commit-ddb7498-dapper-day-d9fd67
  - the-c-stage-collapse-is-caused-by-the-recipe-01862e
key_numbers:
  dense_corr: 0.16
  dense_mae: 24.519
  dense_trivial_new: 6.986
  supa_corr: 0.231
  supa_mae: 10.058
  supa_skill: -6.103
  supa_top1: 0.81
  train_final_loss: 1.305
observed_effects:
- delta: -4.21
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: -0.124
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: -0.295
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
parent_experiment_id: exp-v15-exact-healthy-recipe-20260830
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[anton-s-merger-fixes-commit-ddb7498-dapper-day-d9fd67]].
Branches from [[exp-v15-exact-healthy-recipe-20260830]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 14.268 → after 10.058  (Δ -4.21, down)
- [[eiv_gen_peak_corr]]: before 0.355 → after 0.231  (Δ -0.124, down ✗ predicted up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 24.814 → after 24.519  (Δ -0.295, down)

## Gates
- `g1-fixed-bundle-heals-training`: **FAIL** — FAIL: supabase hr_raw corr 0.231 vs the >= 0.60 bar, top1_mass 0.810 (162/200 rows one value), distinct 8. Same collapse class as v15 on the broken bundle (0.355/0.805). The label-side merger fixes did NOT restore trainability. Suite 972 passed, RC=0, 0 tracebacks, pre-check confirmed the healthy recipe (6-ds/avg/default-prompt).
- `g2-beats-trivial`: **FAIL** — FAIL: supabase hr_raw MAE 10.058 vs trivial 3.954 -> skill -6.103 (v15 was -10.313; the delta is where the constant sits, not health -- corr and top1 say collapse). hr_peak MAE 30.774, skill -13.364.
- `g3-dense-advisory`: **FAIL** — Advisory FAIL: dense-128 hr_raw MAE 24.519 vs trivial 6.986 -> skill -17.5, corr 0.160, top1 0.305. Note the new-bundle dense trivial moved 7.774->6.986 with the rebuilt eval draw.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The label-side merger defects were orthogonal to the C-stage collapse: on the ddb7498 bundle (validate: zero findings, DAPPER anchored, dedup done, fabricated stress gone) the byte-identical healthy recipe still collapses in the same class (corr 0.231, top1 0.810). Eleven of eleven fresh post-rebuild C arms are now collapsed. The breaking corpus property is one that (a) every post-rebuild bundle shares, (b) the old healthy corpus lacked, and (c) the validate suite does not measure -- which points at the TENSOR/PREFIX path: population_stats z-space, added-cohort prefix distributions, fill/coverage statistics over the C draw, or the standardised-on-disk representation. Next probe is free: diff C-pool prefix statistics between data/canonical_1min_conv (the old bundle the healthy json_s42 control trained on, still on disk) and the current bundle.

Transfers to: [[anton-s-merger-fixes-commit-ddb7498-dapper-day-d9fd67]], [[the-c-stage-collapse-is-caused-by-the-recipe-01862e]]

## Key numbers
- `dense_corr`: 0.16
- `dense_mae`: 24.519
- `dense_trivial_new`: 6.986
- `supa_corr`: 0.231
- `supa_mae`: 10.058
- `supa_skill`: -6.103
- `supa_top1`: 0.81
- `train_final_loss`: 1.305

## Artifacts
- `outputs/adapters/v16fixed_c`
- `logs/v16_driver.log`
- `logs/v16_phase_c.log`
- `logs/emission_v16fixed_supa_summary.json`
- `logs/emission_v16fixed_dense_summary.json`
- `scratch/run_v16_fixed.sh`
- `data/eval_v3_1min/eval_manifest.json`

## Do not retry
- Do NOT expect label-side merger fixes to affect the C-stage collapse: ddb7498 (DAPPER anchor, dedup, fabricated-stress removal, WESAD/DREAMT fixes; validate clean; 2.8x label supply) left the identical healthy-recipe arm in the same collapse class (corr 0.355->0.231, top1 0.805->0.810). The breaking property is corpus-side but NOT among these label defects.
- Do NOT read v16's MAE 10.058 as 'improved from v15's 14.268' -- top1 0.81 both; the constant moved, the collapse did not.

## Notes
ELEVENTH consecutive fresh-C collapse, now on a bundle that passes validate with zero findings. This split the corpus-side hypothesis space in half: the cause is NOT the label-side defect classes ddb7498 fixed (they were real, but orthogonal to the collapse), and NOT anything the validate suite measures. What remains corpus-side, shared by EVERY post-rebuild bundle and ABSENT from the old healthy corpus (~[10344] / data/canonical_1min_conv): the tensor/prefix path -- population_stats z-space evolution, the added-cohort prefix distributions, fill/coverage statistics over the C training draw, or the standardised-on-disk representation itself. Next (free, CPU): diff the C-pool prefix statistics old-healthy-bundle vs current bundle -- per-channel z-value distributions, fill fraction, per-dataset value ranges -- data/canonical_1min_conv (5955 rows, the bundle the healthy json_s42 control trained on) is still on disk. Wall 59 min.

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
