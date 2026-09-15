---
artifacts:
- logs/v30_geometry.json
- logs/v30_state_w30_eval.json
- logs/v30_phase_a_report.json
- logs/v30_phase_b_report.json
- logs/v30_phase_c_report.json
- logs/emission_v30state_supa_summary.json
- logs/emission_v30state_dense_summary.json
- logs/v30_driver.log
- outputs/adapters/v30state_c/manifest.json
closed: true
config_note: 'v30state: A = v20strat + affect 0.1 + state_patch_weight 0.1 + statemask
  -> geometry gate -> state scoring (ridge W30/max, v30-A vs v28-A comparator, same
  rows/nulls, scratch/score_state_v30.py) -> B@30 -> band04 C -> deployment scoring.
  Queued behind v29 (runner waits). Runner scratch/run_v30_state.sh.'
cost:
  gpu_hours: 1.54
  wall_hours: 1.54
crash_reason: ''
created_at: '2026-08-31T06:41:09+00:00'
diagnostics_measured:
- after: 7.403
  before: 8.599
  diagnostic_id: eiv_effective_rank
- after: 0.0257
  before: 0.0204
  diagnostic_id: eiv_momentary_state_auc_over_null
- after: 5.6661
  before: 3.0297
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT re-run state_patch_weight 0.1 on a strat-A/affect-0.1 substrate hoping for
  state gain: on a geometry-healthy encoder (eff_rank 7.403) the ridge W30/max over_null
  moved only +0.0053 (0.0257 vs v28''s 0.0204), a quarter of the preregistered mde
  0.02 and half the historical rank-collapsed ceiling +0.037. The ''state detection
  was substrate-limited'' explanation is now measured and dead — healthy geometry
  does NOT unlock momentary state at W=30.'
- 'Do NOT add state_patch_weight to any arm intended for deployment at this dose:
  it cost 2.636 bpm supabase MAE (5.666 vs v28 3.030) and 12.2 bpm dense MAE (15.085
  vs 2.851) with std_ratio 2.71/2.46 vs v28''s 0.93/1.11 — an emission-calibration
  blowout, not a seed draw.'
- Do NOT price the v30 vs v28 MAE gap as noise. 2.636 bpm is ~13.5x the paired arm-vs-arm
  sigma 0.195; it is a demonstrated regression.
external_run: ''
former_ids: []
gate_results:
- evidence: 'logs/v30_geometry.json: eff_rank 7.403 (>= 7.0), cos_p50 0.144, cos_p95
    0.645. v28-A comparator 8.599. Runner did not abort; chain proceeded to state
    scoring + B + C.'
  gate_id: g0-geometry-holds
  passed: true
- evidence: 'logs/v30_state_w30_eval.json: v30state ridge W30/max AUC 0.5939, null_mean
    0.5682, over_null +0.02567, z 2.15, perm_p 0.0 (100 perms), n=2136, 113 subjects,
    nurse_stress excluded. Bar was over_null > 0.05 (mde 0.02) -> FAIL. v28affect01
    comparator on identical rows/nulls: over_null +0.02042, z 1.60, perm_p 0.08. Delta
    v30-v28 = +0.0053, i.e. a quarter of the preregistered mde 0.02 -> powered null
    on the lever. Head probe went the other way on both arms (v30 -0.0195, v28 -0.0265).'
  gate_id: g1-state-beats-historical-ceiling
  passed: false
- evidence: 'logs/emission_v30state_supa_summary.json (n=200, parse_ok 200): hr_raw
    MAE 5.666 -> outside v28 band [2.23, 3.83]; corr 0.777 (>= 0.60 OK), top1_mass
    0.57 (v28 0.47), std_ratio 2.71 (v28 0.93), skill -1.712 vs trivial 3.954. Dense
    no-harm logs/emission_v30state_dense_summary.json (n=128, parse_ok 125): hr_raw
    MAE 15.085, corr 0.660, top1 0.273, skill -8.099 vs v28 dense MAE 2.851 / corr
    0.937. MAE gap vs v28 supabase = 2.636 bpm, far above the paired arm-vs-arm sigma
    0.195 and above the mde 0.8 -> a demonstrated regression, not noise.'
  gate_id: g2-hr-no-harm
  passed: false
