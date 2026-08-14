---
created_at: '2026-07-28T10:34:37+00:00'
gates: []
id: hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728
interventions:
- eiv-dcl-denominator-only
paper_refs:
- paper-dcl-2110-06848
predicted_effects:
- diagnostic_id: eiv_duplicate_recall5
  direction: up
  magnitude_note: Primary. Pilot mean paired DCL-minus-hard-InfoNCE delta must be
    >=+0.005 with at least 2/3 positive seeds. Confirmation requires mean >=+0.010,
    all precommitted statistical gates, and at least 3/5 nonnegative seeds.
- diagnostic_id: eiv_crossmodal_median_rank
  direction: down
  magnitude_note: DCL should improve or preserve retrieval ordering; pilot mean paired
    regression may not exceed +2 ranks and no confirmation seed may regress by more
    than +2.
- diagnostic_id: eiv_effective_rank
  direction: none
  magnitude_note: Denominator decoupling is not expected to directly solve low native
    rank. Require pilot mean delta >=-0.25 and track the separate deployment gate
    of every DCL rank>=5.
- diagnostic_id: eiv_sensor_median_cosine
  direction: none
  magnitude_note: No directional collapse expected; every DCL run must remain <=0.90,
    with paired changes treated as secondary.
problem_id: optihealth-contrastive-loss-20260727
problem_tags:
- matched-baseline
- optihealth-eiv
- contrastive-loss
- subject-heldout
- representation-learning
status: proposed
supersedes: hyp-eiv-dcl-baseline-20260727
topic_tags:
- infonce
- dcl
- denominator
- contrastive
- hsic
- cca
- vicreg
- wearable-ml
- optihealth-eiv
- matched-baseline
---

## Statement
On the exact fix/soft-token-norm-scale Phase-A pipeline, replacing symmetric hard InfoNCE with DCL in both the main and auxiliary terms—while changing no other training or evaluation behavior—will improve duplicate-aware subject-held-out cross-modal retrieval.

## Rationale
The earlier DCL study established learning versus initialization but never included a matched hard-InfoNCE arm. The legacy checkpoint labeled InfoNCE is not valid evidence because its profile had decoupled_loss=true before creation and its saved configuration omitted that flag. A fresh denominator-only comparison is required. DCL removes positive-negative coupling and may improve optimization at the effective batch of 102, but the effect must be demonstrated under identical initialization, batches and validation indices rather than inferred from literature or mismatched checkpoints.

## Plan
Base source: fix/soft-token-norm-scale@955c1b5 plus only reproducibility/campaign controls. Hard InfoNCE and DCL both use soft_assignment=false; DCL changes only decoupled_loss for main and auxiliary losses. Freeze corrected mask, existing subject-disjoint split, population_stats_mode=disabled, bf16 RTX 5070, batch 102, 500 steps, fixed temperature 0.03, AdamW-8bit 1e-4 with same scheduler, uniformity weight 3.0, auxiliary weight 0.2, identical 192→2560 architecture and MedGemma last-token text cache. Engineering smoke seed17 excluded. Paired validation pilot seeds 17/23/29, all six runs sequential before aggregation. Pilot superiority gate: mean DCL-minus-InfoNCE Recall@5 >=+0.005 and at least 2/3 positive; noninferiority gates mean median-rank delta <=+2, mean effective-rank delta >=-0.25, every DCL cosine<=0.90, finite/no OOM, VRAM<11.25GiB, runtime<=110%, identical init/eval/source/protocol hashes and identical false-negative exposure. Confirmation only if earned: seeds31/37/41/43/47, mean Recall@5 delta>=+0.010, exact one-sided sign-flip p<=0.05, deterministic paired bootstrap CI lower>0, same safety gates. Deployment remains a separate absolute gate requiring every DCL effective rank>=5. Locked test remains untouched unless confirmation passes.

## Links
Tests problem [[optihealth-contrastive-loss-20260727]].
Applies [[eiv-dcl-denominator-only]].
Predicts [[eiv_duplicate_recall5]] (up).
Predicts [[eiv_crossmodal_median_rank]] (down).
Predicts [[eiv_effective_rank]] (none).
Predicts [[eiv_sensor_median_cosine]] (none).
Cites [[paper-dcl-2110-06848]].
Supersedes [[hyp-eiv-dcl-baseline-20260727]].

#infonce #dcl #denominator #contrastive #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #matched-baseline #optihealth-eiv #contrastive-loss #subject-heldout #representation-learning
