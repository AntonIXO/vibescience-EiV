---
created_at: '2026-08-14T22:29:52+00:00'
former_ids: []
gates:
- g1_deployment_mae_beats_band
- g2_dense_not_wrecked
- g3_peak_excursion_end_to_end
- g4_vram_ceiling
hypothesis_id: null
id: rb-w4-projector-maxpool-end-to-end-20260815
notes:
- at: '2026-08-15T07:05:04+00:00'
  text: '[2026-08-15] CODE LANDED at optiHealth-EiV be9e087. patch_pool_mode={avg|max|avgmax}
    implemented in src/projectors/cross_modal.py, default ''avg'', 8 tests.


    TWO MATERIAL CHANGES TO THIS RUNBOOK''S SPEC:


    1. THE CONCAT DESIGN WAS WRONG AND IS REPLACED. This runbook specified ''avg+max
    concat along D''. Concatenating on D doubles the soft-token WIDTH, and D is d_llm
    — the width the LM consumes — so that would invalidate every existing adapter
    and double projector VRAM. Implemented instead as a SLOT SPLIT: the k slots-per-channel
    become an avg half and a max half. sensor_slots and d_llm both unchanged (96 slots,
    C=12, k=8 -> 4 avg + 4 max). Drop-in: same prefix length, same width, same VRAM.


    2. GATE g4_vram_ceiling IS NO LONGER LOAD-BEARING. It existed because avgmax was
    expected to double projector input width. It does not. Keep measuring eiv_peak_vram_gib
    as hygiene but it cannot bind, and the ''max-only fallback if VRAM binds'' contingency
    is moot — though max-only remains a config option and captured essentially the
    whole probe effect (+0.6771 vs +0.6849).


    BACK-COMPAT PROVEN, NOT ASSERTED: default ''avg'' is bit-identical to the previous
    adaptive_avg_pool2d path, asserted by torch.equal against a recomputation of the
    ORIGINAL expression on the full forward output. No existing checkpoint changes
    meaning.


    STATUS: Stage 1 (GPU, Phase A/B/C, >=3 seeds) unblocked, NOT yet run. Both preconditions
    now satisfied — E4''s blocking Stage-0 gate is also cleared (exp-e4-stage0-avgmax-cpu-gate-20260815:
    +0.6849 against a >=0.25 requirement).'
- at: '2026-08-15T10:04:27+00:00'
  text: "[2026-08-15] ABANDONED — PREMISE REFUTED BEFORE GPU SPEND. Rule-out `ro-avgmax-pooling-production-site-20260815`.\n\nThe
    +0.4042 effect this runbook was built on was measured AT THE WRONG SITE. E19/E19b
    pooled the TEMPORAL ENCODER's patch tokens [B,C,23,192]. cross_modal.py pools
    the POST-CROSS-ATTENTION soft tokens, after query_proj + 128-prototype cross-attention
    has already mapped 192 -> d_llm=2560.\n\nMeasured at the REAL production site
    (n=4254 strict-clean, 77 subjects, GroupKFold(5) by subject):\n  v5c  avg +0.4880
    \ max +0.5125  avgmax +0.5116   gap +0.0235  FAIL vs +0.05 bar\n  v5d  avg +0.4765
    \ max +0.5113  avgmax +0.5212   gap +0.0447  FAIL vs +0.05 bar\nvs encoder-site
    gap +0.4041 — a 9.1x shrinkage. Shuffle controls dead (-0.099..-0.158).\n\nTHE
    HARNESS IS VALIDATED, NOT SUSPECT: scratch/e19e_site_control.py measures BOTH
    sites in one script on one pool with one checkpoint, and its encoder-site arm
    reproduces E19's published numbers to four decimals (+0.2807/+0.6771/+0.6849).
    This is a site difference, not a broken instrument.\n\nMECHANISM: not the RMS
    renorm (disabling it leaves the gap unchanged: v5d +0.0443 -> +0.0443). The cross-attention
    ALREADY recovers the excursion, lifting avg from +0.2807 to ~+0.48; all three
    operators land within 0.045 of a ~+0.51 ceiling. There is no headroom left at
    the pooling step.\n\nThis independently reconfirms `ro-dual-avg-max-pooling-downstream-20260805`,
    which eliminated this same intervention at delta +0.0090 on 2026-08-05. The direction
    had already been refuted; E19 repeated the exact methodological error that rule-out
    documents (pooling the encoder output without running the projector).\n\nTWO STRUCTURAL
    BLOCKERS, so nobody re-queues this as 'just set the profile key':\n1. patch_pool_mode
    is NOT PLUMBED — it exists only in cross_modal.py + its tests, not in configs/training.yaml,
    cli_train.py or pipeline.py. Setting it in a profile trains 'avg' silently and
    reports a FAKE NULL.\n2. It is a NO-OP in Phase A/B — those sites build the projector
    WITHOUT sensor_slots and the pooling block is gated on it. Executed proof: Phase
    A/B avg vs avgmax are bit-identical (max|diff| 0.0e+00); only the Phase C construction
    differs (5.755). And Phase C FREEZES the projector (pipeline.py:3133-3134), so
    it could never adapt to a new operator. The '>=3 seeds through A/B/C' design was
    not runnable as written.\n\nGPU AVOIDED: ~12 h / 6 runs. Guard comment landed
    in cross_modal.py at e34d42e; probes at 69b0bb8."
