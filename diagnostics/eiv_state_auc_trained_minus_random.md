---
direction: higher_better
id: eiv_state_auc_trained_minus_random
name: eiv_state_auc_trained_minus_random
noise_k: 2.0
noise_n: 4
noise_note: 'ASSUMED, and derived rather than measured. eiv_momentary_state_auc_over_null
  carries an assumed sigma of 0.0050 (itself an upper bound from four arm-vs-arm pairs
  on identical rows). A DIFFERENCE of two over_null values measured on the same rows,
  folds and permutation seeds has sigma <= sqrt(2)*0.0050 = 0.0071 if the two are
  independent, and strictly less than that when they share rows -- which they do,
  by construction, since the random twin is the same architecture on the same 2,136
  rows. So 0.0071 is a conservative bound and k=2 gives a band of +/-0.0141. Replace
  with a real repeat-run sigma before promoting anything on this diagnostic: that
  needs the same recipe retrained at >=3 seeds with the twin re-drawn each time, which
  is GPU work nobody has spent.'
noise_sigma: 0.0071
noise_source: assumed
requires_control: eiv_momentary_state_auc_over_null
topic_tags:
- control
- baseline
- readout
unit: auc
---

## Description
The momentary-state signal a TRAINING RUN ADDED, isolated from the signal its architecture already had: `over_null(trained) - over_null(random_init_twin)`, where the twin rebuilds the identical geometry (patch_len, stride, d_model, fusion, slot count) with seeded random weights and is scored on identical rows, identical GroupKFold folds, identical within-window permutation seeds and the identical readout family.

WHY IT EXISTS. A permutation null answers "could these labels have been shuffled?" against ONE frozen encoder; it cannot answer "did training put the signal there?". references/null-and-control-design.md rule 1 records three independent targets on this encoder where the random-init twin matched or beat the trained arm while the trained arm's own permutation z read +8.88 at p=0.0000. Any claim of the form "the encoder carries X" is unfalsifiable without this number beside it.

READOUT-DEPENDENT, like its parent. Report it per readout family and name the family in every quoted value; a trained-minus-random delta measured under one estimator does not transfer to another.

*Unit:* `auc` — *higher_better*

#control #baseline #readout
