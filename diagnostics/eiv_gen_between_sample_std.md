---
direction: higher_better
id: eiv_gen_between_sample_std
name: eiv_gen_between_sample_std
noise_k: 2.0
noise_n: 9
noise_note: 'Measured 2026-09-01 on three same-recipe 3-seed families already on disk
  (scratch/_emission_noise_floors.py), the same nine dense-128 captures used for the
  other emission floors.


  Per-family seed SD: ab_ctrl 2.1056 (20.668/17.172/20.953), w6avg 2.7541 (18.951/14.900/20.158),
  w6msk 3.9875 (17.606/13.437/21.409).


  THE SCALE SCREEN WAS TESTED HERE AND DELIBERATELY NOT APPLIED. On eiv_dense128_hr_raw_mae_bpm
  and eiv_gen_peak_corr, excluding ab_ctrl seeds 42/45 (emit_sd/true_sd 2.33 and 2.17)
  narrows sigma 3.4x and 1.88x respectively. On THIS diagnostic it makes sigma slightly
  WIDER, 3.0506 -> 3.4268 (0.89x), so the screen buys nothing and the registered figure
  uses ALL NINE seeds.


  That asymmetry is the point and is worth stating: over-expressing the output range
  inflates an ERROR metric and dilutes a CORRELATION, but this diagnostic MEASURES
  output spread, so a scale-broken arm is not an outlier on it -- it is high-but-consistent
  (ab_ctrl has the narrowest per-family SD of the three, 2.11). A screen must be justified
  per diagnostic, never inherited.


  Registered 3.0506 bpm, k=2 band +/-6.10 bpm, against family means of 13.4-21.4 bpm.
  WHAT IT IMPLIES: this diagnostic cannot separate a 17 from a 21; it is a collapse
  DETECTOR (is the output near-constant?) and not a fine-grained quality measure,
  exactly as its description says it must be read alongside eiv_gen_peak_corr.'
noise_sigma: 3.0506
noise_source: measured
requires_control: null
topic_tags: []
unit: bpm
---

## Description
Standard deviation (bpm) of the LM's GENERATED hr_peak values across DIFFERENT samples at fixed prompt. Directly operationalizes 'collapses to the same output': near 0 means literal constant output, while a healthy value approaches the std of the true peak distribution. Must be reported alongside eiv_gen_peak_corr, since a model can vary its output without tracking truth.

*Unit:* `bpm` — *higher_better*
