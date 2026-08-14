---
created_at: '2026-08-08T04:28:13+00:00'
id: phase-b-triplet-orthogonalization-is-close-to-vacuous-99-1-of-its-training-samples-carry-a-single-state-label-and-every-non-none-label-is-a-100-pure-dataset-identifier
paper_refs: []
problem_tags: []
status: open
title: 'Phase B triplet orthogonalization is close to vacuous: 99.1% of its training
  samples carry a single state label, and every non-NONE label is a 100%-pure dataset
  identifier'
topic_tags:
- dataset-whitelist
- contrastive
- collapse
- baseline
updated_at: '2026-08-08T04:28:13+00:00'
---

## Description
Found 2026-08-08 while closing the dataset-whitelist hypothesis. Unpreregistered mechanism audit;
problem evidence, no calibration weight. Script scratch/h1_per_phase_mechanism.py.

Phase B (`phase_b_12ch`) adds an auxiliary TRIPLET orthogonalization term on top of the Phase A
InfoNCE objective, with a dynamic margin. Triplets are mined on `state_label`. Measured over the
7037 samples that survive the Phase A/B gate on the 1-minute corpus:

  state=NONE         n=6973   99.1% of the corpus   (dominant dataset lifesnaps, purity 0.668)
  state=exam_stress  n=  20   purity 1.0000  -> wearable_exam_stress
  state=transient    n=  15   purity 1.0000  -> wesad
  state=neutral      n=  13   purity 1.0000  -> big_ideas_glycemic
  state=n_back       n=   8   purity 1.0000  -> brain_wearable_monitoring
  state=high         n=   6   purity 1.0000  -> nurse_stress
  state=medium       n=   2   purity 1.0000  -> nurse_stress

TWO INDEPENDENT DEFECTS, EITHER OF WHICH IS SUFFICIENT ON ITS OWN.
1. 99.1% of samples share ONE label. A triplet objective needs an anchor, a same-label positive and
   a different-label negative. Within the NONE mass there is no genuine positive/negative structure
   at all -- every pair is nominally "same state".
2. Every one of the 64 labelled samples has a label that is a PERFECT dataset identifier
   (purity exactly 1.0000 in all six cases). So the only triplets carrying real label contrast are
   triplets whose contrast IS dataset provenance. The margin cannot be spent separating
   physiological state, because the labels do not encode physiological state -- they encode which
   study the row came from. Phase B is therefore, at best, orthogonalising dataset identity.

This compounds the Phase A finding recorded on the epoch-resolution problem: dataset provenance is
already ~99.7% decodable from per-channel coverage alone, so the representation entering Phase B
carries a near-perfect provenance axis, and Phase B's labels then ask it to separate exactly that.

NOT EXPLAINED BY THE GATE BUG. Commit e828b9e fixed a separate Phase B defect (no min_hr_coverage,
so 1981 HR-empty days were admitted, 91.5% at exactly zero HR coverage). The label degeneracy above
is measured AFTER that gate and is unaffected by it.

WHAT IS NOT YET KNOWN, STATED HONESTLY: this is a defect in the TRAINING SIGNAL, not yet a
demonstrated harm to any metric. Phase B measurably does no damage on the current instrument -- the
1-minute Phase B checkpoint scores hr_peak increment +0.4182 versus Phase A's +0.4086, and hr_mean
0.9777 versus 0.9713, i.e. slightly BETTER on both. So the honest statement is that Phase B is
running a nearly-empty objective and is roughly neutral, not that it is actively harmful. The open
questions are (a) whether Phase B is worth its ~10 minutes of GPU at all, measured by an ablation
that simply skips it, and (b) whether a genuine state label exists anywhere in the corpus that is
not a dataset proxy -- if not, the triplet arm has nothing to learn from and should be retired or
re-grounded on a label that is not provenance.

#dataset-whitelist #contrastive #collapse #baseline