priority: 5
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
status: abandoned
topic_tags:
- pooling
- cross-modal
- deployment-distribution
- medgemma
- control
updated_at: '2026-08-15T10:04:27+00:00'
---

## Prompt
W4 — swap the projector's cross-patch pooling from AVG to AVG+MAX and measure END-TO-END. GPU. Repo /root/optiHealth-EiV.

WHY THIS IS THE TOP-PRIORITY ARM (context for a fresh session).
optiHealth-EiV / SensorLM: temporal encoder over wearable physiology -> soft-token prefixes for MedGemma. Canonical tensor [11318, 1440, 12], one window per civil day, git_sha e849eb7. hr_mean reads back at R2 0.97 but hr_PEAK / within-day excursion has been the open axis for months.

On 2026-08-15, exp-e19-encoder-pooling-20260815 (verdict SUPPORTS) localised the loss precisely. Reading the REAL v5d Phase-B encoder's own patch tokens ([4000, 24, 23, 192], weights loaded strict, n=4000, 77 subjects, GroupKFold(5) by subject, control R2(excursion | true daily mean) = +0.0649), pooling ACROSS the patch axis gives:
    avg    +0.2807  dim 4608   <- WHAT PRODUCTION DOES TODAY (cross_modal.py:260 adaptive_avg_pool2d)
    max    +0.6771  dim 4608
    avgmax +0.6849  dim 9216   <- BEST
    lse    +0.5116  dim 4608
Gap avg->avgmax +0.4042 (8x the +0.05 bar). The dimensionality confound is CONTROLLED: max alone hits +0.6771 at identical dim to avg, so gap avg->max = +0.3964 with dim held constant. And the upstream risk is dead — encoder avg +0.2807 vs raw-patch avg +0.0090 means the encoder CONCENTRATES the excursion and avg-pooling discards it, so InfoNCE-on-mean-captions did NOT destroy it.

WHAT THIS RUNBOOK MUST NOT ASSUME. E19 is a LINEAR-READOUT PROBE on pooled tokens. It proves the information survives the pooling choice; it does NOT prove the frozen LM will USE it at generation. That is exactly the distinction exp-e31 drew (a probe recovering signal after projection != the LM consuming it), and it is why this arm is end-to-end rather than another probe.

