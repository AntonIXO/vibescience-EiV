---
direction: higher_better
id: eiv_gen_peak_corr
name: EiV generated peak-HR correlation
topic_tags: []
unit: corr
---

## Description
Pearson correlation between the LM's GENERATED hr_peak value (parsed from the json_slots output) and the true within-day peak heart rate, over a peak-stratified sample. This measures generation-time conditioning, which every encoder-side diagnostic (probe R2, retrieval, HSIC) is blind to: a frozen-encoder probe can read HR at R2=0.99 while the LM emits a constant. Near 0 means the generated value is independent of the sensor input.

*Unit:* `corr` — *higher_better*
