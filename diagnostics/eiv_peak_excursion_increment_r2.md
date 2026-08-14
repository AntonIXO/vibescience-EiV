---
direction: higher_better
id: eiv_peak_excursion_increment_r2
name: EiV peak-excursion incremental R2
topic_tags:
- cross-modal
- sensor-text
unit: r2
---

## Description
Incremental R2 for predicting the daily HR peak ABOVE what the daily mean alone already predicts, from the encoder's pooled patch representation. Computed as R2(max | representation) - R2(max | true mean, 1 feature) on a clean fully-observed subset (coverage>0.9, mean>20 bpm to exclude the zero cluster).

WHY THIS EXISTS: raw R2-to-max is almost entirely explained by level. Measured on 23 average-pooled patches (the current encoder): R2(max|repr)=0.4401 but R2(max|true mean alone)=0.4371, so the increment is +0.0030 -- the representation adds essentially NOTHING about peaks beyond knowing the average. Reporting raw R2-to-max instead would have looked like moderate peak skill where there is none.

It also defuses a specific trap: shortening the analysis window makes max and mean converge, so raw R2-to-max rises from 0.466 (24h) to 0.839 (1h) while this increment falls to EXACTLY 0.000. Any window/resolution change must be judged on this metric, never on raw R2-to-max.

Reference values on 23 patches, 24h window, n=4010 clean days: avg-pooled +0.0030; max-pooled patches higher; avg+max concat highest. On the direct excursion target (max minus mean, std 19.1 bpm): avg 0.1685, max 0.1273, avg+max concat 0.2968.

This is a cheap CPU-only ridge probe requiring no GPU and no trained adapter, so it can gate an architecture change BEFORE any training run is spent.

*Unit:* `r2` — *higher_better*

#cross-modal #sensor-text