THE CHANGE. src/projectors/cross_modal.py around line 260:
    pooled = torch.nn.functional.adaptive_avg_pool2d(
        soft_tokens.permute(0, 3, 1, 2), (C, k)).permute(0, 2, 3, 1)
Replace with an avg+max concat along the patch axis (adaptive_avg_pool2d and adaptive_max_pool2d, concatenated on D), then feed the widened representation to the slot projection. Gate it behind a projector_format_version bump so old checkpoints keep loading and the manifest records which operator produced any adapter — do NOT silently change the meaning of existing artifacts. Provide `max`-only as a config option: it is the EQUAL-DIMENSION fallback that captured essentially the whole probe effect, and it is the one to use if VRAM binds.

ARMS: control = v9band04 recipe unchanged (avg). intervention = identical in every respect except the pooling operator. >=3 SEEDS each (42/43/44). Single-seed comparisons are void on this project.

SCORING, all quoted with denominators and trivials in the same table:
  PRIMARY  eiv_supabase_hr_raw_mae_bpm on the frozen 200-row supabase draw (default_rng(0)), quoted with its 4.02 trivial baseline and the measured post-atomfix seed band of 0.60 bpm. The intervention must beat the control's seed BAND, not its best seed.
  GUARD    eiv_dense128_hr_raw_mae_bpm — catches buying deployment accuracy by wrecking the dense population (v7noatom 2.89 reference).
  TARGET   eiv_peak_excursion_increment_r2 end-to-end, plus emitted hr_peak skill with a distinct-value count (a 4-value lattice and a 10-value distribution are indistinguishable to accuracy alone — always pair them).
  OPERATIONAL eiv_peak_vram_gib against the 11.25 GiB ceiling. avgmax DOUBLES projector input width (4608 -> 9216 per channel-block before slot projection), so VRAM is a real risk; if it binds, run the max-only variant and say so.

PREREGISTER before launching: propose_hypothesis with predicted_effects + gates, then start_experiment. Commit the MDE against the 0.60 bpm band. A null here is valuable — it would mean the excursion information reaches the prefix but the LM still cannot emit it, which would move the lever to the readout/decode stage where E6-E10 already localised the numeric-slot failures.

PITFALL: captures must stay batch_size=1. Batched generate() is rejected (rule-out `batched-generate-in-the-capture-f75db4`): 31/32 emissions differ at batch=8 under greedy decode because cuBLAS reduction-order nondeterminism flips argmax on near-ties.

## Preconditions
E19 (exp-e19-encoder-pooling-20260815) is CLOSED with verdict=supports — this runbook is its end-to-end follow-up and should not be run without that. Baseline recipe: v9band04 (`lora_proxy_12ch_wl_band04`), docs/BASELINE.md. GPU required (RTX 5070, 12227 MiB). Budget roughly 2 h/arm warm; link text caches first (scratch/link_text_caches.py is worth ~42 min/arm).

## Log
- **2026-08-15T07:05:04+00:00** — [2026-08-15] CODE LANDED at optiHealth-EiV be9e087. patch_pool_mode={avg|max|avgmax} implemented in src/projectors/cross_modal.py, default 'avg', 8 tests.

TWO MATERIAL CHANGES TO THIS RUNBOOK'S SPEC:

1. THE CONCAT DESIGN WAS WRONG AND IS REPLACED. This runbook specified 'avg+max concat along D'. Concatenating on D doubles the soft-token WIDTH, and D is d_llm — the width the LM consumes — so that would invalidate every existing adapter and double projector VRAM. Implemented instead as a SLOT SPLIT: the k slots-per-channel become an avg half and a max half. sensor_slots and d_llm both unchanged (96 slots, C=12, k=8 -> 4 avg + 4 max). Drop-in: same prefix length, same width, same VRAM.

