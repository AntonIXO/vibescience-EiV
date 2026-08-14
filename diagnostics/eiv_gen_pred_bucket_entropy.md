---
direction: higher_better
id: eiv_gen_pred_bucket_entropy
name: EiV generated prediction bucket entropy
topic_tags: []
unit: fraction
---

## Description
Shannon entropy of the DISTRIBUTION OF PREDICTED BUCKETS over a label-stratified sample, normalized by log(n_buckets). Detects the saturated-prior failure that correlation and output-spread both miss: a model that maps every true class to one predicted class (e.g. bradycardic/resting/elevated/tachycardic all -> tachy) still shows non-zero output std and can score per-cell accuracy 1.00 on the saturated cell, while carrying zero discriminative skill. Near 0 means all predictions land in one bucket. Must be read together with eiv_gen_peak_corr: high entropy with near-zero corr means the model varies without tracking truth, low entropy means it has collapsed onto a prior.

*Unit:* `fraction` — *higher_better*
