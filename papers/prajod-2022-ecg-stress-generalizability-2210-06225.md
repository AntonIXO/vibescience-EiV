---
arxiv_id_or_url: '2210.06225'
id: prajod-2022-ecg-stress-generalizability-2210-06225
title: On the Generalizability of ECG-based Stress Detection Models (Prajod, Andre)
topic_tags:
- optihealth
- deployment-distribution
---

## Key claims
- LOSO within-dataset vs cross-dataset (WESAD <-> SWELL-KW) for ECG deep models and HRV-feature models: deep models win within-dataset, HRV-feature models 'considerably outperform' them cross-dataset, on both directions.
- Direct evidence for the multimodal-ai skill's pitfall 7 (shallow beats deep on low-dim physiological data) in the exact regime EiV deploys into: a subject and device the model never saw.
- For EiV: the raw_shallow bound (W-SWEEP +0.0666 z+5.02 vs trained head +0.0218) is the same phenomenon; and it argues the held-out protocol must be cross-DATASET as well as cross-subject, since supabase is its own device/scenario.
- Code: not stated. Modality: ECG/HRV wearable -- same modality as EiV's hr_mean/hrv_rmssd channels.

#optihealth #deployment-distribution
