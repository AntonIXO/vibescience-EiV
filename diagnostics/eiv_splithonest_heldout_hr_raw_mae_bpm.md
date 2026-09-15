---
direction: lower_better
id: eiv_splithonest_heldout_hr_raw_mae_bpm
name: eiv_splithonest_heldout_hr_raw_mae_bpm
noise_k: 2.0
noise_n: 2
noise_note: 'MEASURED 2026-09-03 from the v41rebased seed family, and the FIRST split-honest
  repeat-run sigma this project has ever had. Protocol: the identical three-stage
  PL60 recipe re-run end to end with ONE key changed (seed 42 -> 43) and everything
  else asserted byte-identical by the driver''s two-key config check (scratch/run_v41_seedfamily.sh),
  each chain scored by the same instrument on the same 128-row draw at --split heldout.
  Seed 42 hr_raw 2.843 (scale 0.992, corr 0.924); seed 43 hr_raw 2.549 (scale 1.254,
  corr 0.856). mean 2.696, sd(ddof=1) 0.208, k=2 band +/-0.416. THREE CAVEATS THAT
  TRAVEL WITH IT: (1) n=2, so the sd is itself uncertain -- it is a floor to price
  arms against, not a precise number, and the third seed was deliberately not bought
  (Anton''s single-seed steer, 2026-09-03). (2) The two seeds differ more in CALIBRATION
  than in MAE (scale 0.992 vs 1.254, corr 0.924 vs 0.856), so an arm that moves MAE
  inside this band while moving scale toward 1.0 is not automatically a null. (3)
  This replaces g1''s inherited mde of 0.7, which came from no split-honest repeat
  at all.'
noise_sigma: 0.208
noise_source: measured
requires_control: eiv_gen_between_sample_std
topic_tags: []
unit: bpm
---

## Description
hr_raw MAE in bpm on the 1,485-row val+test pool of data/eval_v3_1min, for an arm whose CONSUMER was trained under `split: train`. Distinct from eiv_dense128_hr_raw_mae_bpm on two axes that both matter: (a) the pool is the whole 1,485 rows rather than a 128-row draw from it, and (b) the training side honoured the subject split, which no arm before 2026-09-01 did -- 125 of the 128 rows in every prior "held-out" capture belong to subjects the model trained on (rule-out that-the-scorer-s-split-heldout-flag-makes-the-d88faa). Arithmetic is emission_diversity_audit.py's own summarise(): MAE against the masked-mean HR of the window in bpm, denormalised with the eval bundle's population_stats, with the trivial always-predict-median MAE recomputed ON THE SCORED ROWS and reported beside it. RESIDUAL CAVEAT that any user of this number must carry: Phase A and Phase B still ran with split=None on every arm to date, so the frozen tower has seen these subjects even when the consumer has not. This is honest about the CONSUMER and optimistic about the ENCODER.

*Unit:* `bpm` — *lower_better*
