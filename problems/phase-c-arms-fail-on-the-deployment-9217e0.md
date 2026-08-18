---
created_at: '2026-08-12T08:57:29+00:00'
former_ids:
- phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
id: phase-c-arms-fail-on-the-deployment-9217e0
paper_refs: []
problem_tags: []
status: open
target: null
title: 'Phase-C arms fail on the deployment distribution (optihealth_supabase): hr_raw
  floor atom at 30.0 plus unlearned sparsity handling'
topic_tags:
- deployment-distribution
- optihealth
- collapse
- baseline
updated_at: '2026-08-18T20:52:08+00:00'
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

## Synthesis
STATE: the deployment failure has TWO proven root causes, one is FIXED and
measured, and the problem stays open on the second.

WHAT WE NOW BELIEVE.
- ROOT CAUSE 1 (fixed, verified). The hr-augmentation clamp in
  src/training/datasets.py mapped the below-window tail of the uniform
  target_peak draw onto lo_peak exactly, so ~36.5% of augmented samples landed
  at exactly min_valid_hr_bpm=30.0 => ~14.6% of ALL hr_raw LM targets were the
  literal string "30.0". The supabase days are the lowest-HR, sparsest days,
  nearest that attractor. The atom fix (a71034c) removed it and the removal
  REPLICATES ACROSS SEEDS: exact-30 emissions 0/200 on seeds 42, 43 and 44
  (exp-v7noatom-seed-band-20260813, g1 PASS). Deployment MAE band narrowed
  11.37 -> 6.29 bpm (45%).
- The arms could always READ supabase once they escaped the floor: conditional
  on escaping, v4w n=81 MAE 4.20 corr 0.839, v5c n=50 MAE 3.65 (beats the 4.39
  trivial). The unconditional MAE conflated attractor capture with reading skill.
- The instrument was the real failure. Arm ranking REVERSED between the dense
  draw (97% lifesnaps+pmdata; v5c beat v4w 108/128, p<1e-4) and the production
  supabase draw (v4w 16.72 < v5c 20.83 < v5d 25.05, all far below the 4.02
  trivial). Anton's manual annotations detected this before any diagnostic did.
  A metric on the wrong population cannot gate a deployment decision.

THE HARD CONSTRAINT ON EVERYTHING MEASURED HERE.
eiv_supabase_hr_raw_mae_bpm now carries a MEASURED seed floor of sigma 3.145
(k=2 => 6.29 bpm band, from the post-fix v7noatom family 11.54/15.78/9.49).
The trivial always-median baseline on the same draw is 4.02 bpm. The seed band
therefore EXCEEDS the trivial baseline: no single-seed arm comparison on this
diagnostic is interpretable, and v7noatom's own 11.54 is one draw from an
unknown-width distribution. Its baseline designation rests on the STRUCTURAL
claim (floor rate 0.000 on all seeds), not on the point score — and it carries
a preregistered revocation gate, which is what makes it a finding and not a
preference.

WHAT IS STILL OPEN.
- ROOT CAUSE 2, coverage dilution, is a separate defect verified on a different
  regime and is NOT closed by the atom fix.
- The last five closed runs are 3 directional_only + 1 supports + 1
  inconclusive, i.e. sign-matched arms that did not clear their blocking gates.
  With the band above, "directional" on this problem means very little.
- 10 of 11 experiments here carry no interpretation; the lessons are still
  locked in the notes.

#deployment-distribution #optihealth #collapse #baseline
