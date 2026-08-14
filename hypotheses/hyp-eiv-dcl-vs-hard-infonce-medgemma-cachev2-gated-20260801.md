---
created_at: '2026-08-01T08:23:27+00:00'
former_ids: []
gates:
- blocking: true
  description: mean paired dRecall@5 >= +0.010 across five seeds
  diagnostic_id: null
  id: mean_recall_delta
  mde: null
- blocking: true
  description: exact one-sided sign-flip p < 0.05
  diagnostic_id: null
  id: exact_signflip_p
  mde: null
- blocking: true
  description: paired bootstrap CI95 lower bound > 0
  diagnostic_id: null
  id: bootstrap_ci95_lower
  mde: null
- blocking: true
  description: mean d effective rank no worse than -0.25
  diagnostic_id: null
  id: mean_effective_rank_delta
  mde: null
- blocking: true
  description: no per-seed median-rank regression worse than +2
  diagnostic_id: null
  id: per_seed_median_rank_delta
  mde: null
- blocking: true
  description: every DCL sensor median cosine <= 0.90 (no directional collapse)
  diagnostic_id: null
  id: dcl_cosine
  mde: null
- blocking: true
  description: peak VRAM below 11.25 GiB on RTX 5070, no OOM
  diagnostic_id: null
  id: vram
  mde: null
- blocking: true
  description: DCL runtime no more than 1.10x paired InfoNCE
  diagnostic_id: null
  id: runtime
  mde: null
- blocking: false
  description: 'deployment: every DCL effective rank >= 5'
  diagnostic_id: null
  id: deployment_gate_every_dcl_rank_at_least_5
  mde: null
id: hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-gated-20260801
interventions:
- eiv-dcl-denominator-only
- eiv-medgemma-last-attended-cache-v2
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_duplicate_recall5
  direction: up
  magnitude_note: 'Primary: mean paired DCL minus InfoNCE Recall@5 at least +0.010
    across five seeds.'
- diagnostic_id: eiv_crossmodal_median_rank
  direction: down
  magnitude_note: DCL should not worsen per-seed median rank by more than +2 and is
    expected to lower it on average.
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Expected weak increase; hard guard permits mean delta no worse than
    -0.25 and deployment requires every DCL rank >=5.
- diagnostic_id: eiv_sensor_median_cosine
  direction: down
  magnitude_note: Expected no directional collapse; every DCL value <=0.90.
- diagnostic_id: eiv_false_negative_pairs_per_batch
  direction: none
  magnitude_note: Exact same caption-equivalent false-negative exposure by construction;
    DCL remains one-positive.
- diagnostic_id: eiv_training_runtime_sec
  direction: none
  magnitude_note: DCL runtime no more than 1.10x paired InfoNCE.
- diagnostic_id: eiv_peak_vram_gib
  direction: none
  magnitude_note: Both arms below 11.25 GiB on RTX 5070 with no OOM.
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: Secondary saturated probe; no material directional claim.
- diagnostic_id: eiv_text_cache_batch_invariance_cosine
  direction: none
  magnitude_note: Both arms use the same authenticated cache; ten-caption cache-vs-fresh
    cosine should remain near 1 before training.
problem_id: optihealth-contrastive-loss-20260727
problem_tags:
- optihealth-eiv
- matched-baseline
- cache-provenance
- contrastive-loss
- subject-heldout
- representation-learning
status: inconclusive
supersedes: hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-20260731
topic_tags:
- contrastive
- dcl
- infonce
- medgemma
- hsic
- cca
- vicreg
- wearable-ml
- denominator
- padding
- text-encoder
---

## Statement
With authenticated, padding-correct MedGemma-4B targets, changing only the matched-positive denominator from hard symmetric InfoNCE to DCL will improve duplicate-aware held-out Recall@5 without materially worsening retrieval rank or native effective rank. [gated restatement: the magnitude thresholds that were preregistered in prose are now enforced as gates]

## Rationale
The v2 comparison was invalidated because its genuine MedGemma targets were batch-dependent under a left-padding/right-padding-index mismatch. The corrected final-attended-token cache removes that confound for both arms. DCL then tests its intended mechanism cleanly: retaining matched-positive attraction while removing the positive from each denominator should reduce gradient coupling at this large in-batch denominator. Exact-caption false negatives remain deliberately identical in both arms, so any gain is attributable to the DCL denominator rather than multi-positive relabeling.

Superseding restatement created 2026-08-01. The original recorded verdict=supports because the verdict was direction-only, while its own confirmation aggregate reported superiority_passed=False (exact sign-flip p=0.34375, paired bootstrap CI95 [-0.00848,+0.01347] crossing zero, per-seed dRecall@5 signs -,+,+,-,+). Every threshold below already existed as free-text magnitude_note on the original hypothesis; nothing enforced them.

## Plan
Freeze source/config/data/split/cache hashes. Use live fix/soft-token profile composition 45 LifeSnaps + 12 PMData + 35 wildcard (batch92), raw tensor with population stats disabled, float32 sensor training, 500 steps/46,000 samples, hard one-positive labels, identical paired initialization/data/augmentation/exposure. Run one excluded smoke pair then exactly five paired seeds 31/37/41/43/47 sequentially on RTX 5070. Primary gate: mean DCL-InfoNCE Recall@5 >=0.010, exact one-sided sign-flip p<=0.05, paired bootstrap CI95 lower>0; retain rank/effective-rank/collapse/resource gates. Test stays locked unless superiority and every DCL effective rank>=5 both pass.

## Links
Tests problem [[optihealth-contrastive-loss-20260727]].
Applies [[eiv-dcl-denominator-only]], [[eiv-medgemma-last-attended-cache-v2]].
Predicts [[eiv_duplicate_recall5]] (up).
Predicts [[eiv_crossmodal_median_rank]] (down).
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_sensor_median_cosine]] (down).
Predicts [[eiv_false_negative_pairs_per_batch]] (none).
Predicts [[eiv_training_runtime_sec]] (none).
Predicts [[eiv_peak_vram_gib]] (none).
Predicts [[eiv_heldout_hr_r2]] (none).
Predicts [[eiv_text_cache_batch_invariance_cosine]] (none).
Gate `mean_recall_delta` (blocking): mean paired dRecall@5 >= +0.010 across five seeds
Gate `exact_signflip_p` (blocking): exact one-sided sign-flip p < 0.05
Gate `bootstrap_ci95_lower` (blocking): paired bootstrap CI95 lower bound > 0
Gate `mean_effective_rank_delta` (blocking): mean d effective rank no worse than -0.25
Gate `per_seed_median_rank_delta` (blocking): no per-seed median-rank regression worse than +2
Gate `dcl_cosine` (blocking): every DCL sensor median cosine <= 0.90 (no directional collapse)
Gate `vram` (blocking): peak VRAM below 11.25 GiB on RTX 5070, no OOM
Gate `runtime` (blocking): DCL runtime no more than 1.10x paired InfoNCE
Gate `deployment_gate_every_dcl_rank_at_least_5` (advisory): deployment: every DCL effective rank >= 5
Supersedes [[hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-20260731]].

#contrastive #dcl #infonce #medgemma #hsic #cca #vicreg #wearable-ml #denominator #padding #text-encoder #optihealth-eiv #matched-baseline #cache-provenance #contrastive-loss #subject-heldout #representation-learning
