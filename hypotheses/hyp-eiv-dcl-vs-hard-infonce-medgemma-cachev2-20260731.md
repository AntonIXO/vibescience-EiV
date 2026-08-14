---
created_at: '2026-07-31T08:22:48+00:00'
gates: []
id: hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-20260731
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
- contrastive-loss
- subject-heldout
- cache-provenance
- optihealth-eiv
- representation-learning
status: confirmed
supersedes: hyp-eiv-dcl-vs-hard-infonce-fp32-cached-20260728
topic_tags:
- contrastive
- dcl
- infonce
- medgemma
- hsic
- cca
- vicreg
- wearable-ml
- optihealth-eiv
- matched-baseline
- denominator
- cache-provenance
- padding
- text-encoder
---

## Statement
With authenticated, padding-correct MedGemma-4B targets, changing only the matched-positive denominator from hard symmetric InfoNCE to DCL will improve duplicate-aware held-out Recall@5 without materially worsening retrieval rank or native effective rank.

## Rationale
The v2 comparison was invalidated because its genuine MedGemma targets were batch-dependent under a left-padding/right-padding-index mismatch. The corrected final-attended-token cache removes that confound for both arms. DCL then tests its intended mechanism cleanly: retaining matched-positive attraction while removing the positive from each denominator should reduce gradient coupling at this large in-batch denominator. Exact-caption false negatives remain deliberately identical in both arms, so any gain is attributable to the DCL denominator rather than multi-positive relabeling.

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
Supersedes [[hyp-eiv-dcl-vs-hard-infonce-fp32-cached-20260728]].

#contrastive #dcl #infonce #medgemma #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #denominator #cache-provenance #padding #text-encoder #contrastive-loss #subject-heldout #cache-provenance #optihealth-eiv #representation-learning
