---
created_at: '2026-08-20T21:17:00+00:00'
former_ids: []
id: that-the-mean-gap-naive-pooled-mean-present-5bb2e3
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-20T21:17:00+00:00'
topic_tags:
- extent-heterogeneity
- denominator
- pooling
- control
---

## Claim
That the MEAN GAP |naive_pooled_mean - present_only_mean| in bpm is a valid instrument for detecting coverage dilution / extent contraction on short-session wearable cohorts. It is NOT. It is structurally blind to the failure on exactly the cohorts that matter most, and any future extent claim scored on a MEAN rather than on a SPREAD is uninterpretable. This does NOT eliminate the dilution mechanism itself -- the mechanism is confirmed to machine precision by the variance-compression measurement and by the exact arithmetic identity; it eliminates the mean-gap METRIC as the way to measure it.

## Method
Preregistered numeric falsifier committed to git BEFORE the data was generated (commit 7ca34e5, scratch/w4_extent_premise_audit.py docstring, predictions P1-P4 with explicit falsifier bands), then executed on the full corpus. The refuting comparison is between two instruments computed on the IDENTICAL rows, identical denormalisation path (scratch/canon_utils.channel_denorm, which resolves the population_stats.json list-vs-dict trap), identical present-mask: (i) median |naive-present| in bpm, (ii) std(naive)/std(present) within cohort. Follow-up P5 in scratch/w4b_variance_compression.py, also committed before execution.

## Evidence
PREREGISTERED AND FAILED, which is why this is recorded rather than quietly dropped. scratch/w4_extent_premise_audit.py header (committed 7ca34e5 BEFORE the data existed) predicted P4: median |naive_bpm - present_bpm| > 10 bpm on all four short labelled cohorts, falsifier 'median gap < 5 bpm on all four'.

MEASURED (corpus ac7d2b1c, tensor [12547,1440,12], hr_mean corpus median 73.00 bpm, iqr 23.62):
  wesad                 n=15   present 74.7  naive 73.1  gap  5.8
  case                  n=30   present 71.5  naive 73.0  gap  4.9
  nurse_stress          n=102  present 85.7  naive 73.3  gap 12.1
  wearable_exam_stress  n=30   present106.0  naive 78.8  gap 26.3
  -> 2 of 4 over the bar. P4 FAILED as written.

WHY IT FAILED, derived rather than guessed. W4/P2 measured the pooling identity exactly:
  naive_bpm = median + c*(present_bpm - median),  slope 1.0000, intercept 0.00000, R2 1.00000, n=12503.
Therefore gap = (1-c)*|present_bpm - median|. The gap is the product of the dilution factor AND the
cohort's distance from the corpus median. WESAD sits 1.7 bpm from the median and CASE 1.5 bpm, so
both post small gaps while being among the MOST diluted cohorts in the corpus (c = 0.0831 and
0.0285). The metric reports 'fine' precisely where the damage is worst.

THE CORRECT INSTRUMENT, measured in the same session (scratch/w4b_variance_compression.py,
committed before running; P5 PASS, 0 cohorts failing the tracking check):
  cohort                        mean_c   sd_true  sd_naive   ratio
  popane_s4                     0.0187    10.41      0.20   0.0189
  neuro_stress_resilience_hci   0.0226    15.35      0.35   0.0231
  popane_s3                     0.0257     9.61      0.24   0.0251
  case                          0.0285     7.59      0.22   0.0285
  nurse_stress                  0.0419     6.67      0.67   0.1008
  wesad                         0.0831     7.61      0.63   0.0825
  wearable_exam_stress          0.1794     8.13      2.31   0.2838
  lifesnaps                     0.7958     9.29      6.50   0.6992
  pmdata                        0.8703     8.35      7.15   0.8567
  big_ideas_glycemic            0.7581    10.87      7.81   0.7183
  optihealth_supabase           0.6672     9.88      5.01   0.5071
The ratio equals mean coverage c to four decimal places on cohorts near the median (case 0.0285 vs
0.0285). Registered as diagnostic `eiv_extent_variance_compression_ratio`.

REUSABLE LESSON, which generalises past this project: a CONTRACTION toward a fixed point is
invisible to any statistic measured AT that fixed point. When the fill value is the corpus median
-- which is what population normalisation guarantees -- means are the worst possible probe and
spreads are the right one. This is the same class of error as the previously-recorded
'accuracy-gap metric on a skewed categorical target' trap (eiv_readout_linear_gap, deprecated for
exactly this reason): the instrument's blind spot coincided with the phenomenon.

## Links
Rules out for problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].

#extent-heterogeneity #denominator #pooling #control
