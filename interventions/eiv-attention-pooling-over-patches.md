---
id: eiv-attention-pooling-over-patches
name: Cardinality-invariant attention pooling over patch tokens (PMA / gated MIL)
topic_tags:
- extent-heterogeneity
- pooling
- state-supervision
---

## Description
Replace the fixed-arity cross-patch pool with attention pooling over the 23 patch tokens: either Set-Transformer PMA (learnable seed queries, cardinality-invariant by construction) or gated attention MIL (Ilse et al.), with the M_mask presence fraction supplied as an additive attention bias so fill patches can be driven to ~0 weight. Unlike mask-weighted mean this is TRAINABLE and can also localise WHICH patch carries a sparse label. Keeps sensor_slots and d_llm unchanged so the LM-facing prefix geometry is untouched.

#extent-heterogeneity #pooling #state-supervision
