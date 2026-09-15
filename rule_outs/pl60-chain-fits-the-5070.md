---
created_at: '2026-09-01T18:56:38+00:00'
former_ids: []
id: pl60-chain-fits-the-5070
problem_id: the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T18:56:38+00:00'
topic_tags:
- optihealth
- deployment-distribution
---

## Claim
A PL60/ST30 (47-patch) A->B->C chain can be trained end-to-end on the RTX 5070 (12 GiB) with the shipped profiles -- so v41pl60state, or any PL60 chain, can be queued locally as written.

## Method
Direct measurement: eiv-v41 unit, Phase A phase_a_12ch_v41pl60 (batch 16, grad-accum 4, PL60/ST30, medgemma-1.5-4b-it bf16, expandable_segments on), logs/v41_phase_a.log; cross-checked against the two prior PL60 VRAM measurements in references/analog-state-campaign-ops.md and ab-chain-pitfalls-20260819.md (2026-08-19).

## Evidence
Phase A at PL60 batch 16 OOMs at step 0: 'Tried to allocate 1014.00 MiB ... 783.00 MiB free ... 10.32 GiB allocated by PyTorch' inside torch.rms_norm on the MedGemma text path. This adds a THIRD stage to the 08-19 record: Phase B at PL60 batch 16 OOMs 530 MiB short (batch 8 fits); Phase C at PL60 batch 2 OOMs 908 MiB short in backward even with expandable segments, and there is no batch below 2 with grad-accum that changes the per-step activation. So even if A and B are cut to batch 8, C cannot run at PL60 on this card. The three v41 profiles inherited batch 16/30/2 from their PL120 bases verbatim. Consequences: (1) v41's hypothesis a-held-out-scored-chain-combining-every-079038 is UNTESTED (exp-v41-pl60state-oom-20260901 = crashed, no evidence), not refuted; (2) testing it needs a >=16 GiB card, or LLM gradient checkpointing in Phase C exposed as a default-off profile key (today it is hard-gated on swap_contrastive_weight>0 at pipeline.py ~4226) and ridden through its own one-variable arm first; (3) the PL120 fallback with 0.5/0.5 heads is not a substitute -- v30state already collapsed at PL120 with state_patch_weight 0.1 (held-out dense 19.935, scale 2.77) and exp-v22/v29 measured heads costing -1.5..-1.8 eff_rank. Cost of learning this: 1h53 wall, 1 min CPU, 0 GPU (g0 stopped the chain).

## Links
Rules out for problem [[the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all]].

#optihealth #deployment-distribution
