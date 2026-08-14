---
created_at: '2026-07-27T21:04:10+00:00'
gates: []
id: hyp-eiv-dcl-hsic-20260727
interventions:
- eiv-dcl-package
- eiv-dcl-rbf-hsic
paper_refs:
- paper-dcl-2110.06848
- paper-cka-1905.00414
predicted_effects:
- diagnostic_id: eiv_heldout_hr_r2
  direction: up
  magnitude_note: Median paired improvement is at least +0.02 R2 versus reproduced
    DCL.
- diagnostic_id: eiv_crossmodal_hsic
  direction: up
  magnitude_note: Held-out RBF-HSIC increases versus DCL.
- diagnostic_id: eiv_duplicate_recall5
  direction: none
  magnitude_note: Recall@5 regression is no worse than -0.02 absolute.
problem_id: optihealth-contrastive-loss-20260727
problem_tags:
- contrastive-loss
- subject-heldout
- optihealth-eiv
- representation-learning
status: confirmed
supersedes: null
topic_tags:
- hsic
- kernel
- dcl
- contrastive
- cca
- vicreg
- wearable-ml
---

## Statement
Adding an unbiased RBF-HSIC dependence reward to the full DCL package will improve held-out-subject raw-HR decodability without materially degrading duplicate-aware retrieval.

## Rationale
Pairwise contrastive classification may underuse nonlinear shared structure at batch size 102. HSIC directly rewards nonlinear dependence between paired sensor and frozen-text views while DCL retains instance discrimination.

## Plan
Select HSIC weight in {0.03,0.1,0.3} and bandwidth multiplier in {0.5,1,2} using seed 17 and validation subjects only. Confirm the selected configuration against reproduced DCL over eight paired seeds on locked test subjects.

## Links
Tests problem [[optihealth-contrastive-loss-20260727]].
Applies [[eiv-dcl-package]], [[eiv-dcl-rbf-hsic]].
Predicts [[eiv_heldout_hr_r2]] (up).
Predicts [[eiv_crossmodal_hsic]] (up).
Predicts [[eiv_duplicate_recall5]] (none).
Cites [[paper-dcl-2110.06848]], [[paper-cka-1905.00414]].

#hsic #kernel #dcl #contrastive #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
