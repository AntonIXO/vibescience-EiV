---
created_at: '2026-09-01T09:48:33+00:00'
former_ids: []
id: early-vs-late-presence-is-untested
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:48:33+00:00'
topic_tags: []
---

## Claim
Whether EARLY presence beats LATE presence is untested and needs its own justification before it earns GPU.

## Method
Recognised patch_pool_mode as the early/late presence knob, then scored the six existing captures (w6avg and w6msk at s43/s44/s45) on both the supabase and dense draws, comparing difference-of-means with SE from the SEED spread rather than the eval draw.

## Evidence
IT WAS ALREADY RUN, THREE SEEDS PER SIDE, AND THE ANSWER IS THE OPPOSITE OF WHAT THE PROFILES ASSUME (scratch/_early_vs_late_presence.py).

patch_pool_mode IS this knob:
  avg         pools the patch axis WITHOUT presence -- presence enters LATER, via the projector's patch_presence path
  masked_avg  weights the pool BY each patch's measured fraction -- presence enters AT pooling, i.e. EARLY

w6avg and w6msk differ on exactly this and have s43/s44/s45 each. They are also the families the noise floors were measured from, so their seed spread is known rather than assumed. That makes this the 3-vs-3 paired-seed design the total-noise readout says is required -- already on disk, at zero GPU cost.

SUPABASE (the deployment draw, 200 rows/seed):
  avg  (LATE)   3.187 / 2.861 / 2.742   mean 2.930  sd 0.230
  msk  (EARLY)  3.389 / 3.351 / 3.737   mean 3.493  sd 0.213
  delta +0.562  SE 0.181  z +3.11  -> LATE IS BETTER, BEYOND NOISE

DENSE (128 rows/seed):
  avg  (LATE)   3.560 / 2.642 / 3.334   mean 3.179  sd 0.478
  msk  (EARLY)  3.947 / 2.439 / 3.301   mean 3.229  sd 0.757
  delta +0.050  SE 0.517  z +0.10  -> inside noise

EVERY SEED of w6avg beats EVERY seed of w6msk on supabase -- 2.742-3.187 against 3.351-3.737, no overlap. That is a rank-sum of 0, the strongest form this design can produce, and it is the FIRST result in this campaign to clear the full noise treatment rather than the finite-draw one.

WHY THIS MATTERS BEYOND THE QUESTION ASKED: configs/training.yaml sets patch_pool_mode: masked_avg on the shipped profiles and justifies it at line 3344 by citing exp-w6b (short-cohort variance-compression 0.5630 -> 0.6897) and exp-w12/w13 (out-of-sample 0.4577 -> 0.6989). Those are VARIANCE-COMPRESSION results and they are not disputed here. But the deployment MAE moves the other way, and the profile comment does not mention it. Two different diagnostics, opposite signs, and the config records only the favourable one.

SCOPE, STATED NARROWLY: this compares presence at POOLING against presence at the PROJECTOR. It says nothing about presence at the encoder's FIRST layer, where FlexMLP genuinely has no mask term -- that remains the one untested placement.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
