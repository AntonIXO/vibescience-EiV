---
direction: lower_better
id: eiv_supabase_seed_band_mae_bpm
name: eiv_supabase_seed_band_mae_bpm
noise_k: 2.0
noise_n: 4000
noise_note: 'Finite-DRAW sigma measured 2026-08-31 by scratch/noise_floor_emission.py
  (row bootstrap, 4000 resamples, seed 0) on the fixed 200-row supabase capture: per-arm
  sigma of the hr_raw MAE point estimate is 0.155 (v28affect01), 0.177 (v25r30), 0.209
  (v27affect), 0.427 (baseline_sync); the PAIRED sigma of the difference between two
  arms on shared rows is 0.195. 0.195 is the honest floor for an arm-vs-arm MAE claim
  on this instrument. NOTE this is strictly the draw component and is MUCH SMALLER
  than the seed-to-seed retrain band of 3.19 bpm measured in exp-052/exp-054 -- an
  arm-vs-arm MAE gap must clear the SEED band, not this one, before it is a recipe
  difference rather than a lucky init. Report logs/noise_floor_emission.json.'
noise_sigma: 0.195
noise_source: measured
requires_control: null
topic_tags: []
unit: bpm
---

## Description
max - min of eiv_supabase_hr_raw_mae_bpm across >=3 runs of the IDENTICAL Phase-C configuration differing ONLY in the training seed, off the SAME Phase-B checkpoint, scored on the SAME frozen 200-row supabase draw (default_rng(0) over the 223-row pool).

WHY THIS EXISTS. The PRE-atomfix config family measured a seed-only band of 11.37 bpm on this exact metric (seeds 42/43/44 -> 20.83/19.35/9.45, commit c62b983) — wider than most effects anyone would want to claim. The atom fix (a71034c) plausibly shrank it (the 30-floor was the dominant bistability), but that is UNMEASURED: v7noatom is a single seed (42, MAE 11.54). Until this band is measured post-fix, no deployment-MAE comparison against v7noatom can state its minimum detectable effect, and v7noatom's own 11.54 is a draw from an unknown-width distribution.

HOW TO USE. Measure once per config family (v7noatom recipe = lora_proxy_12ch_wl_noatom). Every subsequent arm must beat the v7noatom SEED BAND (median and range), not the single seed-42 point. Report with (a) per-seed values, (b) the companion floor-emission rates per seed (the floor-rate=0 claim is structural and should hold on ALL seeds — a single seed re-flooring falsifies the atom-fix account), (c) the trivial supabase-median baseline 4.02 on the same draw.

*Unit:* `bpm` — *lower_better*
