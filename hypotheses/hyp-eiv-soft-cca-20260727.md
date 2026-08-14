---
created_at: '2026-07-27T21:04:10+00:00'
gates: []
id: hyp-eiv-soft-cca-20260727
interventions:
- eiv-soft-cca
paper_refs:
- paper-softcca-1707.09669
- paper-cka-1905.00414
predicted_effects:
- diagnostic_id: eiv_heldout_hr_r2
  direction: up
  magnitude_note: Median paired improvement is at least +0.02 R2 versus reproduced
    DCL.
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Effective rank increases and remains above 5.
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
- soft-cca
- decorrelation
- multiview
- contrastive
- hsic
- cca
- vicreg
- wearable-ml
---

## Statement
Soft CCA will improve held-out-subject raw-HR decodability over DCL by aligning paired standardized views while explicitly suppressing redundant dimensions.

## Rationale
Exact DCCA whitening is fragile for finite batches and 2560-dimensional views. Soft CCA's loss-only 128D projections and EMA covariance penalty provide a numerically stable multiview objective while leaving inference architecture unchanged.

## Plan
Select decorrelation weight in {0.1,0.5,1.0} using seed 17 and validation subjects only; confirm the selected native replacement over eight paired seeds on locked test subjects.

## Links
Tests problem [[optihealth-contrastive-loss-20260727]].
Applies [[eiv-soft-cca]].
Predicts [[eiv_heldout_hr_r2]] (up).
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_duplicate_recall5]] (none).
Cites [[paper-softcca-1707.09669]], [[paper-cka-1905.00414]].

#soft-cca #decorrelation #multiview #contrastive #hsic #cca #vicreg #wearable-ml #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
