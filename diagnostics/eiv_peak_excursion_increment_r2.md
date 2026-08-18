---
direction: higher_better
id: eiv_peak_excursion_increment_r2
name: EiV peak-excursion incremental R2
noise_k: 2.0
noise_n: 5
noise_note: 'RECIPE-level band (not seed): the identical avg-vs-max pooling probe
  re-run across every available Phase-B checkpoint, same rows/split/control, only
  the checkpoint varying: gap min +0.3963, median +0.4059, max +0.4420 => BAND WIDTH
  0.0457 over 5 checkpoints. sigma = range/2 = 0.02285 so k=2 reproduces the band.
  Source: exp-e19b-pooling-recipe-level-20260815 gate g3_report_gap_band (PASS), key_numbers.gap_band_width=0.0457.
  The shuffle control on the same fits scored -0.0001..+0.0018 (g2_shuffle_control_is_dead
  PASS), so this band is genuine checkpoint spread and not readout-manufactured variance.
  SCOPE: verified on 5 of 6 checkpoints, all sharing the 23-token geometry; 12ch_v2
  at 359 tokens was OOM-skipped and is UNTESTED.'
noise_sigma: 0.02285
noise_source: measured
requires_control: null
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