git_ref: ''
hypothesis_id: state-detection-was-substrate-limited-on-the-46fa39
id: exp-v30-state-healthy-substrate-20260831
interpretation:
  at: '2026-08-31T10:34:35+00:00'
  failure_mode: claim_false
  lesson: 'The ''state detection was substrate-limited by rank collapse'' story is
    now dead by measurement. On a geometry-healthy encoder (eff_rank 7.403, logs/v30_geometry.json)
    with the patch-level soft-state supervision W8/E29 asked for, momentary-state
    ridge W30/max over_null reached only +0.0257 (z 2.15) vs the no-state comparator''s
    +0.0204 (z 1.60) on identical rows/nulls — a +0.0053 gain, a quarter of the preregistered
    mde 0.02 and still below the historical rank-collapsed ceiling +0.037. Every future
    state arm must therefore treat the W=30 wrist state signal as information-limited,
    not substrate-limited: the next honest move is changing the LABEL GEOMETRY (window/pooling/label
    construction, per exp-053''s peri-EMA problem) rather than adding another encoder-side
    supervision term. Second, independent lesson: state_patch_weight 0.1 is deployment-toxic
    — it left corr intact (0.777 supa / 0.660 dense) but blew the emission SCALE (std_ratio
    2.71/2.46 vs v28''s 0.93/1.11, top1_mass 0.57 vs 0.47), costing 2.636 bpm supabase
    and 12.2 bpm dense MAE. An auxiliary head can preserve HR ordering and still destroy
    HR calibration; corr alone would have hidden this.'
  transfers_to:
  - the-window-level-ce-state-term-trains-on-a35561
  - h-w8-cardinality-invariant-attention-pooling-f01250
  - e29-state-geometry-innocent-weights-guilty-20260815
  - w-sweep-rb-window-size-x-pooling-state-sweep-73469a
key_numbers:
  dense_corr_v30: 0.66
  dense_mae_v28: 2.851
  dense_mae_v30: 15.085
  eff_rank: 7.403
  g1_bar: 0.05
  g1_mde: 0.02
  paired_arm_sigma_mae: 0.195
  phase_c_final_loss: 1.3018
  ridge_auc_v30: 0.5939
  ridge_over_null_delta: 0.0053
  ridge_over_null_v28: 0.02042
  ridge_over_null_v30: 0.02567
  ridge_z_v30: 2.153
  seed_band_mae: 3.19
  supa_corr_v30: 0.777
  supa_mae_delta: 2.636
  supa_mae_v28: 3.03
  supa_mae_v30: 5.666
  supa_std_ratio_v30: 2.708
  supa_top1_v30: 0.57
observed_effects:
- delta: -1.196
  diagnostic_id: eiv_effective_rank
  direction: down
- delta: 0.0053
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
- delta: 2.6364
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-v28-affect01-chain-20260831
prediction_match:
  overall: true
  per_diagnostic:
    eiv_momentary_state_auc_over_null: true
provenance: {}
recipe_fingerprint: {}
verdict: 'null'
---

## Summary
Tests hypothesis [[state-detection-was-substrate-limited-on-the-46fa39]].
Branches from [[exp-v28-affect01-chain-20260831]].

## Measurements
- [[eiv_effective_rank]]: before 8.599 → after 7.403  (Δ -1.196, down)
- [[eiv_momentary_state_auc_over_null]]: before 0.0204 → after 0.0257  (Δ +0.0053, up ✓ predicted up)
- [[eiv_supabase_hr_raw_mae_bpm]]: before 3.0297 → after 5.6661  (Δ +2.6364, up)

## Gates
- `g0-geometry-holds`: PASS — logs/v30_geometry.json: eff_rank 7.403 (>= 7.0), cos_p50 0.144, cos_p95 0.645. v28-A comparator 8.599. Runner did not abort; chain proceeded to state scoring + B + C.
- `g1-state-beats-historical-ceiling`: **FAIL** — logs/v30_state_w30_eval.json: v30state ridge W30/max AUC 0.5939, null_mean 0.5682, over_null +0.02567, z 2.15, perm_p 0.0 (100 perms), n=2136, 113 subjects, nurse_stress excluded. Bar was over_null > 0.05 (mde 0.02) -> FAIL. v28affect01 comparator on identical rows/nulls: over_null +0.02042, z 1.60, perm_p 0.08. Delta v30-v28 = +0.0053, i.e. a quarter of the preregistered mde 0.02 -> powered null on the lever. Head probe went the other way on both arms (v30 -0.0195, v28 -0.0265).
- `g2-hr-no-harm`: **FAIL** — logs/emission_v30state_supa_summary.json (n=200, parse_ok 200): hr_raw MAE 5.666 -> outside v28 band [2.23, 3.83]; corr 0.777 (>= 0.60 OK), top1_mass 0.57 (v28 0.47), std_ratio 2.71 (v28 0.93), skill -1.712 vs trivial 3.954. Dense no-harm logs/emission_v30state_dense_summary.json (n=128, parse_ok 125): hr_raw MAE 15.085, corr 0.660, top1 0.273, skill -8.099 vs v28 dense MAE 2.851 / corr 0.937. MAE gap vs v28 supabase = 2.636 bpm, far above the paired arm-vs-arm sigma 0.195 and above the mde 0.8 -> a demonstrated regression, not noise.

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Interpretation
**Failure mode:** `claim_false`

