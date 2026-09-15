---
arxiv_id_or_url: '2505.05694'
id: amin-2025-stress-reproducibility-consumer-wearables-2505-05694
title: Extending Stress Detection Reproducibility to Consumer Wearable Sensors
topic_tags:
- optihealth
- wearables
- deployment-distribution
---

## Key claims
- 35 students, three lab stressors, four devices. HRV-only LOSO AUROC: Empatica E4 wrist 0.73 (rest vs all stressors) / 0.905 (rest vs mental arithmetic); Garmin Forerunner 55 wrist 0.961; Polar H10 chest 0.954. Adding EDA lifts E4 to 0.953.
- A model pre-trained on other studies transfers to Biopac/Polar/Garmin but reads only 0.723 on E4 — device-specific HRV quality, not the stressor, is what breaks cross-study transfer.
- Ceiling context for EiV's momentary-state pool: HRV-only wrist, mixed stressors, subject-held-out sits at ~0.73 in a single controlled lab study; EiV's 21-corpus flow-vs-stress pool ceiling of 0.72 (union of 15 multi-scale features + encoder) is at that published level, not below it.

#optihealth #wearables #deployment-distribution