2. GATE g4_vram_ceiling IS NO LONGER LOAD-BEARING. It existed because avgmax was expected to double projector input width. It does not. Keep measuring eiv_peak_vram_gib as hygiene but it cannot bind, and the 'max-only fallback if VRAM binds' contingency is moot — though max-only remains a config option and captured essentially the whole probe effect (+0.6771 vs +0.6849).

BACK-COMPAT PROVEN, NOT ASSERTED: default 'avg' is bit-identical to the previous adaptive_avg_pool2d path, asserted by torch.equal against a recomputation of the ORIGINAL expression on the full forward output. No existing checkpoint changes meaning.

STATUS: Stage 1 (GPU, Phase A/B/C, >=3 seeds) unblocked, NOT yet run. Both preconditions now satisfied — E4's blocking Stage-0 gate is also cleared (exp-e4-stage0-avgmax-cpu-gate-20260815: +0.6849 against a >=0.25 requirement).
- **2026-08-15T10:04:27+00:00** — [2026-08-15] ABANDONED — PREMISE REFUTED BEFORE GPU SPEND. Rule-out `ro-avgmax-pooling-production-site-20260815`.

The +0.4042 effect this runbook was built on was measured AT THE WRONG SITE. E19/E19b pooled the TEMPORAL ENCODER's patch tokens [B,C,23,192]. cross_modal.py pools the POST-CROSS-ATTENTION soft tokens, after query_proj + 128-prototype cross-attention has already mapped 192 -> d_llm=2560.

Measured at the REAL production site (n=4254 strict-clean, 77 subjects, GroupKFold(5) by subject):
  v5c  avg +0.4880  max +0.5125  avgmax +0.5116   gap +0.0235  FAIL vs +0.05 bar
  v5d  avg +0.4765  max +0.5113  avgmax +0.5212   gap +0.0447  FAIL vs +0.05 bar
vs encoder-site gap +0.4041 — a 9.1x shrinkage. Shuffle controls dead (-0.099..-0.158).

THE HARNESS IS VALIDATED, NOT SUSPECT: scratch/e19e_site_control.py measures BOTH sites in one script on one pool with one checkpoint, and its encoder-site arm reproduces E19's published numbers to four decimals (+0.2807/+0.6771/+0.6849). This is a site difference, not a broken instrument.

MECHANISM: not the RMS renorm (disabling it leaves the gap unchanged: v5d +0.0443 -> +0.0443). The cross-attention ALREADY recovers the excursion, lifting avg from +0.2807 to ~+0.48; all three operators land within 0.045 of a ~+0.51 ceiling. There is no headroom left at the pooling step.

This independently reconfirms `ro-dual-avg-max-pooling-downstream-20260805`, which eliminated this same intervention at delta +0.0090 on 2026-08-05. The direction had already been refuted; E19 repeated the exact methodological error that rule-out documents (pooling the encoder output without running the projector).

TWO STRUCTURAL BLOCKERS, so nobody re-queues this as 'just set the profile key':
1. patch_pool_mode is NOT PLUMBED — it exists only in cross_modal.py + its tests, not in configs/training.yaml, cli_train.py or pipeline.py. Setting it in a profile trains 'avg' silently and reports a FAKE NULL.
2. It is a NO-OP in Phase A/B — those sites build the projector WITHOUT sensor_slots and the pooling block is gated on it. Executed proof: Phase A/B avg vs avgmax are bit-identical (max|diff| 0.0e+00); only the Phase C construction differs (5.755). And Phase C FREEZES the projector (pipeline.py:3133-3134), so it could never adapt to a new operator. The '>=3 seeds through A/B/C' design was not runnable as written.

GPU AVOIDED: ~12 h / 6 runs. Guard comment landed in cross_modal.py at e34d42e; probes at 69b0bb8.

## Links
Queued for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Intended gates: `g1_deployment_mae_beats_band`, `g2_dense_not_wrecked`, `g3_peak_excursion_end_to_end`, `g4_vram_ceiling`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#pooling #cross-modal #deployment-distribution #medgemma #control
