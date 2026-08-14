---
direction: higher_better
id: eiv_gen_between_sample_std
name: EiV generated between-sample HR spread
topic_tags: []
unit: bpm
---

## Description
Standard deviation (bpm) of the LM's GENERATED hr_peak values across DIFFERENT samples at fixed prompt. Directly operationalizes 'collapses to the same output': near 0 means literal constant output, while a healthy value approaches the std of the true peak distribution. Must be reported alongside eiv_gen_peak_corr, since a model can vary its output without tracking truth.

*Unit:* `bpm` — *higher_better*
