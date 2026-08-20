---
arxiv_id_or_url: https://arxiv.org/abs/2402.05956
id: pathformer-adaptive-pathways-2402-05956
title: 'Pathformer: Multi-scale Transformers with Adaptive Pathways for Time Series
  Forecasting'
topic_tags:
- extent-heterogeneity
- epoch-resolution
- pooling
---

## Key claims
- Multi-scale patch division (several patch sizes in one model) plus ADAPTIVE PATHWAYS that route each input through scales based on its own temporal dynamics -- the closest published analogue to per-sample multi-scale weighting.
- Reports stronger generalisation under TRANSFER scenarios, which is the property we need when a 41-minute session and a 1382-minute day hit the same encoder.
- Code public: github.com/decisionintelligence/pathformer. ICLR 2024; arXiv id verified 2026-08-20.
- SCOPE WARNING FOR US: forecasting benchmarks, NOT physiological affect, and our own w3 grid probe already refuted finer fixed grids at 24h (120m/23tok +0.0266 > 60m/47tok +0.0142 > 30m/95tok +0.0115 > 10m/144tok -0.0143). So this paper motivates ADAPTIVE ROUTING ACROSS scales, never simply adding more/finer scales -- the latter is already measured dead here.

#extent-heterogeneity #epoch-resolution #pooling
