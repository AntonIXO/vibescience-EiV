---
created_at: '2026-07-27T22:28:45+00:00'
former_ids: []
gates: []
id: hyp-eiv-native-kta-vicreg-20260727
interventions:
- eiv-native-rbf-kta-vicreg
paper_refs:
- paper-cka-1905.00414
- paper-vicreg-2105.04906
predicted_effects:
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Seed-17 500-step pilot best config reaches effective rank >=6.0,
    and must in all cases clear the original unrelaxed rank>=5 gate.
- diagnostic_id: eiv_duplicate_recall5
  direction: none
  magnitude_note: Best config duplicate-aware Recall@5 >=0.08723 (no more than 0.02
    below DCL pilot 0.10723).
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: Best config held-out-subject raw-bpm HR R2 >=0.94.
- diagnostic_id: eiv_crossmodal_hsic
  direction: up
  magnitude_note: Expected to exceed the projected VICReg default 0.05651 if native
    kernel alignment is effective; secondary diagnostic only.
- diagnostic_id: eiv_peak_vram_gib
  direction: none
  magnitude_note: No OOM and peak reserved VRAM <11.25 GiB.
problem_id: optihealth-contrastive-loss-20260727
problem_tags:
- contrastive-loss
- subject-heldout
- representation-learning
- optihealth-eiv
status: confirmed
supersedes: hyp-eiv-vicreg-20260727
topic_tags:
- contrastive
- hsic
- cca
- vicreg
- wearable-ml
- kernel-alignment
- cross-modal
- anti-collapse
---

## Statement
Applying duplicate-aware RBF kernel-target alignment and variance/covariance regularization directly to the evaluated native embeddings, with no learned loss head, will clear the original collapse gate while retaining useful cross-modal retrieval and HR decodability.

## Rationale
The first campaign showed projected SoftCCA/VICReg can satisfy loss-head objectives while native representations collapse. DCL/HSIC fixed cosine but remained below rank 5. Direct native regularization closes the bypass; duplicate-aware cross-view RBF kernel-target alignment preserves nonlinear paired structure without reintroducing false negatives for identical captions. Exact covariance Frobenius penalties can be computed through the batch Gram, avoiding a 2560x2560 allocation.

## Plan
Create a new frozen follow-up output root. Unit-test duplicate-aware KTA, collapse gradients, exact Gram covariance identity, and finite bf16-compatible float32 math. Run an engineering 2-step RTX smoke, then seed17 validation-only 500-step pilots sequentially for one grid only: native regularizer weight lambda in {0.3,1.0,3.0}; fixed RBF bandwidth multiplier 1, gamma 1, otherwise the original raw-tensor split/batch/bf16 protocol. Go to confirmation only if a config has effective rank>=6.0, median native cosine<=0.80, alignment gap>=0.05, Recall@5>=0.08723, HR R2>=0.94, no OOM, and peak reserved VRAM<11.25 GiB. Otherwise stop.

## Links
Tests problem [[optihealth-contrastive-loss-20260727]].
Applies [[eiv-native-rbf-kta-vicreg]].
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_duplicate_recall5]] (none).
Predicts [[eiv_heldout_hr_r2]] (none).
Predicts [[eiv_crossmodal_hsic]] (up).
Predicts [[eiv_peak_vram_gib]] (none).
Cites [[paper-cka-1905.00414]], [[paper-vicreg-2105.04906]].
Supersedes [[hyp-eiv-vicreg-20260727]].

#contrastive #hsic #cca #vicreg #wearable-ml #kernel-alignment #cross-modal #anti-collapse #contrastive-loss #subject-heldout #representation-learning #optihealth-eiv
