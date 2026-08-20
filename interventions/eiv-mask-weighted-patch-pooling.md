---
id: eiv-mask-weighted-patch-pooling
name: Mask-weighted (presence-normalised) cross-patch pooling
topic_tags:
- extent-heterogeneity
- pooling
- padding
---

## Description
Replace the presence-blind cross-patch pool with a MASK-WEIGHTED pool: weight each patch token by its fraction of measured (M_mask) minutes and normalise by the sum of those weights, so a patch of pure fill contributes 0 instead of contributing the fill value. Sites: cross_modal.py:323 adaptive_avg_pool2d (production, Phase C) and the encoder-side patch pool. Removes the (1-c) contraction term by construction. NOTE the code comment at cross_modal.py:260-270 explicitly says presence-weighting was NOT done because it 'would move the output of every input that has a missing channel, which is every checkpoint and every recorded result' -- so this is a breaking change requiring its own control arm, exactly as that comment demands.

#extent-heterogeneity #pooling #padding
