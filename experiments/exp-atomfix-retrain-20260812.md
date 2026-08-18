---
artifacts: []
closed: true
config_note: 'Retrain queued as proc_9a60b5cd8903 (scratch/run_atomfix_retrain.sh,
  logs/atomfix_retrain.log), behind present-mean counterfactual proc_f6ffac91272f.
  Profile lora_proxy_12ch_wl_noatom: seed 42, init outputs/checkpoints/v5c/phase_b,
  id_wl_12ch filter; single variable = a71034c truncated-uniform augment draw. Baselines
  frozen pre-run: v5c supabase MAE 20.83 / floor 0.750 / escaped-floor 3.65 (n=50);
  v4w 16.72 / 0.595 / 4.20 (n=81); v5d 25.05 / 0.895 / 6.26 (n=21); trivial 4.02;
  dense v5c 3.07 / v4w 6.14. Draw: default_rng(0) over 223-row supabase pool, 200
  rows, byte-identical across arms. Capture instrument commit 0940a9e.'
cost: null
crash_reason: ''
created_at: '2026-08-12T09:00:23+00:00'
diagnostics_measured:
- after: 11.54
  before: 20.83
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.0
  before: 0.75
  diagnostic_id: eiv_supabase_floor_emission_rate
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 0/200 supabase rows emit exactly 30 (was 150/200 on v5c). Top emitted
    values now 38.9 (n=111), 54.6 (n=42); the exact-30 atom is gone. outputs/probes/v7noatom_emissions_supabase.json,
    scratch/v7_attractor_forensics.py.
  gate_id: gate-floor-collapse
  passed: true
- evidence: 'v7noatom supabase hr_raw MAE 11.54 vs bar <=6.14. Better than v5c 20.83
    and v4w 16.72, still 2.9x the trivial 4.02. Residual error is concentrated in
    a NEW attractor: 111/200 rows emit exactly 38.9 (truth under them 55.7+/-3.3,
    constant -16.8 bpm bias); the other 89 rows have MAE 5.03. Attractor membership
    correlates with truth level (corr -0.56, truth p50 56.0 vs 61.4) NOT coverage
    (corr cov_hr +0.008). 38.9 ~= the mean of a low truncated-uniform augment band
    -- the model emits a low-band prototype for OOD-low days instead of interpolating
    below its training support.'
  gate_id: gate-mae-v4w-level
  passed: false
- evidence: Dense 128-row capture hr_raw MAE 2.89 vs bar <=4.5 (v5c 3.07, v4w 6.14).
    corr 0.893, skill vs trivial +4.27 bpm, 42 distinct values, top1 mass 0.094. No
    regression; slightly better than v5c. outputs/probes/v7noatom_emissions_n128_summary.json.
  gate_id: gate-dense-no-regress
  passed: true
- evidence: 'Manifest diff v5c vs v7noatom: training.final_loss 0.5821 vs 1.0952,
    training.seed absent vs 42, created_at, git_sha 99f1e75 vs 1e2e2b9. Byte-identical:
    base_model_id, base_model_type, data_paths, hr_buckets (pinned cut points 157.71853625752556/126.9999977820953),
    sensor_config, version, dataset_filter. Single variable confirmed.'
  gate_id: gate-single-variable
  passed: true
git_ref: ''
hypothesis_id: the-hr-raw-floor-emissions-on-the-87cd26
id: exp-atomfix-retrain-20260812
interpretation:
  at: '2026-08-18T21:08:38+00:00'
  failure_mode: recipe_conditional
  lesson: 'Removing the augmentation clamp''s probability atom (a71034c, single verified
    variable) did exactly what it claimed — exact-30 emissions 150/200 -> 0/200, floor
    rate 0.750 -> 0.000 — and nearly halved deployment error (supabase hr_raw MAE
    20.83 -> 11.54, better than v5c 20.83 and v4w 16.72) with dense-128 improving
    to 2.89 (v5c 3.07, v4w 6.14), yet missed the <= 6.14 bar because a SECOND quantization
    attractor immediately replaced the first: 111/200 rows emit exactly 38.9 against
    truth 55.7 +/- 3.3 (constant -16.8 bpm bias), while the remaining 89 rows sit
    at MAE 5.03. Attractor membership tracks TRUTH LEVEL (corr -0.56, truth p50 56.0
    vs 61.4), not coverage (corr with cov_hr +0.008), and 38.9 is approximately the
    mean of a low truncated-uniform augment band — so the model emits a low-band prototype
    for OOD-low deployment days instead of interpolating below its training support.
    The lesson to act on: this is a TRAINING TARGET LAW defect one level up from the
    atom, not an encoder or sparse-reading defect (the present-mean counterfactual
    left 133-154/200 emissions byte-identical: v4w 16.72 -> 16.56, v5c 20.83 -> 21.07),
    so patching individual atoms will keep relocating the prototype; the correct lever
    is populating and smoothing the low-HR band in the target distribution, which
    is precisely what E1 then did (supabase median 3.89).'
  transfers_to:
  - the-atom-fix-a71034c-removed-the-d96b6e
  - e1-band-mass-starvation-drives-supabase-mae-and-seed-band
  - hrv-raw-fails-on-deployment-for-the-624895
  - the-deficit-is-not-purely-a-decode-rule-1ccb22
