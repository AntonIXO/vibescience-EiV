---
direction: lower_better
id: eiv_supabase_hr_raw_mae_bpm
name: Deployment-distribution (optihealth_supabase) native-emission hr_raw MAE
noise_k: 2.0
noise_n: 3
noise_note: 'Phase-C seed band on the deployment (supabase) hr_raw MAE, v7noatom family
  seeds 42/43/44 = 11.54 / 15.78 / 9.49 bpm, RANGE 6.29 bpm (= eiv_supabase_seed_band_mae_bpm
  after the atom fix; pre-fix family band was 11.37). sigma = range/2 = 3.145 so k=2
  reproduces the measured 6.29 band. Source: exp-v7noatom-seed-band-20260813 gate
  g2-band-reported-with-per-seed-values (PASS). Context: the trivial always-median
  baseline on the same 200-row draw is 4.02 bpm, i.e. the seed band alone exceeds
  the trivial baseline — single-seed arm comparisons on this diagnostic are not interpretable.'
noise_sigma: 3.145
noise_source: measured
requires_control: null
topic_tags:
- deployment-distribution
- optihealth
unit: bpm
---

## Description
MAE (bpm) of the LM's NATIVE GENERATED hr_raw against the present-only daily mean HR, on a fixed 200-row draw restricted to dataset==optihealth_supabase in data/eval_v3_1min (rng default_rng(0) over the 223-row supabase pool; scratch/emission_diversity_audit.py --dataset optihealth_supabase --n 200). This is the PRODUCTION population: the eiv annotate path serves these rows, mean HR coverage 0.789, truth mean 58.3 bpm, truth never <45 bpm.

MUST be reported with (a) the supabase-median trivial baseline recomputed on the same 200 rows (4.02 bpm on the 2026-08-12 draw), (b) the floor-emission rate = fraction of rows emitting exactly "30" (companion diagnostic eiv_supabase_floor_emission_rate), and (c) the escaped-floor conditional MAE, because the unconditional MAE conflates attractor capture with reading skill.

WHY THIS EXISTS. Every prior emission metric was computed on unrestricted eval draws that are 97% lifesnaps+pmdata (84/40/3 of 128). Arm ranking REVERSED between that draw and this one (v5c beat v4w 108/128 dense, p<1e-4; on supabase v4w 16.72 < v5c 20.83 < v5d 25.05, all catastrophically below the 4.02 trivial baseline). Anton's manual annotations detected the failure before any instrument did. A metric on the wrong population cannot gate a deployment decision.

Reference values 2026-08-12 (commit 0940a9e): v4w 16.72 / v5c 20.83 / v5d 25.05; escaped-floor conditional: v4w 4.20 (n=81), v5c 3.65 (n=50), v5d 6.26 (n=21).

*Unit:* `bpm` — *lower_better*

#deployment-distribution #optihealth
