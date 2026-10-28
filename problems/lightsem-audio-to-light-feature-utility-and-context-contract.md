---
created_at: '2026-10-15T21:18:12+00:00'
former_ids: []
id: lightsem-audio-to-light-feature-utility-and-context-contract
paper_refs: []
problem_tags:
- conditioning-collapse
- matched-baseline
status: open
target: null
title: LightSem audio-to-light feature utility and context contract
topic_tags:
- cross-modal
- control
- baseline
updated_at: '2026-10-15T22:51:38+00:00'
updates:
- at: '2026-10-15T21:27:56+00:00'
  text: '2026-10-15 unpreregistered context follow-up: bounded bidirectional attention
    with 16-frame past/future halo completed on the same eight-song split, seed 0,
    400 updates. Compared with the full causal feature arm: aim MAE 0.3431 vs 0.3417,
    RGB MAE 0.3972 vs 0.3940, brightness MAE 0.4905 vs 0.4213, event BCE 0.2128 vs
    0.2127, cross-song aim correlation 0.9979 vs 0.9970, and raw aim jerk p99 2071
    vs 2592. It reduced the jerk diagnostic by ~20% but worsened target fit and did
    not reduce the shared song trajectory. This is a bounded semantic-control probe,
    not a continuous full-song qualification or render.'
- at: '2026-10-15T22:45:37+00:00'
  text: '2026-10-15 external baseline demo completed: official RS2002/Skip-BART repo
    commit 88e56d1c07460276273fbbed777d248239262ced, official trained checkpoint archive
    sha256 737603ca3921a070ea8b70e623f7b789219656e0a02ec65721a7698a0ceb9d3c, OpenL3
    512-D embeddings from authenticated /root/shpongle.flac. Artifact receipt /mnt/lightsem-data/experiments/lightsem/skipbart_repo_demo_20261015/DEMO_RECEIPT.json
    and video skipbart_shpongle.mp4. Output is 1024 frames at 10Hz, 400x400, 102.4s,
    single HSV hue/value; generated controls have 82 unique pairs, hue 115-122, value
    41-64. Compatibility deviations were map_location cuda:2->cuda:0, checkpoint decoder_emb2->decoder_emb
    key rename, ffmpeg mux replacing MoviePy fps=None failure, and correction of upstream
    *_gen.npy embedding-write bug. No MuQ, Base92, body, DMX or LightSem renderer
    used; promotion forbidden.'
- at: '2026-10-15T22:51:38+00:00'
  text: '2026-10-15 test/collapse correction: added tests/test_audio_light_foundation.py
    and corrected audio_light_foundation/evaluation.py to use model.config.local_rate_hz,
    physical timestamps, and per-sample temporal reversal rather than batch reversal.
    New suite plus existing foundation/body tests: 12 passed. Declared 32-frame streaming
    contract is exact (<1e-5) when timestamps and duration are supplied; 16-frame
    external splits are intentionally not equivalent because latent updates occur
    at 32-frame internal blocks and must not be reported as continuous equivalence.
    Direct saved-array audit: v22 carried-state long90 is spatially collapsed (84.6%
    frames below spread .2, aim effective rank 1.329); v23/v24/v25 reset-window exports
    pass the narrow spatial gate but are not continuous runs and have large boundary/raw
    derivative problems. v25 raw aim speed max 26.7, acceleration max 1075.7, jerk
    max 54553. No candidate is promoted.'
---

## Description
Determine which authenticated MuQ/Base92/clock feature groups improve target-specific audio-to-light controls, and whether bounded future context repairs raw movement without leakage or unstable chunk behavior. Scope is the eight-song diagnostic corpus and learned semantic controls only; no native DMX, body generation, or product promotion.

## Updates
- **2026-10-15T21:27:56+00:00** — 2026-10-15 unpreregistered context follow-up: bounded bidirectional attention with 16-frame past/future halo completed on the same eight-song split, seed 0, 400 updates. Compared with the full causal feature arm: aim MAE 0.3431 vs 0.3417, RGB MAE 0.3972 vs 0.3940, brightness MAE 0.4905 vs 0.4213, event BCE 0.2128 vs 0.2127, cross-song aim correlation 0.9979 vs 0.9970, and raw aim jerk p99 2071 vs 2592. It reduced the jerk diagnostic by ~20% but worsened target fit and did not reduce the shared song trajectory. This is a bounded semantic-control probe, not a continuous full-song qualification or render.
- **2026-10-15T22:45:37+00:00** — 2026-10-15 external baseline demo completed: official RS2002/Skip-BART repo commit 88e56d1c07460276273fbbed777d248239262ced, official trained checkpoint archive sha256 737603ca3921a070ea8b70e623f7b789219656e0a02ec65721a7698a0ceb9d3c, OpenL3 512-D embeddings from authenticated /root/shpongle.flac. Artifact receipt /mnt/lightsem-data/experiments/lightsem/skipbart_repo_demo_20261015/DEMO_RECEIPT.json and video skipbart_shpongle.mp4. Output is 1024 frames at 10Hz, 400x400, 102.4s, single HSV hue/value; generated controls have 82 unique pairs, hue 115-122, value 41-64. Compatibility deviations were map_location cuda:2->cuda:0, checkpoint decoder_emb2->decoder_emb key rename, ffmpeg mux replacing MoviePy fps=None failure, and correction of upstream *_gen.npy embedding-write bug. No MuQ, Base92, body, DMX or LightSem renderer used; promotion forbidden.
- **2026-10-15T22:51:38+00:00** — 2026-10-15 test/collapse correction: added tests/test_audio_light_foundation.py and corrected audio_light_foundation/evaluation.py to use model.config.local_rate_hz, physical timestamps, and per-sample temporal reversal rather than batch reversal. New suite plus existing foundation/body tests: 12 passed. Declared 32-frame streaming contract is exact (<1e-5) when timestamps and duration are supplied; 16-frame external splits are intentionally not equivalent because latent updates occur at 32-frame internal blocks and must not be reported as continuous equivalence. Direct saved-array audit: v22 carried-state long90 is spatially collapsed (84.6% frames below spread .2, aim effective rank 1.329); v23/v24/v25 reset-window exports pass the narrow spatial gate but are not continuous runs and have large boundary/raw derivative problems. v25 raw aim speed max 26.7, acceleration max 1075.7, jerk max 54553. No candidate is promoted.

#cross-modal #control #baseline #conditioning-collapse #matched-baseline
