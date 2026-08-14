---
created_at: '2026-08-07T21:46:06+00:00'
former_ids:
- training-the-encoder-on-the-1-minute-canonical-tensor-n-23-patches-and-the-2-hou
gates: []
id: training-the-encoder-on-the-1-minute-031c19
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: ''
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
problem_tags: []
status: confirmed
supersedes: null
topic_tags:
- epoch-resolution
- wearables
- baseline
- dataset-whitelist
---

## Statement
Training the encoder on the 1-MINUTE canonical tensor (N=23 patches and the 2-hour token span
held fixed; only within-patch content changes from 8 pre-averaged 15-minute means to 120 real minutes)
raises the readout of the daily HR PEAK above what the daily mean already implies, while leaving the
already-saturated hr_MEAN readout unchanged.

MECHANISM. The 15-minute merge destroyed the peak in the DATA, before any model touched it: median
daily peak 133.55 -> 110.84 bpm, 98.2% of days attenuated. No downstream architecture change can undo
an upstream deletion, which is why ten prior experiments on pooling, slot count and window length all
came back refuted or flat - they were all trying to recover information that was no longer in the
tensor. The 1-minute grid restores it, and the restored quantity is genuinely incremental rather than
a relabelling of level: R2(hr_peak <- day mean) is only 0.034/0.020/0.016 at coverage 0.5/0.7/0.9, and
the recovered peak is a sustained excursion (corr 0.943 with the 5-minute rolling max), not a spike.

WHY hr_MEAN MUST NOT MOVE, AND WHY THAT IS THE POINT. hr_mean is saturated at R2 ~0.975-0.98 with
MAE 0.69 bpm against a trivial-median baseline of 6.60 bpm - about 1.3% residual variance and no
headroom. A previous corpus upgrade was scored on hr_mean and correctly returned "a wash, marginally
worse" (-0.0043) purely because the instrument had no room to move. Predicting FLAT on hr_mean is
therefore a falsification test of my own instrument-headroom claim, not a hedge: if hr_mean moves
materially in either direction, my account of where the information lives is wrong.

COMPARATOR, STATED EXPLICITLY. The 15-minute-trained encoder CANNOT be run on the 1-minute tensor
(patch_len 8 vs 120 are different weight shapes), so a same-weights before/after is impossible. The
honest comparators are therefore (i) the level-only control measured on the identical 1-minute strict
clean eval pool, R2(peak <- day mean) = 0.034, which is the number the model must beat, and (ii) the
lossless upper bound obtainable from the raw 1440 values on the same pool. The 15-minute-era recorded
peak figures are NOT directly comparable because the corpus, the eval pool and the bucket cut points
(146/112 corpus-derived at 1 minute vs 120/100 clinical) all differ; any cross-grid comparison must
restate the cut points.

STRICT clean eval pool (no zero-bpm present step, day-mean>40, HR coverage>0.5): n=6624.

## Rationale


## Plan


## Links
Tests problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Predicts [[eiv_heldout_hr_r2]] (none).

#epoch-resolution #wearables #baseline #dataset-whitelist