The 'state detection was substrate-limited by rank collapse' story is now dead by measurement. On a geometry-healthy encoder (eff_rank 7.403, logs/v30_geometry.json) with the patch-level soft-state supervision W8/E29 asked for, momentary-state ridge W30/max over_null reached only +0.0257 (z 2.15) vs the no-state comparator's +0.0204 (z 1.60) on identical rows/nulls — a +0.0053 gain, a quarter of the preregistered mde 0.02 and still below the historical rank-collapsed ceiling +0.037. Every future state arm must therefore treat the W=30 wrist state signal as information-limited, not substrate-limited: the next honest move is changing the LABEL GEOMETRY (window/pooling/label construction, per exp-053's peri-EMA problem) rather than adding another encoder-side supervision term. Second, independent lesson: state_patch_weight 0.1 is deployment-toxic — it left corr intact (0.777 supa / 0.660 dense) but blew the emission SCALE (std_ratio 2.71/2.46 vs v28's 0.93/1.11, top1_mass 0.57 vs 0.47), costing 2.636 bpm supabase and 12.2 bpm dense MAE. An auxiliary head can preserve HR ordering and still destroy HR calibration; corr alone would have hidden this.

Transfers to: [[the-window-level-ce-state-term-trains-on-a35561]], [[h-w8-cardinality-invariant-attention-pooling-f01250]], [[e29-state-geometry-innocent-weights-guilty-20260815]], [[w-sweep-rb-window-size-x-pooling-state-sweep-73469a]]

## Key numbers
- `dense_corr_v30`: 0.66
- `dense_mae_v28`: 2.851
- `dense_mae_v30`: 15.085
- `eff_rank`: 7.403
- `g1_bar`: 0.05
- `g1_mde`: 0.02
- `paired_arm_sigma_mae`: 0.195
- `phase_c_final_loss`: 1.3018
- `ridge_auc_v30`: 0.5939
- `ridge_over_null_delta`: 0.0053
- `ridge_over_null_v28`: 0.02042
- `ridge_over_null_v30`: 0.02567
- `ridge_z_v30`: 2.153
- `seed_band_mae`: 3.19
- `supa_corr_v30`: 0.777
- `supa_mae_delta`: 2.636
- `supa_mae_v28`: 3.03
- `supa_mae_v30`: 5.666
- `supa_std_ratio_v30`: 2.708
- `supa_top1_v30`: 0.57

## Artifacts
- `logs/v30_geometry.json`
- `logs/v30_state_w30_eval.json`
- `logs/v30_phase_a_report.json`
- `logs/v30_phase_b_report.json`
- `logs/v30_phase_c_report.json`
- `logs/emission_v30state_supa_summary.json`
- `logs/emission_v30state_dense_summary.json`
- `logs/v30_driver.log`
- `outputs/adapters/v30state_c/manifest.json`

## Do not retry
- Do NOT re-run state_patch_weight 0.1 on a strat-A/affect-0.1 substrate hoping for state gain: on a geometry-healthy encoder (eff_rank 7.403) the ridge W30/max over_null moved only +0.0053 (0.0257 vs v28's 0.0204), a quarter of the preregistered mde 0.02 and half the historical rank-collapsed ceiling +0.037. The 'state detection was substrate-limited' explanation is now measured and dead — healthy geometry does NOT unlock momentary state at W=30.
- Do NOT add state_patch_weight to any arm intended for deployment at this dose: it cost 2.636 bpm supabase MAE (5.666 vs v28 3.030) and 12.2 bpm dense MAE (15.085 vs 2.851) with std_ratio 2.71/2.46 vs v28's 0.93/1.11 — an emission-calibration blowout, not a seed draw.
- Do NOT price the v30 vs v28 MAE gap as noise. 2.636 bpm is ~13.5x the paired arm-vs-arm sigma 0.195; it is a demonstrated regression.

## Notes
TERMINAL: chain ran end-to-end (A 2000 steps / geometry / state scoring / B@30 1000 steps / C band04 2600 steps, final_loss 1.302, adapter bundle written to outputs/adapters/v30state_c, 282 MB, manifest.json + lora/ + sensor_modules/). g0 PASSED (eff_rank 7.403). g1 FAILED: ridge over_null +0.0257 vs the preregistered bar +0.05; the v30-vs-v28 gain +0.0053 is a quarter of the preregistered mde 0.02 -> POWERED NULL on the state lever at the committed scale. g2 FAILED hard: supabase hr_raw MAE 5.666 (band was [2.23,3.83]) and dense MAE 15.085 (v28 2.851) — the regression is 2.636 bpm on supabase, ~13.5x the paired arm-vs-arm sigma 0.195 and far outside the 3.19 bpm seed band on the dense side, so it is a real regression, not a draw artefact. Mechanism visible in the emission stats: std_ratio blew up to 2.71 supabase / 2.46 dense (v28: 0.93 / 1.11) and top1_mass rose to 0.57 (v28 0.47) while distinct collapsed to 20/28 (v28 dense 39) — the arm emits an over-dispersed, mode-locked HR distribution. corr stayed high (0.777 supa / 0.660 dense), i.e. the ordering survived but the scale/calibration did not: state_patch_weight 0.1 bought +0.005 over_null and cost the HR calibration. INCUMBENT UNCHANGED: v28affect01 stays the deployment arm.

#sensor-text #contrastive #deployment-distribution #wearables #epoch-resolution #baseline #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
