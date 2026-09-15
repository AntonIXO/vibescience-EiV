---
artifacts:
- outputs/adapters/v15exact_c
- logs/v15_driver.log
- logs/v15_phase_c.log
- logs/emission_v15exact_supa_summary.json
- logs/emission_v15exact_dense_summary.json
- scratch/run_v15_exact.sh
- scratch/_target_coupling_probe.py
- logs/v14_target_coupling_probe.json
closed: true
config_note: 'v15exact: profile lora_proxy_12ch_wl_band04 BYTE-UNTOUCHED (the recipe
  with recorded healthy results: 6-ds filter, default avg pooling, default truncated
  json prompt, seed 42), trained C-only off the frozen v11full/phase_b encoder on
  the CURRENT [14092] b659855 bundle. Deliberately compound vs v14: resolves the disjunction
  ''recipe knobs {masked_avg, fit256, seed43} vs corpus-as-training-data'' in one
  arm, decisive in both directions. Runner scratch/run_v15_exact.sh; scoring identical
  instrument (supabase n=200 batch1 rng0 + dense-128), pre-check asserts 6-ds manifest.
  Free probes already run: target/prefix coupling PERFECT (p50 0.025 bpm, 0% > 2bpm,
  n=300) and target marginal mode 70-75 bpm (12% near the 55.9 constant) -- logs/v14_target_coupling_probe.json.'
cost:
  gpu_hours: 1.0
  wall_hours: 0.98
crash_reason: ''
created_at: '2026-08-30T12:30:55+00:00'
diagnostics_measured:
- after: 14.268
  before: 4.954
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.355
  before: 0.116
  diagnostic_id: eiv_gen_peak_corr
- after: 24.814
  before: 22.161
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT run ANY further Phase-C recipe variant (pooling, prompt, seed, roster, weights,
  pool gating) on the b659855 [14092] bundle expecting health: the byte-identical
  historically-healthy recipe collapses on it (corr 0.355, skill -10.3). The recipe
  axis is EXHAUSTED in both directions -- collapsed-recipe-knobs refuted (v12-v14)
  and healthy-recipe-knobs refuted (v15). The cause lives in the bundle as C-stage
  TRAINING data.'
- 'Do NOT blame target construction: coupling probe measured |json target - prefix-derived|
  p50 0.025 bpm, 0% > 2 bpm over 300 draws on the collapsed recipe. Targets follow
  the prefix perfectly.'
- Do NOT quote v15's corr 0.355 as 'less collapsed than v13's 0.130' -- top1_mass
  0.805 and skill -10.3 say otherwise; the constant merely moved to a worse location.
  MAE and corr are not comparable across arms whose emissions are near-constant at
  different values.
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAIL, decisively, and this is the informative direction. The byte-identical
    healthy recipe (6-ds, avg pooling, default prompt, seed 42 -- manifest pre-check
    PASS) trained on the [14092] bundle reads supabase corr 0.355 vs the >= 0.60 bar,
    with top1_mass 0.805: still a collapse. The corpus is NOT exonerated -- the RECIPE
    is: every knob combination now has a collapsed instance on the new bundle, and
    this exact recipe has recorded healthy results (corr ~0.74-0.81) on the old corpus.
    TENTH consecutive fresh-C collapse on a post-rebuild bundle.'
  gate_id: g1-exact-recipe-healthy-on-new-corpus
  passed: false
- evidence: 'FAIL, worst of the series: supabase hr_raw MAE 14.268 vs trivial 3.954
    -> skill -10.313 (v11 -1.535, v12 -0.429, v13 -0.704, v14 -0.999). The constant
    this arm crystallised sits far from the supabase median -- top1 0.805 like v11full.
    hr_peak MAE 27.667, skill -10.258.'
  gate_id: g2-beats-trivial
  passed: false
- evidence: 'Advisory FAIL: dense-128 hr_raw MAE 24.814 vs trivial 7.774 (skill -17.0),
    corr 0.287, top1 0.227. Same ~20+ regime as every collapsed arm.'
  gate_id: g3-dense-advisory
  passed: false
