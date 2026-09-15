---
direction: higher_better
id: eiv_state_auc_pool_ceiling
name: eiv_state_auc_pool_ceiling
noise_k: 2.0
noise_n: 6
noise_note: 'MEASURED 2026-09-03 in exp-c1-heldout-pool-ceiling-20260903. Protocol:
  the winning arm (union_ms/gb) refit under the plain GroupKFold partition plus 5
  SEEDED subject-to-fold partitions on the identical rows, labels and permutation
  nulls -- so the sd is fold-assignment noise, which is the noise this diagnostic
  actually carries, and not seed-of-training noise (nothing is trained). sd(ddof=1)
  over the 6 partitions = 0.0155 AUC, k=2 band +/-0.031. CAVEATS: (1) measured on
  the 666-row held-out pool; the 2,136-row clean pool will be tighter, so using 0.0155
  there is conservative. (2) The tree arms vary more than the linear ones -- raw_multiscale/rf
  reads sd 0.0294, raw_shallow/ridge 0.0053 -- so a cell-specific sd is in every g5
  output''s `fold_auc_sd` field and should be preferred over this pooled figure when
  comparing two tree arms.'
noise_sigma: 0.0155
noise_source: measured
requires_control: eiv_state_auc_trained_minus_random
topic_tags:
- optihealth
- readout
- control
- state-supervision
unit: AUC over own permutation null
---

## Description
The BEST momentary-state (flow vs stress) AUC over own within-window permutation null that ANY readout reaches on a stated pool, reported as over_null with the AUC beside it -- the number every trained representation is priced against. Defined by scratch/g5_pool_ceiling.py: same rows as g3 (2,136 minute-cells / 666 windows / 113 subjects on the clean pool), GroupKFold by subject, each arm with its OWN permutation null (default_rng(1000+s)); arms are raw_shallow (15 HR/HRV features), raw_multiscale (same 15 at W in {30,120,240}), raw_grid, the encoder at one and three spans, its random-init twin, and the unions. Reported under TWO estimator families minimum (ridge and gb/rf) -- rule 2 of null-and-control-design.md; a single-family ceiling is not a ceiling. First value 2026-09-02: union_ms/gb +0.1649 (AUC 0.7211), raw_multiscale/rf +0.1515 (0.7049), best encoder-only +0.0852. Published context: wrist HRV-only LOSO on mixed lab stressors reads AUROC 0.73 (Amin 2025, arXiv:2505.05694), so 0.72 is at the literature level for this input set without EDA. The pool must be named with every value: the clean g3 pool and the 1,485-row split-honest held-out pool are different populations and are not interchangeable.

*Unit:* `AUC over own permutation null` — *higher_better*

#optihealth #readout #control #state-supervision
