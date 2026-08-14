---
created_at: '2026-07-27T21:04:10+00:00'
former_ids: []
gates: []
id: hyp-eiv-vicreg-20260727
interventions:
- eiv-vicreg
paper_refs:
- paper-vicreg-2105.04906
- paper-align-uniform-2005.10242
predicted_effects:
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: Median paired R2 change remains within +/-0.02 versus reproduced
    DCL.
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Effective rank is above 5 and increases versus untrained.
- diagnostic_id: eiv_duplicate_recall5
  direction: down
  magnitude_note: Recall@5 is lower than DCL due to lack of explicit negatives.
problem_id: optihealth-contrastive-loss-20260727
problem_tags:
- contrastive-loss
- subject-heldout
- optihealth-eiv
- representation-learning
status: refuted
supersedes: null
topic_tags:
- vicreg
- control
- collapse
- contrastive
- hsic
- cca
- wearable-ml
- decorrelation
---

## Statement
VICReg will prevent representation collapse but will not materially outperform DCL on held-out-subject raw-HR decodability because it lacks DCL's explicit instance discrimination.

## Rationale
VICReg is a collapse-control objective. Its variance/covariance terms should preserve dimensionality, but paired MSE alone may be less well matched to direct sensor-text retrieval and physiological caption discrimination.

## Plan
Select covariance weight in {0.5,1.0,2.0} using seed 17 and validation subjects only; confirm the selected native replacement over eight paired seeds on locked test subjects.

## Links
Tests problem [[optihealth-contrastive-loss-20260727]].
Applies [[eiv-vicreg]].
Predicts [[eiv_heldout_hr_r2]] (none).
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_duplicate_recall5]] (down).
Cites [[paper-vicreg-2105.04906]], [[paper-align-uniform-2005.10242]].

#vicreg #control #collapse #contrastive #hsic #cca #wearable-ml #decorrelation #contrastive-loss #subject-heldout #optihealth-eiv #representation-learning