git_ref: ''
hypothesis_id: the-c-stage-collapse-is-caused-by-the-recipe-01862e
id: exp-v15-exact-healthy-recipe-20260830
interpretation:
  at: '2026-08-30T13:29:23+00:00'
  failure_mode: claim_false
  lesson: 'THE RECIPE IS EXONERATED AND THE CORPUS IS CONVICTED. The byte-identical
    historically-healthy band04 recipe (avg pooling, default prompt, seed 42, 6-ds)
    collapses when trained C-only on the b659855 [14092] bundle (corr 0.355, top1
    0.805, skill -10.3 -- the worst of the series), while a healthy adapter reads
    the same bundle at inference at corr 0.760. Combined with v12-v14 this closes
    BOTH sides of the recipe axis: no knob setting, healthy or collapsed, trains a
    working C stage on this bundle -- 10/10 fresh post-rebuild C arms are collapsed.
    The cause is in the bundle as TRAINING data: not targets (coupling p50 0.025 bpm),
    not the target marginal (mode 70-75, constant at 55.9), so the live suspect is
    the PREFIX distribution / the encoder''s input statistics as the LoRA sees them
    during training. The formal verdict is ''directional_only/inconclusive'' only
    because the hypothesis was worded recipe-side and its gates were pass-side; the
    DISJUNCTION the arm was built to resolve resolved cleanly: corpus. IMMEDIATE NEXT:
    Anton is landing merger bug fixes -- rerun scratch/run_v15_exact.sh unchanged
    on the fixed bundle as the first arm; if it heals, the merger defects were the
    cause of the entire v11-v15 series.'
  transfers_to:
  - the-c-stage-collapse-is-caused-by-the-recipe-01862e
  - the-c-stage-collapse-is-caused-by-short-window-5859e2
  - the-c-stage-collapse-is-caused-by-upsampling-186858
key_numbers:
  dense_corr: 0.287
  dense_mae: 24.814
  dense_top1: 0.227
  n_train_rows: 7566.0
  supa_corr: 0.355
  supa_mae: 14.268
  supa_skill: -10.313
  supa_top1: 0.805
  train_final_loss: 1.1
