---
created_at: '2026-08-12T08:57:29+00:00'
former_ids:
- phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
id: phase-c-arms-fail-on-the-deployment-9217e0
paper_refs: []
problem_tags: []
status: open
title: 'Phase-C arms fail on the deployment distribution (optihealth_supabase): hr_raw
  floor atom at 30.0 plus unlearned sparsity handling'
topic_tags:
- deployment-distribution
- optihealth
- collapse
- baseline
updated_at: '2026-08-12T08:57:29+00:00'
updates: []
---

## Description
MEASURED 2026-08-12 (commit 0940a9e, outputs/probes/{v4w,v5c,v5d}_emissions_supabase.json, 200 identical-draw supabase rows, production prompt, greedy decode).

THE FAILURE. On the production annotate-path population (optihealth_supabase, mean HR coverage 0.789, truth HR mean 58.3 bpm, never <45 bpm):
- v4w hr_raw MAE 16.72 (trivial supabase-median baseline 4.02, skill -12.70), emits exactly "30" on 119/200
- v5c MAE 20.83 (skill -16.81), "30" on 150/200
- v5d MAE 25.05 (skill -21.03), "30" on 179/200
Arm ranking v4w > v5c > v5d is the REVERSE of the dense 128-row draw (where v5c beat v4w 108/128, p<1e-4). Anton's field annotations detected this before the instrument did, because the eval draw was 97% lifesnaps+pmdata (84/40/3 of 128) while supabase is 223/6151 eval rows.

ROOT CAUSE 1 (proven mechanism, fix in flight): the hr-augmentation clamp in src/training/datasets.py mapped the below-window tail of the uniform target_peak draw onto lo_peak EXACTLY, so the augmented daily mean landed at exactly min_valid_hr_bpm=30.0 on ~36.5% of augmented samples => ~14.6% of ALL hr_raw LM targets were the literal string "30.0" (scratch/floor_atom_estimate.py; prob=0.40, range 40-182.72). The supabase days are the lowest-HR sparsest days, nearest the attractor. Conditional on ESCAPING the floor, the arms read supabase fine: v4w n=81 MAE 4.20 corr 0.839; v5c n=50 MAE 3.65 (beats trivial 4.39); v5d n=21 MAE 6.26.

ROOT CAUSE 2 (separate defect, verified on a different regime): coverage dilution. Encoder zero-fills masked cells in normalized space (zero = population median 71.8), so a day with HR coverage c pools to 71.8 + c*(truth-71.8). Verified on wearable_exam_stress sample 10313: coverage 0.129, predicted 77.7, emitted 82.0 (~91% of the 36-bpm error). REFUTED as the supabase mechanism: regression of signed error on (cov-1)*(truth-med) gives slope -1.5/-0.47/+0.42 vs predicted 1.0, R2<=0.11. Two defects, not one.

ALSO OBSERVED on production rows: state slot is a near-constant prior with arm-dependent polarity (v5c "stress" 139/200, v4w "neutral" 198/200, v5d "flow" 65/200); both arms confabulate sleep/rr/temp slots on days where those channels are absent (though sparse days can drop schema slots entirely -- conditional slot emission partially works).

GOAL: restore deployment-distribution hr_raw accuracy to v4w's level (MAE <=6.14, its dense-draw level) and better, with floor-emission rate <10%, without regressing the dense population. The standing eval defect (draws must stratify or restrict to supabase) is part of this problem.

#deployment-distribution #optihealth #collapse #baseline
