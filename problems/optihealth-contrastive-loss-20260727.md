---
created_at: '2026-07-27T21:03:32+00:00'
former_ids: []
id: optihealth-contrastive-loss-20260727
paper_refs: []
problem_tags:
- contrastive-loss
- subject-heldout
- representation-learning
- optihealth-eiv
status: open
target: null
title: Which cross-modal objective best preserves raw heart-rate information in OptiHealth
  EiV?
topic_tags:
- contrastive
- hsic
- cca
- vicreg
- wearable-ml
updated_at: '2026-08-18T20:52:08+00:00'
updates: []
---

## Description
Compare the current DCL + uniformity + auxiliary-DCL Phase A package against RBF-HSIC dependence regularization, Soft CCA, and VICReg on frozen raw 12-channel tensors. Use an experiment-local correction for HR==0 values incorrectly marked present, deterministic dataset-stratified subject splits, validation-only pilot selection, and eight-seed subject-held-out confirmation on the local RTX 5070. Population stats are disabled because HR is schema-excluded and merge-time fitting before the test split would leak.

## Synthesis
STATE: the objective comparison is DONE at pilot scale; what remains open is not
which loss to use but whether retrieval can move at all at this scale.

WHAT WE NOW BELIEVE.
- Native KTA+VICReg is the best of the four candidates on the primary
  (eiv_duplicate_recall5): the trajectory rises 0.0175 -> 0.1072 -> 0.1197 ->
  0.1212 across exp-eiv-dcl-20260727, exp-eiv-native-kta-vicreg-20260727 and
  exp-014. Plain VICReg (0.0075) and Soft CCA (0.0125) both COLLAPSE retrieval
  by ~10x versus the DCL package and are not candidates.
- The multi-positive construction moves GEOMETRY, not retrieval. In
  exp-eiv-multipositive-spectral-entropy-pilot-20260728: effective rank
  +0.1368 (4.3809 -> 4.5177), spectral entropy up, caption-equivalent false
  negatives held at exactly 0.0 — while duplicate recall@5 moved by EXACTLY
  0.0 (0.10723192 -> 0.10723192) against a preregistered >= +0.005 bar, and
  cross-modal median rank went the WRONG way (38 -> 39, predicted down).
  Removing false negatives from the denominator is therefore not sufficient
  for a retrieval gain at 500 steps.

WHAT IS RULED OUT.
- Soft CCA and plain VICReg as the Phase-A objective (measured, above).
- Inheriting a mechanism gate that is satisfied BY CONSTRUCTION as evidence:
  the exact-caption equivalence set cannot fail its own false-negative gate, so
  that gate carries no evidential weight (see exp-multipositive-dcl-resolved-
  via-spectral-supersession-20260818's interpretation).

WHAT IS UNTESTED — AND MUST NOT BE READ AS REFUTED.
- DCL vs hard InfoNCE on soft tokens NEVER RAN. Both arms of
  exp-eiv-dcl-vs-hard-infonce-fp32-pilot-20260728 OOMed symmetrically at 10.314
  GiB on the 11.51 GiB 5070 before step 1, while materialising 4,962 missing
  corrected-mask caption embeddings with fp32 MedGemma resident. The two
  hypotheses in that chain are `inconclusive` for a CAPACITY reason, not an
  evidential one. Reviving it needs the text-cache corpus materialised under a
  new campaign root first, so the embedding pass never coincides with a
  resident fp32 backbone.

CAVEAT ON EVERY NUMBER HERE. All of it is 500-step, seed-17, validation-only
pilot scale, and no noise floor has ever been measured on eiv_duplicate_recall5
(10 evaluations ride on it, doctor still flags it). The +0.0030 gap between the
KTA+VICReg arms is far inside any plausible seed band; treat the ordering
between the top arms as UNRESOLVED and the ordering against Soft CCA/VICReg as
solid because it is a 10x effect. Measuring a seed band on this diagnostic is
the cheapest thing that would sharpen this problem.

#contrastive #hsic #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #representation-learning #optihealth-eiv
