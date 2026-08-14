---
created_at: '2026-07-27T21:04:10+00:00'
former_ids: []
gates: []
id: hyp-eiv-dcl-baseline-20260727
interventions:
- eiv-dcl-package
paper_refs:
- paper-dcl-2110.06848
- paper-align-uniform-2005.10242
predicted_effects:
- diagnostic_id: eiv_heldout_hr_r2
  direction: up
  magnitude_note: Median subject-held-out raw-HR R2 increases by at least 0.20 over
    untrained.
- diagnostic_id: eiv_duplicate_recall5
  direction: up
  magnitude_note: Recall@5 increases over untrained.
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Effective rank remains above the collapse threshold of 5.
problem_id: optihealth-contrastive-loss-20260727
problem_tags:
- contrastive-loss
- subject-heldout
- optihealth-eiv
- representation-learning
status: refuted
supersedes: null
topic_tags:
- dcl
- baseline
- contrastive
- hsic
- cca
- vicreg
- wearable-ml
---

## Statement
The current DCL + uniformity + auxiliary-DCL package will learn substantially more raw-HR-predictive subject-held-out representations than the seed-matched untrained architecture.

## Rationale
DCL removes positive-negative coupling and the existing package was previously tuned for small-batch cross-modal alignment, but prior probes leaked subjects; the subject-held-out campaign tests whether its apparent physiological signal generalizes.

## Plan
Run 2000 Phase A steps for eight precommitted seeds on train+validation subjects, evaluate the locked test subjects with a grouped Ridge probe and duplicate-aware retrieval. The before value is the seed-matched untrained model evaluated through the identical harness.

## Links
Tests problem [[optihealth-contrastive-loss-20260727]].
Applies [[eiv-dcl-package]].
Predicts [[eiv_heldout_hr_r2]] (up).
Predicts [[eiv_duplicate_recall5]] (up).
Predicts [[eiv_effective_rank]] (up).
Cites [[paper-dcl-2110.06848]], [[paper-align-uniform-2005.10242]].

#dcl #baseline #contrastive #hsic #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
