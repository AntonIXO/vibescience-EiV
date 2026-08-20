---
aliases:
- ts-jepa
- ecg-jepa
- cgm-jepa
- jets
- v-jepa
- joint-embedding-predictive
- latent-prediction
- world-model
- masked-latent-prediction
axis: topic
created_at: '2026-08-18T22:40:32+00:00'
id: jepa
---

## Description
Joint-Embedding Predictive Architecture: self-supervised pretraining that predicts TARGET-ENCODER LATENTS of masked regions rather than reconstructing raw values, with an EMA target encoder and stop-gradient. Covers TS-JEPA (arXiv:2509.25449), ECG-JEPA (arXiv:2410.13867), CGM-JEPA (arXiv:2605.00933), JETS and V-JEPA-2 as prior art, and any EiV arm that replaces the InfoNCE-on-captions Phase A objective with latent prediction. Distinct from `contrastive` (needs negatives/augmentations) and from `anti-collapse` (the failure mode EMA+predictor exists to avoid).

*Axis:* `topic`

## Aliases
- ts-jepa
- ecg-jepa
- cgm-jepa
- jets
- v-jepa
- joint-embedding-predictive
- latent-prediction
- world-model
- masked-latent-prediction
