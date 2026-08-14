---
id: hrv-band-mixture-augment-law
name: hrv-band-mixture-augment-law
topic_tags: []
---

## Description
hrv_raw analog of the hr band-mixture law. New _sample_hrv_augment_mean in src/training/datasets.py: with prob hrv_augment_prob (0.4) shift channel 1 so the daily-mean RMSSD lands at a mixture draw — band_prob 0.5 uniform over [55,90] ms (0.910 of supabase truth, 0.406 of unaugmented supply), else uniform over [15,120]; truncation only (no clamp → no atom), raw-space floor 1 ms, NO sub-floor deletion (sub-45 ms is ~half of dense truth, unlike hr where sub-45 bpm was fictional). Encoder tensor and json_slots target shift identically. Defaults off → existing profiles byte-identical (tested).
