---
artifacts: []
closed: true
config_note: Replacement for symmetric pre-step OOM only; no failed _v1 output reused.
  RTX 5070 float32 paired hard InfoNCE vs DCL, batch102, 500 steps, pilot seeds17/23/29,
  test locked. Training MedGemma caption cache must authenticate 4,962 files and corpus
  SHA 7afdd8b1de54921e1c80b5aef7e1dbf984c703c56c38246e48822e887f7bb9a1. Validation
  cache must authenticate 401 files and SHA e71f74fbb0b7d61df7c4582e59ee4480cd632ff2bd5a2e4096c802c48d5d5a46.
  Runtime text precompute forbidden. Implementation digest bca2dfa3df312bb0f053fffaefd1230d7412124adea206e75fcce433c26cd997;
  protocol SHA 643aa7a507e04238af4274ff3517e5f3caaab76ab55c279be3f0c163f3ab8e7f; split
  SHA 43dcafef4a0a4a0e4cae3c6bca0b067599ae61b35d1ea1983e4a16ab4ac2f2f5. Smoke is excluded;
  gate/scope unchanged.
cost: null
crash_reason: ''
created_at: '2026-07-28T11:19:49+00:00'
diagnostics_measured:
- after: 0.6890278160572052
  before: 1.0000004768371582
  diagnostic_id: eiv_text_cache_batch_invariance_cosine
do_not_retry: []
external_run: /root/optiHealth-EiV/outputs/experiments/infonce_vs_dcl_softtoken_20260728_v2
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5+frozen-bca2dfa3df312bb0
hypothesis_id: hyp-eiv-dcl-vs-hard-infonce-fp32-cached-20260728
id: exp-eiv-dcl-vs-hard-infonce-fp32-cached-pilot-20260728
interpretation:
  at: '2026-08-18T21:08:44+00:00'
  failure_mode: test_invalid
  lesson: DCL-vs-hard-InfoNCE REMAINS UNTESTED after this v2 retry — the run cleared
    the earlier fp32 OOM by authenticating the cache (4,962 training files at corpus
    SHA 7afdd8b1..., 401 validation files at e71f74fb...) but was then scientifically
    invalidated by a padding bug, so no objective comparison exists. The cache was
    proven genuine (the ten audited vectors reproduced at cosine ~1.0 with identical
    norms under genuine google/medgemma-1.5-4b-it once their original precompute groups
    of four were reconstructed; legacy FrozenHash norms would have been 0.40-0.61
    versus real cached norms 85.5-113.8), but MedGemma's tokenizer LEFT-pads while
    the hidden_states_last_token code gathered index sum(mask)-1, a right-padding
    assumption. Under changed batching the cached-vs-fresh batch-invariance cosine
    fell from 1.0000 to a median of 0.6890 with a minimum of 0.0276, so every v2 objective
    metric, the pilot gate, and the seed-31 InfoNCE confirmation were excluded and
    the locked test was never opened. Any retry must first land padding-agnostic final-attended-token
    pooling, cache version 2 with CONTENT hashes (not path fingerprints), and fail-loud
    MedGemma loading with FrozenHash removed; a batch-invariance cosine check near
    1.0 belongs in preflight.
  transfers_to:
  - hyp-eiv-dcl-vs-hard-infonce-fp32-20260728
  - hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728
  - hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-20260731
  - hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-gated-20260801
  - hyp-eiv-multipositive-dcl-20260728
key_numbers: {}
observed_effects:
- delta: -0.31097266078
  diagnostic_id: eiv_text_cache_batch_invariance_cosine
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_crossmodal_median_rank: false
    eiv_duplicate_recall5: false
    eiv_effective_rank: false
    eiv_false_negative_pairs_per_batch: false
    eiv_heldout_hr_r2: false
    eiv_peak_vram_gib: false
    eiv_sensor_median_cosine: false
    eiv_training_runtime_sec: false
provenance: {}
recipe_fingerprint: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[hyp-eiv-dcl-vs-hard-infonce-fp32-cached-20260728]].
At commit `fix/soft-token-norm-scale@955c1b5+frozen-bca2dfa3df312bb0`.
External run: /root/optiHealth-EiV/outputs/experiments/infonce_vs_dcl_softtoken_20260728_v2

## Measurements
- [[eiv_text_cache_batch_invariance_cosine]]: before 1.0000004768371582 → after 0.6890278160572052  (Δ -0.310973, down)

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `test_invalid`

DCL-vs-hard-InfoNCE REMAINS UNTESTED after this v2 retry — the run cleared the earlier fp32 OOM by authenticating the cache (4,962 training files at corpus SHA 7afdd8b1..., 401 validation files at e71f74fb...) but was then scientifically invalidated by a padding bug, so no objective comparison exists. The cache was proven genuine (the ten audited vectors reproduced at cosine ~1.0 with identical norms under genuine google/medgemma-1.5-4b-it once their original precompute groups of four were reconstructed; legacy FrozenHash norms would have been 0.40-0.61 versus real cached norms 85.5-113.8), but MedGemma's tokenizer LEFT-pads while the hidden_states_last_token code gathered index sum(mask)-1, a right-padding assumption. Under changed batching the cached-vs-fresh batch-invariance cosine fell from 1.0000 to a median of 0.6890 with a minimum of 0.0276, so every v2 objective metric, the pilot gate, and the seed-31 InfoNCE confirmation were excluded and the locked test was never opened. Any retry must first land padding-agnostic final-attended-token pooling, cache version 2 with CONTENT hashes (not path fingerprints), and fail-loud MedGemma loading with FrozenHash removed; a batch-invariance cosine check near 1.0 belongs in preflight.

Transfers to: [[hyp-eiv-dcl-vs-hard-infonce-fp32-20260728]], [[hyp-eiv-dcl-vs-hard-infonce-softtoken-20260728]], [[hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-20260731]], [[hyp-eiv-dcl-vs-hard-infonce-medgemma-cachev2-gated-20260801]], [[hyp-eiv-multipositive-dcl-20260728]]

## Notes
Scientifically invalidated before confirmation. Ten fixed cache vectors reproduced at cosine ~1.0 and identical norms with genuine google/medgemma-1.5-4b-it only when their original precompute groups of four were reconstructed, proving there was no FrozenHash fallback. The actual legacy hash norms were 0.40-0.61 versus cached norms 85.5-113.8. But MedGemma's tokenizer left-pads while the old hidden_states_last_token code gathered sum(mask)-1, a right-padding assumption. Under changed batching, cached-vs-fresh cosine median fell to 0.6890 and minimum to 0.0276. Thus all v2 objective metrics, the pilot gate, and seed31 InfoNCE confirmation are inconclusive and excluded; test stayed locked. Code is being superseded by padding-agnostic final-attended-token pooling, cache version 2/content hashes, and fail-loud MedGemma loading with FrozenHash removed.

#contrastive #dcl #infonce #cross-modal #float32 #authenticated-cache #rtx-5070 #hsic #cca #vicreg #wearable-ml #optihealth-eiv #matched-baseline #denominator #optihealth-eiv #matched-baseline #historical-pipeline #contrastive-loss #subject-heldout #representation-learning
