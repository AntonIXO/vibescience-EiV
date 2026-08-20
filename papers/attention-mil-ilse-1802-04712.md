---
arxiv_id_or_url: https://arxiv.org/abs/1802.04712
id: attention-mil-ilse-1802-04712
title: Attention-based Deep Multiple Instance Learning (Ilse, Tomczak, Welling)
topic_tags:
- extent-heterogeneity
- pooling
- state-supervision
---

## Key claims
- Gated attention pooling over instances in a bag; the bag label supervises a weighted sum of instance embeddings, and the attention weights LOCALISE the positive instances (shown in histopathology).
- This is the canonical method for our label geometry: one EMA ping / one session label attached to a bag of 23 patch tokens of which only 1-2 are real for a short session.
- Directly relevant to the contraction mechanism: attention pooling with a learned gate can assign ~0 weight to fill patches, where adaptive_avg_pool2d cannot. It is the trainable generalisation of masked mean pooling.
- HONEST GAP found in the 2026-08-20 sweep: NO published work puts one EMA ping as a bag label over a multi-hour physiological bag AND quantifies attention mass on the labelled segment vs fixed-window baselines. Closest physiological application is arXiv:2604.06990 (attention MIL for questionnaire stress from wearables, LOSO R2 0.24-0.28, r 0.42-0.49) which is bag-level PSS and does NOT prove localisation.
- arXiv id verified 2026-08-20; ICML 2018.

#extent-heterogeneity #pooling #state-supervision