key_numbers: {}
observed_effects:
- delta: -9.29
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: -0.75
  diagnostic_id: eiv_supabase_floor_emission_rate
  direction: down
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_skill_bpm: false
    eiv_supabase_floor_emission_rate: true
    eiv_supabase_hr_raw_mae_bpm: true
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[the-hr-raw-floor-emissions-on-the-87cd26]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 20.83 → after 11.54  (Δ -9.29, down ✓ predicted down)
- [[eiv_supabase_floor_emission_rate]]: before 0.75 → after 0.0  (Δ -0.75, down ✓ predicted down)

## Gates
- `gate-floor-collapse`: PASS — 0/200 supabase rows emit exactly 30 (was 150/200 on v5c). Top emitted values now 38.9 (n=111), 54.6 (n=42); the exact-30 atom is gone. outputs/probes/v7noatom_emissions_supabase.json, scratch/v7_attractor_forensics.py.
- `gate-mae-v4w-level`: **FAIL** — v7noatom supabase hr_raw MAE 11.54 vs bar <=6.14. Better than v5c 20.83 and v4w 16.72, still 2.9x the trivial 4.02. Residual error is concentrated in a NEW attractor: 111/200 rows emit exactly 38.9 (truth under them 55.7+/-3.3, constant -16.8 bpm bias); the other 89 rows have MAE 5.03. Attractor membership correlates with truth level (corr -0.56, truth p50 56.0 vs 61.4) NOT coverage (corr cov_hr +0.008). 38.9 ~= the mean of a low truncated-uniform augment band -- the model emits a low-band prototype for OOD-low days instead of interpolating below its training support.
- `gate-dense-no-regress`: PASS — Dense 128-row capture hr_raw MAE 2.89 vs bar <=4.5 (v5c 3.07, v4w 6.14). corr 0.893, skill vs trivial +4.27 bpm, 42 distinct values, top1 mass 0.094. No regression; slightly better than v5c. outputs/probes/v7noatom_emissions_n128_summary.json.
- `gate-single-variable`: PASS — Manifest diff v5c vs v7noatom: training.final_loss 0.5821 vs 1.0952, training.seed absent vs 42, created_at, git_sha 99f1e75 vs 1e2e2b9. Byte-identical: base_model_id, base_model_type, data_paths, hr_buckets (pinned cut points 157.71853625752556/126.9999977820953), sensor_config, version, dataset_filter. Single variable confirmed.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `recipe_conditional`

Removing the augmentation clamp's probability atom (a71034c, single verified variable) did exactly what it claimed — exact-30 emissions 150/200 -> 0/200, floor rate 0.750 -> 0.000 — and nearly halved deployment error (supabase hr_raw MAE 20.83 -> 11.54, better than v5c 20.83 and v4w 16.72) with dense-128 improving to 2.89 (v5c 3.07, v4w 6.14), yet missed the <= 6.14 bar because a SECOND quantization attractor immediately replaced the first: 111/200 rows emit exactly 38.9 against truth 55.7 +/- 3.3 (constant -16.8 bpm bias), while the remaining 89 rows sit at MAE 5.03. Attractor membership tracks TRUTH LEVEL (corr -0.56, truth p50 56.0 vs 61.4), not coverage (corr with cov_hr +0.008), and 38.9 is approximately the mean of a low truncated-uniform augment band — so the model emits a low-band prototype for OOD-low deployment days instead of interpolating below its training support. The lesson to act on: this is a TRAINING TARGET LAW defect one level up from the atom, not an encoder or sparse-reading defect (the present-mean counterfactual left 133-154/200 emissions byte-identical: v4w 16.72 -> 16.56, v5c 20.83 -> 21.07), so patching individual atoms will keep relocating the prototype; the correct lever is populating and smoothing the low-HR band in the target distribution, which is precisely what E1 then did (supabase median 3.89).

Transfers to: [[the-atom-fix-a71034c-removed-the-d96b6e]], [[e1-band-mass-starvation-drives-supabase-mae-and-seed-band]], [[hrv-raw-fails-on-deployment-for-the-624895]], [[the-deficit-is-not-purely-a-decode-rule-1ccb22]]

## Notes
MECHANISM CONFIRMED, TARGET PARTIALLY MISSED. The exact-30 atom is eliminated (floor 0.750->0.000, gate-floor-collapse PASS) and supabase MAE nearly halved (20.83->11.54, best of any arm) with dense IMPROVED (2.89 vs v5c 3.07) -- but the <=6.14 bar failed because a SECOND quantization attractor surfaced: 111/200 rows emit exactly 38.9 (truth 55.7+/-3.3, constant -16.8 bias; non-attractor rows MAE 5.03). Membership tracks truth level (corr -0.56), not coverage (cov_hr corr +0.008): supabase truths (median ~56 bpm) sit at the extreme low tail of the training distribution, and the model snaps them to a low-band prototype rather than interpolating. This is the SAME defect class as the atom one level up: the training target distribution, not the encoder. T0 counterfactual (present-mean fill, commit 1e2e2b9) independently confirmed decode-side locus: paired n=200, v4w 16.72->16.56, v5c 20.83->21.07, 133-154/200 byte-identical emissions. Successor lever: make the low-HR band populated and smooth in training targets (e.g. augment window extended/reweighted below 55 bpm, or supabase-like low-mean days upsampled), tracked on the same two diagnostics. Artifacts: outputs/probes/v7noatom_emissions_{supabase,n128}*.json, scratch/v7_attractor_forensics.py, adapter outputs/checkpoints/v7noatom/phase_c.

#deployment-distribution #optihealth #collapse #baseline
