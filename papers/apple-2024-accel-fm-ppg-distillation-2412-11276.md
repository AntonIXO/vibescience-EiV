---
arxiv_id_or_url: '2412.11276'
id: apple-2024-accel-fm-ppg-distillation-2412-11276
title: Wearable Accelerometer Foundation Models for Health via Knowledge Distillation
  (Apple AHMS)
topic_tags:
- optihealth
- wearables
- cross-modal
---

## Key claims
- Cross-modal privileged distillation at scale: a PPG encoder (rich, high-power modality) is the frozen teacher; accelerometry encoders (cheap, always available) are students trained on 20M minutes / 172K participants of UNLABELLED paired data. 99.2% top-1 PPG-from-accel retrieval on unseen data.
- Distilled accelerometry encoders beat self-supervised AND supervised accelerometry encoders by 23-49% on heart rate and HRV prediction and are generalist across health targets -- representation-level distillation from a richer paired modality beats training the poor modality on labels directly.
- Same design family as PULSE (2510.24058) and the target of EiV Track E: the privileged signal is matched at the REPRESENTATION, never regressed as a value.

#optihealth #wearables #cross-modal
