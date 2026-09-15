---
arxiv_id_or_url: '2606.07365'
id: geenjaar-2026-robust-ppg-fm-multimodal-supervision-2606-07365
title: A robust PPG foundation model using multimodal physiological supervision (Geenjaar
  et al., 2026)
topic_tags:
- wearables
- cross-modal
---

## Key claims
- Uses co-recorded ECG and respiration in ICU data to SELECT contrastive pairs during PPG pretraining, so noisy PPG segments are kept rather than curated away; claims robustness at inference on consumer-grade data.
- Pretrained on 3x fewer subjects than prior PPG FMs, improves 14/15 downstream tasks including field-like daily-activity and HR prediction. Weight release not stated in the abstract (June 2026) -- check before planning on it.
- Third instance (with PaPaGei-S morphology contrast and Pulse-PPG field pretraining) that PPG-FM quality on wearable data is decided by the pretraining DISTRIBUTION and pair selection, not by encoder scale.

#wearables #cross-modal
