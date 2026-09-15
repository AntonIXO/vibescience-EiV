---
created_at: '2026-09-01T10:09:42+00:00'
former_ids: []
id: anchor-parity-heals-b-needs-90-plus-anchors
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:09:42+00:00'
topic_tags: []
---

## Claim
Anchor parity heals B: contrastive stages on this corpus need ~90+ anchors per step, and batch_size 25 -> 92 is the exact test of that law.

## Method
Read the hypothesis's stated variable against the shipped profile and git history, found the OOM in the Phase B log, then scored the arm that actually ran against the preregistered gate and its four recorded comparators.

## Evidence
THE 92-ANCHOR ARM NEVER RAN, AND THE ARM THAT DID REFUTES THE LAW'S REASONING WHILE PASSING ITS GATE. This hypothesis has sat in `testing` since 2026-08-31 with its data already on disk.

WHAT HAPPENED TO BATCH 92: it ran and died. logs/v25_phase_b.log ends in `OutOfMemoryError: CUDA out of memory. Tried to allocate 1.45 GiB`, inside the TRIPLET path (trip_batch_size = anchor_input.shape[0]; flattened = encoder_tokens.reshape(...)). The hypothesis's own Plan predicted this failure would be harmless -- 'VRAM check implicit -- B holds no LLM, the text cache is precomputed, 92x1440x24 floats is ~32 MB' -- and that estimate costed the INPUT tensor, not the triplet's encoder-token activations, which is where the allocation actually died.

The profile was lowered to 30 and never restored. phase_b_12ch_v25bigbatch carries batch_size 30 today, and `git log -S'batch_size: 92' -- configs/training.yaml` returns NOTHING: 92 was never committed. verify_one_variable confirms the SHIPPED arm is 25 -> 30, one variable, PASS. So every arm built on this Phase B checkpoint -- v27 through v40, including the incumbent v28affect01 -- inherits a B trained at 30 anchors, not 92.

THE GATE PASSES ANYWAY, WIDELY:
  v25r30 supabase  n=200  MAE 3.203  corr 0.717  scale 1.00
  g1 was corr >= 0.55 -> PASS
  comparators: B@25+triplet 0.327 | B@25 no-triplet 0.240 | no B 0.567 | old-corpus B 0.583
corr 0.717 exceeds EVERY comparator, including 'no B at all' and 'old-corpus B'.

WHY THAT REFUTES THE LAW RATHER THAN CONFIRMING IT: the claim is that this corpus needs ~90+ anchors per step. Batch 25 -> 30 is a 20% increase, not the 3.7x argued for, and it moved supabase corr from 0.240/0.327 to 0.717. If 30 anchors reach 0.717, there is no threshold at 90 -- whatever repairs B is something the 25 -> 30 step already crosses. The anchor-parity account survives only as 'more than 25', which is not the law as stated and does not predict 92.

CALIBRATION, read first as the rule requires: v25r30's DENSE ratio is 1.87, far outside [0.85, 1.35], dense MAE 7.388. The supabase capture is clean (1.00); the dense one is a calibration failure. The arm is not a promotion candidate on any reading -- this is a mechanism finding only.

WHAT IS LEFT, NARROW AND NOT QUEUED: where between 25 and 30 B recovers, and whether it is anchor count at all or the triplet's memory path. v25r36 and v25r48 checkpoints already exist on disk and should be scored before any new GPU is spent on it.

## Links
Rules out for problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
