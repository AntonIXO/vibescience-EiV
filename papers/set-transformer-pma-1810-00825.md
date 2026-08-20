---
arxiv_id_or_url: https://arxiv.org/abs/1810.00825
id: set-transformer-pma-1810-00825
title: 'Set Transformer: A Framework for Attention-based Permutation-Invariant Neural
  Networks'
topic_tags:
- extent-heterogeneity
- pooling
- padding
---

## Key claims
- Pooling by Multihead Attention (PMA): learnable seed vectors attend over a set, giving a pooled representation that is CARDINALITY-INVARIANT -- the pooled output does not shrink toward a fill value as the number of real elements falls.
- This is the principled fix for the measured contraction ratio~=c: a cardinality-invariant pool has, by construction, no (1-c) contraction term.
- Reused in physiological foundation models for mask-aware channel pooling, so the transplant to a [B,C,23,D] token grid is precedented rather than novel.
- Code public: github.com/juho-lee/set_transformer. ICML 2019; arXiv id verified 2026-08-20.

#extent-heterogeneity #pooling #padding
