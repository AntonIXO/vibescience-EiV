---
created_at: '2026-07-28T11:09:20+00:00'
gates: []
id: hyp-eiv-dcl-vs-hard-infonce-fp32-20260728
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
  magnitude_note: DCL expected no worse; pilot mean regression must be <= +2 ranks
    and confirmation every seed <= +2.
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: DCL expected to preserve or improve geometry; mean delta must be
    >= -0.25, with independent deployment requirement every DCL rank >=5.
- diagnostic_id: eiv_sensor_median_cosine
  direction: down
  magnitude_note: No directional collapse; every DCL value <=0.90.
- diagnostic_id: eiv_false_negative_pairs_per_batch
  direction: none
  magnitude_note: Exactly identical exposure between paired arms because both remain
    one-positive objectives.
- diagnostic_id: eiv_training_runtime_sec
  direction: none
  magnitude_note: DCL/InfoNCE wall-clock ratio <=1.10.
- diagnostic_id: eiv_peak_vram_gib
  direction: none
  magnitude_note: Both arms below 11.25 GiB reserved with no OOM.
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: Secondary only because this probe is saturated by untrained features;
    no superiority claim.
problem_id: optihealth-contrastive-loss-20260727
problem_tags:
- optihealth-eiv
- matched-baseline
- historical-pipeline
- contrastive-loss
- subject-heldout
- representation-learning
status: inconclusive
supersedes: hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728
topic_tags:
- contrastive
- dcl
- infonce
- cross-modal
- float32
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
At 500 Phase-A optimizer steps in the actual historical float32 execution regime, replacing symmetric hard one-positive InfoNCE with DCL in both the main and auxiliary terms, while holding every paired training exposure and evaluation input fixed, will improve duplicate-aware subject-held-out cross-modal Recall@5.

## Rationale
The legacy checkpoint labeled InfoNCE was actually DCL, so a true hard-InfoNCE checkpoint does not exist. The prior bf16 proposal is superseded because audit of run_12ch_pipeline.sh, cli_train.py, configs/training.yaml, and checkpoint tensor dtypes showed that Phase A ignored the YAML bf16 field and trained in float32. The current comparison retains scientific corrections: deterministic subject-disjoint splits, corrected HR-zero sentinel mask, no stale population stats, and no best-of-retry selection.

## Plan
Freeze source/protocol/implementation hashes. Engineering smoke: seed 17, 2 steps per arm, excluded from inference. Pilot: paired seeds 17/23/29, 500 steps, actual stratified batch 102, float32, same initialization/batches/augmentations/captions; soft_assignment=false in both arms; only decoupled_loss and output paths differ. Primary pilot gate: mean DCL-InfoNCE duplicate-aware Recall@5 >=0.005 and >=2/3 positive. Geometry/resource gates: mean median-rank delta <=+2, mean effective-rank delta >=-0.25, every DCL median cosine <=0.90, identical false-negative exposure, no OOM/nonfinite, peak reserved VRAM <11.25 GiB, DCL runtime ratio <=1.10. If passed, confirmation seeds 31/37/41/43/47 with mean Recall@5 delta >=0.010, >=3/5 nonnegative, one-sided exact sign-flip p<=0.05, fixed 10k paired-bootstrap lower 95% bound >0, per-seed rank delta <=+2 and the same common gates. Locked test requires confirmation superiority plus every DCL effective rank >=5. Scope conclusions to the 500-step protocol; a 2,000-step paired follow-up is required for a full-pipeline convergence claim.

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
Supersedes [[hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728]].

#contrastive #dcl #infonce #cross-modal #float32 #rtx-5070 #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #denominator #optihealth-eiv #matched-baseline #historical-pipeline #contrastive-loss #subject-heldout #representation-learning
