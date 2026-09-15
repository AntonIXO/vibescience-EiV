---
direction: lower_better
id: eiv_dense128_hr_raw_mae_bpm
name: eiv_dense128_hr_raw_mae_bpm
noise_k: 2.0
noise_n: 6
noise_note: 'Measured 2026-09-01 on three same-recipe 3-seed families already on disk
  (scratch/_dense_mae_noise_floor.py, scratch/_seed_family_heterogeneity.py). Per-family
  seed SD: ab_ctrl 4.357 (s42 10.049 / s44 2.562 / s45 10.168), w6avg 0.478 (3.560/2.642/3.334),
  w6msk 0.757 (3.947/2.439/3.301). The families disagree 9.11x, so a naive pooled
  sigma of 2.5683 is NOT registered.


  ab_ctrl''s two high seeds were diagnosed rather than averaged in. They are not broken
  captures: json_ok 128/128, distinct 36 and 35, top1 0.102 and 0.125, corr 0.708
  and 0.702 -- well-formed, diverse, correlated emissions. What separates them is
  SCALE: emit_sd/true_sd is 2.33 and 2.17 against 0.95-1.33 on all seven other captures.
  They read the day correctly and then over-express the range, which is a calibration
  failure with its own signature, not seed jitter. Pooling them would have set sigma
  at 2.57 bpm -- wider than the entire asserted no-harm band -- and made every future
  arm on this diagnostic unfalsifiable.


  Registered sigma 0.7566 is the pooled within-family SD of the two families whose
  scale is intact (w6avg, w6msk; 6 seeds, dof 4), i.e. the seed floor CONDITIONAL
  on a calibrated arm. Any arm with emit_sd/true_sd outside ~[0.85, 1.35] is outside
  this floor''s domain and must be judged on the scale defect first.'
noise_sigma: 0.7566
noise_source: measured
requires_control: null
topic_tags: []
unit: bpm
---

## Description
Mean absolute error in bpm between the emitted hr_raw slot and the window's true daily mean HR on the frozen dense 128-row draw (greedy decode, batch 1, repeat-key stop criterion). THE NO-HARM GATE OF THE v28-v38 CAMPAIGN RIDES ON THIS DIAGNOSTIC.

*Unit:* `bpm` — *lower_better*
