---
arxiv_id_or_url: '2410.20542'
id: papagei-2025-open-ppg-fm-2410-20542
title: 'PaPaGei: Open Foundation Models for Optical Physiological Signals'
topic_tags:
- optihealth
- wearables
---

## Key claims
- First open PPG foundation model: 57k hours / 20M unlabeled segments from public datasets, morphology-aware contrastive objective (inter-individual pulse-shape structure) rather than plain InfoNCE; beats time-series FMs and SSL baselines on 14+ of 20 tasks across 10 datasets (cardiovascular, sleep, pregnancy, wellbeing) at 70x fewer parameters.
- Usable as a frozen feature extractor for a multimodal model. Relevance to EiV: the ring's native signal is PPG; EiV never touches the waveform (it consumes per-minute HR/HRV), so PaPaGei is an upstream option for a richer per-minute feature set, not a drop-in for the day-level encoder.

#optihealth #wearables
