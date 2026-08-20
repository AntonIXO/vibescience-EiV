---
direction: higher_better
id: eiv_extent_variance_compression_ratio
name: Between-subject variance compression of a pooled representation on short-extent
  cohorts
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: null
topic_tags:
- extent-heterogeneity
- pooling
- denominator
- wearables
unit: ratio
---

## Description
std(pooled_representation_readout) / std(present_only_readout), computed WITHIN a dataset cohort, on a per-window scalar (default: mean HR in bpm). 1.0 = the pooling preserves the cohort's between-subject spread; 0.0 = every window in the cohort collapses to the same value and no downstream head can tell two subjects apart.

WHY THIS EXISTS, AND WHY THE OBVIOUS METRIC IS WRONG. The natural instrument is the MEAN GAP |naive_mean - present_mean|. It was preregistered as W4/P4 with a >10 bpm bar and it FAILED (wesad 5.8, case 4.9, nurse_stress 12.1, wearable_exam 26.3 -- 2 of 4). The failure is instructive rather than exculpatory. Unmasked pooling over a population-normalised tensor whose fill 0.0 IS the corpus median implements exactly
    naive_bpm = median + c*(present_bpm - median),  c = present_fraction
(measured W4/P2 on n=12,503: slope 1.0000, intercept 0.00000, R2 1.00000 -- this is arithmetic, not a fit). So the mean gap is (1-c)*|present_bpm - median|, which is SMALL for any cohort whose true mean happens to sit near the corpus median, however severely diluted it is. WESAD (74.7 bpm) and CASE (71.5) sit on the corpus median (73.0), so they post small mean gaps while being maximally compressed. The mean is blind to the failure; the SPREAD is not.

The same arithmetic predicts the ratio EXACTLY equals c, and it does (W4b, corpus ac7d2b1c):
  cohort                        mean_c   sd_true  sd_naive   ratio
  popane_s4                     0.0187    10.41      0.20   0.0189
  neuro_stress_resilience_hci   0.0226    15.35      0.35   0.0231
  popane_s3                     0.0257     9.61      0.24   0.0251
  case                          0.0285     7.59      0.22   0.0285   <- exact to 4 dp
  nurse_stress                  0.0419     6.67      0.67   0.1008
  wesad                         0.0831     7.61      0.63   0.0825
  wearable_exam_stress          0.1794     8.13      2.31   0.2838
  lifesnaps                     0.7958     9.29      6.50   0.6992
  pmdata                        0.8703     8.35      7.15   0.8567
  big_ideas_glycemic            0.7581    10.87      7.81   0.7183
  optihealth_supabase           0.6672     9.88      5.01   0.5071
CASE retains 2.85% of its between-subject spread. That is the quantitative statement of 'four labelled stress cohorts are structurally invisible at the daily grid'.

MANDATORY REPORTING. (a) Per COHORT, never pooled -- the whole point is that cohorts differ by 35x. (b) The cohort's mean coverage c alongside, because ratio~=c is the null model and an intervention must beat THAT, not beat 0. (c) The cohort's distance from the corpus median, since that is what decouples this metric from the mean gap. (d) The STAGE at which the representation was read (raw naive mean / encoder patch tokens / post-projector production), because the encoder does not compute a naive 1440-mean -- it patches at patch_len=120 stride=60 into 23 tokens, so its compression is nearer n_real_patches/23 and MUST be measured rather than assumed equal to c.

CPU-only on the raw arms, no GPU and no adapter needed, so it gates an extent/pooling change before training is spent.

*Unit:* `ratio` — *higher_better*

#extent-heterogeneity #pooling #denominator #wearables
