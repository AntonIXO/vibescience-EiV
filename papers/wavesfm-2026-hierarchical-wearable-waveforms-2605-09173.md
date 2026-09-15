---
arxiv_id_or_url: '2605.09173'
id: wavesfm-2026-hierarchical-wearable-waveforms-2605-09173
title: 'WavesFM: Hierarchical Representation Learning for Longitudinal Wearable Sensor
  Waveforms (Cao et al., Google, 2026)'
topic_tags:
- wearables
- cross-modal
---

## Key claims
- Two-stage SSL for raw PPG+ACC over week-long horizons: Stage I a 15-s segment encoder trained subject-contrastively (6.8M h, 324k people), then FROZEN; Stage II a temporal encoder over the sequence of segment embeddings trained by masked embedding prediction with a dual-branch decoder and multi-scale sparse masking (5.3M h, 10k people).
- Frames the field as a resolution trade-off: morphology-centric models (10 s - 5 min segments) collapse longitudinal dynamics by mean-pooling ('bag of segments'); behaviour-centric models run on minute-level aggregates (HR, steps) and lose waveform signatures. EiV's 12-channel minute tensor is the behaviour-centric side.
- Reports superior performance across 58 tasks (demographics, lifestyle, conditions, medications) against both paradigms. No weights or code stated as released (Google).
- Design template for EiV Track P2: frozen PPG-FM per-segment embeddings -> small temporal model over the day -> heads, replacing the custom TemporalEncoder on minute aggregates.

#wearables #cross-modal
