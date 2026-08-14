---
direction: lower_better
id: eiv_supabase_floor_emission_rate
name: Deployment-distribution hr_raw floor-emission rate (exact "30")
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