observed_effects:
- delta: 9.314
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
- delta: 0.239
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: 2.653
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-v14-long-pool-conly-20260830
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: true
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[the-c-stage-collapse-is-caused-by-the-recipe-01862e]].
Branches from [[exp-v14-long-pool-conly-20260830]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 4.954 → after 14.268  (Δ +9.314, up)
- [[eiv_gen_peak_corr]]: before 0.116 → after 0.355  (Δ +0.239, up ✓ predicted up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 22.161 → after 24.814  (Δ +2.653, up)

## Gates
- `g1-exact-recipe-healthy-on-new-corpus`: **FAIL** — FAIL, decisively, and this is the informative direction. The byte-identical healthy recipe (6-ds, avg pooling, default prompt, seed 42 -- manifest pre-check PASS) trained on the [14092] bundle reads supabase corr 0.355 vs the >= 0.60 bar, with top1_mass 0.805: still a collapse. The corpus is NOT exonerated -- the RECIPE is: every knob combination now has a collapsed instance on the new bundle, and this exact recipe has recorded healthy results (corr ~0.74-0.81) on the old corpus. TENTH consecutive fresh-C collapse on a post-rebuild bundle.
- `g2-beats-trivial`: **FAIL** — FAIL, worst of the series: supabase hr_raw MAE 14.268 vs trivial 3.954 -> skill -10.313 (v11 -1.535, v12 -0.429, v13 -0.704, v14 -0.999). The constant this arm crystallised sits far from the supabase median -- top1 0.805 like v11full. hr_peak MAE 27.667, skill -10.258.
- `g3-dense-advisory`: **FAIL** — Advisory FAIL: dense-128 hr_raw MAE 24.814 vs trivial 7.774 (skill -17.0), corr 0.287, top1 0.227. Same ~20+ regime as every collapsed arm.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `claim_false`

THE RECIPE IS EXONERATED AND THE CORPUS IS CONVICTED. The byte-identical historically-healthy band04 recipe (avg pooling, default prompt, seed 42, 6-ds) collapses when trained C-only on the b659855 [14092] bundle (corr 0.355, top1 0.805, skill -10.3 -- the worst of the series), while a healthy adapter reads the same bundle at inference at corr 0.760. Combined with v12-v14 this closes BOTH sides of the recipe axis: no knob setting, healthy or collapsed, trains a working C stage on this bundle -- 10/10 fresh post-rebuild C arms are collapsed. The cause is in the bundle as TRAINING data: not targets (coupling p50 0.025 bpm), not the target marginal (mode 70-75, constant at 55.9), so the live suspect is the PREFIX distribution / the encoder's input statistics as the LoRA sees them during training. The formal verdict is 'directional_only/inconclusive' only because the hypothesis was worded recipe-side and its gates were pass-side; the DISJUNCTION the arm was built to resolve resolved cleanly: corpus. IMMEDIATE NEXT: Anton is landing merger bug fixes -- rerun scratch/run_v15_exact.sh unchanged on the fixed bundle as the first arm; if it heals, the merger defects were the cause of the entire v11-v15 series.

Transfers to: [[the-c-stage-collapse-is-caused-by-the-recipe-01862e]], [[the-c-stage-collapse-is-caused-by-short-window-5859e2]], [[the-c-stage-collapse-is-caused-by-upsampling-186858]]

## Key numbers
- `dense_corr`: 0.287
- `dense_mae`: 24.814
- `dense_top1`: 0.227
- `n_train_rows`: 7566
- `supa_corr`: 0.355
- `supa_mae`: 14.268
- `supa_skill`: -10.313
- `supa_top1`: 0.805
- `train_final_loss`: 1.1

## Artifacts
- `outputs/adapters/v15exact_c`
- `logs/v15_driver.log`
- `logs/v15_phase_c.log`
- `logs/emission_v15exact_supa_summary.json`
- `logs/emission_v15exact_dense_summary.json`
- `scratch/run_v15_exact.sh`
- `scratch/_target_coupling_probe.py`
- `logs/v14_target_coupling_probe.json`

## Do not retry
- Do NOT run ANY further Phase-C recipe variant (pooling, prompt, seed, roster, weights, pool gating) on the b659855 [14092] bundle expecting health: the byte-identical historically-healthy recipe collapses on it (corr 0.355, skill -10.3). The recipe axis is EXHAUSTED in both directions -- collapsed-recipe-knobs refuted (v12-v14) and healthy-recipe-knobs refuted (v15). The cause lives in the bundle as C-stage TRAINING data.
- Do NOT blame target construction: coupling probe measured |json target - prefix-derived| p50 0.025 bpm, 0% > 2 bpm over 300 draws on the collapsed recipe. Targets follow the prefix perfectly.
- Do NOT quote v15's corr 0.355 as 'less collapsed than v13's 0.130' -- top1_mass 0.805 and skill -10.3 say otherwise; the constant merely moved to a worse location. MAE and corr are not comparable across arms whose emissions are near-constant at different values.

## Notes
THE DECISIVE ARM, and it fell on the CORPUS side of the disjunction exactly as predicted (for once my prediction matched: 0/5 became 1/6). The healthy band04 recipe -- byte-untouched profile, manifest pre-check confirming 6-ds/avg/default-prompt/seed-42 -- collapses when its C stage TRAINS on the [14092] b659855 bundle, while a healthy adapter READING the same bundle at inference scores corr 0.760. That kills every remaining recipe-side account at once and localises the cause in the training-data path: something about the new bundle's PREFIX distribution (not its targets -- coupling is perfect; not its target marginal -- the mode is not the constant) makes CLM training converge to prefix-ignoring within 2600 steps under four different recipes. Ten of ten fresh post-rebuild C arms are now collapsed. NEXT (corpus-side, cheap first): (1) diff population_stats.json old-vs-new (a z-space shift changes every prefix value the LoRA sees while the frozen encoder was TRAINED on the same space -- but the ENCODER passed its probes, so suspect the supabase block specifically); (2) compare the supabase rows' prefix stats (coverage, HR-minutes, z-values) old bundle vs new -- eval_v3_1min was REBUILT (sha 3e51df88) at the same time, so the deployment draw itself changed; (3) Anton is landing a FIXED dataset (merger bug repairs) -- rerun THIS EXACT arm (scratch/run_v15_exact.sh, 1 GPU-hour) on the fixed bundle as the first test; if it heals, the merger bugs were the cause and the whole v11-v15 series was measuring them. Wall 59 min (15:29-16:28).

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
