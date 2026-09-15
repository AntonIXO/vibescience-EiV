---
direction: higher_better
id: eiv_gen_peak_corr
name: eiv_gen_peak_corr
noise_k: 2.0
noise_n: 7
noise_note: 'Measured 2026-09-01 on three same-recipe 3-seed families already on disk
  (scratch/_emission_noise_floors.py), the same nine dense-128 captures used for eiv_dense128_hr_raw_mae_bpm.


  Per-family seed SD: ab_ctrl 0.1102 (0.1728/0.2369/0.0222), w6avg 0.0040 (0.2089/0.2062/0.2010),
  w6msk 0.0529 (0.2160/0.2502/0.1465).


  SCALE SCREEN APPLIED, and re-derived for THIS diagnostic rather than inherited:
  ab_ctrl seeds 42 and 45 emit at 2.33x and 2.17x the true hr_raw spread against 0.95-1.33
  on the other seven, which is a calibration defect with its own signature. Excluding
  them takes the pooled sigma from 0.0706 to 0.0375 -- 1.88x narrower. That leaves
  ab_ctrl with a single calibrated seed and so contributing no within-family SD, hence
  the registered figure rests on w6avg and w6msk (dof 4, 7 calibrated seeds screened).


  Registered 0.0375 is the seed floor CONDITIONAL on a calibrated arm. WHAT IT IMPLIES:
  the calibrated families sit at corr 0.15-0.25, so a k=2 band of +/-0.075 means arms
  in that range are mutually indistinguishable on this diagnostic. Peak conditioning
  is weak everywhere it has been measured, and no single-seed peak-corr comparison
  inside that range is interpretable.'
noise_sigma: 0.0375
noise_source: measured
requires_control: null
topic_tags: []
unit: corr
---

## Description
Pearson correlation between the LM's GENERATED hr_peak value (parsed from the json_slots output) and the true within-day peak heart rate, over a peak-stratified sample. This measures generation-time conditioning, which every encoder-side diagnostic (probe R2, retrieval, HSIC) is blind to: a frozen-encoder probe can read HR at R2=0.99 while the LM emits a constant. Near 0 means the generated value is independent of the sensor input.

*Unit:* `corr` — *higher_better*
