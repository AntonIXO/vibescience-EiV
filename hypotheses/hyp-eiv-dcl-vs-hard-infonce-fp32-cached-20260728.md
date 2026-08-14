---
created_at: '2026-07-28T11:19:35+00:00'
former_ids: []
gates: []
id: hyp-eiv-dcl-vs-hard-infonce-fp32-cached-20260728
interventions:
- eiv-dcl-denominator-only
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_duplicate_recall5
  direction: up
  magnitude_note: Pilot mean paired DCL-InfoNCE delta >= +0.005; confirmation target
    >= +0.010.
- diagnostic_id: eiv_crossmodal_median_rank
  direction: down
  magnitude_note: Pilot mean regression <=+2; confirmation every seed <=+2.
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Mean paired delta >=-0.25; independent test unlock requires every
    DCL rank >=5.
- diagnostic_id: eiv_sensor_median_cosine
  direction: down
  magnitude_note: Every DCL value <=0.90.
- diagnostic_id: eiv_false_negative_pairs_per_batch
  direction: none
  magnitude_note: Exactly identical paired one-positive false-negative exposure.
- diagnostic_id: eiv_training_runtime_sec
  direction: none
  magnitude_note: DCL/InfoNCE ratio <=1.10.
- diagnostic_id: eiv_peak_vram_gib
  direction: none
  magnitude_note: Both below 11.25 GiB reserved with no OOM.
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: Secondary only; no superiority claim.
problem_id: optihealth-contrastive-loss-20260727
problem_tags:
- optihealth-eiv
- matched-baseline
- historical-pipeline
- contrastive-loss
- subject-heldout
- representation-learning
status: inconclusive
supersedes: hyp-eiv-dcl-vs-hard-infonce-fp32-20260728
topic_tags:
- contrastive
- dcl
- infonce
- cross-modal
- float32
- authenticated-cache
- rtx-5070
- hsic
- cca
- vicreg
- wearable-ml
- optihealth-eiv
- matched-baseline
- denominator
---

## Statement
With the exact MedGemma training and validation caption-embedding corpora authenticated before GPU work, DCL will improve duplicate-aware subject-held-out Recall@5 over matched hard one-positive InfoNCE at 500 historical-regime float32 Phase-A steps.

## Rationale
The superseded experiment was inconclusive because both arms OOMed before initialization while redundantly loading fp32 MedGemma for an empty path-fingerprinted cache. This revision does not change any text target: the 4,962 training and 401 validation embeddings are exact filename- and byte-identical corpora already produced for the same MedGemma model, last-token pooling, max length 96, captions, corrected mask, and split. Their file counts and stable corpus hashes are now frozen and verified before GPU preflight. Thus the change removes a symmetric infrastructure failure without changing the tested loss contrast.

## Plan
New immutable _v2 root. Before each run require training text-cache count/hash 4962/7afdd8b1...bb9a1 and validation count/hash 401/e71f74fb...d5a46; runtime precompute forbidden. Smoke seed17, two steps per arm, excluded. Then paired pilot seeds17/23/29, 500 steps, float32, batch102, identical full initialization/exposure/evaluation; only decoupled_loss and paths differ. Pilot and conditional confirmation/test gates are unchanged from the superseded hypothesis. No reuse, deletion, or retry of the failed _v1 run. Scope remains 500 steps, not legacy 2,000-step convergence.

## Links
Tests problem [[optihealth-contrastive-loss-20260727]].
Applies [[eiv-dcl-denominator-only]].
Predicts [[eiv_duplicate_recall5]] (up).
Predicts [[eiv_crossmodal_median_rank]] (down).
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_sensor_median_cosine]] (down).
Predicts [[eiv_false_negative_pairs_per_batch]] (none).
Predicts [[eiv_training_runtime_sec]] (none).
Predicts [[eiv_peak_vram_gib]] (none).
Predicts [[eiv_heldout_hr_r2]] (none).
Supersedes [[hyp-eiv-dcl-vs-hard-infonce-fp32-20260728]].

#contrastive #dcl #infonce #cross-modal #float32 #authenticated-cache #rtx-5070 #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #denominator #optihealth-eiv #matched-baseline #historical-pipeline #contrastive-loss #subject-heldout #representation-learning
