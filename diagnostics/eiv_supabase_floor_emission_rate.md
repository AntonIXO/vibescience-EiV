---
direction: lower_better
id: eiv_supabase_floor_emission_rate
name: Deployment-distribution hr_raw floor-emission rate (exact "30")
noise_k: 2.0
noise_n: 3
noise_note: 'Exact-30 floor emission rate on the frozen 200-row supabase draw, v7noatom
  seeds 42/43/44 = 0/200, 0/200, 0/200. The band is EXACTLY zero across seeds: this
  is a structural (present/absent) property, not a noisy scalar, which is why the
  baseline designation was made on it rather than on a point score. sigma=0 means
  any non-zero movement is signal — appropriate here and NOT a placeholder. Source:
  exp-v7noatom-seed-band-20260813 gate g1-all-seeds-floor-rate-below-0.05 (PASS).'
noise_sigma: 0.0
noise_source: measured
requires_control: null
topic_tags:
- deployment-distribution
- collapse
unit: frac
---

## Description
Fraction of the fixed 200-row supabase draw (same draw as eiv_supabase_hr_raw_mae_bpm) on which the LM emits hr_raw exactly 30 -- the value of min_valid_hr_bpm, which the pre-a71034c augmentation clamp turned into ~14.6% of all training targets (scratch/floor_atom_estimate.py). Truth on this population is never below 45 bpm, so ANY exact-30 emission is attractor capture, not reading.

Reference values 2026-08-12 (commit 0940a9e, atom still in training data): v4w 0.595 (119/200), v5c 0.750 (150/200), v5d 0.895 (179/200).

Companion to eiv_supabase_hr_raw_mae_bpm; report both together with n.

*Unit:* `frac` — *lower_better*

#deployment-distribution #collapse
