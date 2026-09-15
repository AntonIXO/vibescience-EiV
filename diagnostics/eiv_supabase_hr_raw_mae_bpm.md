---
direction: lower_better
id: eiv_supabase_hr_raw_mae_bpm
name: eiv_supabase_hr_raw_mae_bpm
noise_k: 2.0
noise_n: 10
noise_note: "RE-MEASURED 2026-09-01 at the CURRENT error level (scratch/_supa_noise_floor.py).
  Previous value 3.145 is retained below because it is still correct AT ITS OWN LEVEL
  and must not be read as an error.\n\nPREVIOUS: v7noatom seeds 42/43/44 = 11.54/15.78/9.49,
  sigma taken as range/2 = 3.145, at a mean MAE of 12.27.\nNOW: three same-recipe
  families on the same 200-row draw, 10 seeds total --\n  ab_ctrl  6.031/2.836/3.075/4.174
  \  sd 1.456\n  w6avg    3.187/2.861/2.742         sd 0.230\n  w6msk    3.389/3.351/3.737
  \        sd 0.213\n  pooled within-family sigma 0.8601 (dof 7), grand mean MAE 3.484.\n\nTHE
  SPREAD IS PROPORTIONAL TO THE LEVEL, WHICH IS WHY THE OLD FIGURE DOES NOT TRANSFER.
  Coefficient of variation is 0.256 for v7noatom (3.145 at 12.27) and 0.247 for the
  current families (0.860 at 3.48) -- the same fractional spread at a level 3.5x lower.
  A sigma measured on arms erroring at ~12 bpm was being applied to arms erroring
  at ~3.\n\nWHAT THIS CHANGES. The synthesis on problem phase-c-arms-fail-on-the-deployment
  concluded: 'the seed band therefore EXCEEDS the trivial baseline (4.02 bpm): no
  single-seed arm comparison on this diagnostic is interpretable.' At the measured
  floor the k=2 band is +/-1.720 bpm, comfortably inside the 4.02 trivial baseline,
  so single-seed supabase comparisons between HEALTHY arms are interpretable again.
  That conclusion was correct for the arms it was drawn on and stops applying once
  the arms improved -- a noise floor is a property of a regime, not of a metric.\n\nIT
  ALSO DOES NOT RESCUE THE CAMPAIGN'S HEADLINE CLAIMS. Re-read against 0.8601: v33dispersion
  2.142 vs incumbent 3.030 is z -1.03, v34nopeak 2.095 is z -1.09, v31detached 2.699
  is z -0.38. All INSIDE noise at k=2. The supa wins that looked decisive on a single
  seed are directional only.\n\nESTIMATOR NOTE: the old figure used range/2 on n=3.
  The unbiased range-to-sigma factor at n=3 is d2=1.693, so range/2 understates the
  SD by about 15%; the replacement is a pooled within-family SD, not a range."
noise_sigma: 0.8601
noise_source: measured
requires_control: null
topic_tags:
- deployment-distribution
- optihealth
unit: bpm
---

## Description
Mean absolute error in bpm between the emitted hr_raw slot and the true daily mean HR on the frozen optihealth_supabase 200-row deployment draw (greedy decode, batch 1). This is the PRODUCTION population and the diagnostic every promotion decision in this project has been argued on.

*Unit:* `bpm` — *lower_better*

#deployment-distribution #optihealth
