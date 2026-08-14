---
direction: neutral
id: eiv_dense128_hr_raw_mae_bpm
name: eiv_dense128_hr_raw_mae_bpm
requires_control: null
topic_tags: []
unit: ''
---

## Description
Mean |emit_raw - true_mean| in bpm over the 128-row dense capture (default draw, 97% lifesnaps/pmdata; scratch/emission_diversity_audit.py --n 128, greedy decode, production json_slots prompt). Companion to eiv_supabase_hr_raw_mae_bpm: guards against buying deployment accuracy by wrecking the dense population. Reference points: v7noatom 2.89, v9band (E1) worst 5.65. Report per-seed with the worst seed, never a single point.

*Unit:* `` — *neutral*
