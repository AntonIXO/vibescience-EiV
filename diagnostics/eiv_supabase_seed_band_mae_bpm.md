---
direction: lower_better
id: eiv_supabase_seed_band_mae_bpm
name: Post-atomfix Phase-C seed-to-seed range of supabase hr_raw MAE
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: null
topic_tags: []
unit: bpm
---

## Description
max - min of eiv_supabase_hr_raw_mae_bpm across >=3 runs of the IDENTICAL Phase-C configuration differing ONLY in the training seed, off the SAME Phase-B checkpoint, scored on the SAME frozen 200-row supabase draw (default_rng(0) over the 223-row pool).

WHY THIS EXISTS. The PRE-atomfix config family measured a seed-only band of 11.37 bpm on this exact metric (seeds 42/43/44 -> 20.83/19.35/9.45, commit c62b983) — wider than most effects anyone would want to claim. The atom fix (a71034c) plausibly shrank it (the 30-floor was the dominant bistability), but that is UNMEASURED: v7noatom is a single seed (42, MAE 11.54). Until this band is measured post-fix, no deployment-MAE comparison against v7noatom can state its minimum detectable effect, and v7noatom's own 11.54 is a draw from an unknown-width distribution.

HOW TO USE. Measure once per config family (v7noatom recipe = lora_proxy_12ch_wl_noatom). Every subsequent arm must beat the v7noatom SEED BAND (median and range), not the single seed-42 point. Report with (a) per-seed values, (b) the companion floor-emission rates per seed (the floor-rate=0 claim is structural and should hold on ALL seeds — a single seed re-flooring falsifies the atom-fix account), (c) the trivial supabase-median baseline 4.02 on the same draw.

*Unit:* `bpm` — *lower_better*
