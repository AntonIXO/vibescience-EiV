---
direction: lower_better
id: eiv_train_label_marginal_max
name: EiV max HR-label training marginal
topic_tags: []
unit: fraction
---

## Description
Largest share of the sampler-weighted HR-label marginal actually presented to the LM during Phase C, over the 4 target labels (bradycardic/resting/elevated/tachycardic). Measured from the production get_sampler_weights() path, not reasoned about. 0.25 means perfectly balanced; the natural corpus prior is 0.289. Values near 0.5 indicate the sampler is equalizing a partition the target text does not use, which manufactures a saturated prior the LM can exploit as a shortcut: at 0.50 tachycardic the LM predicted 'tachycardic' for every input while scoring per-cell accuracy 1.00 on that one saturated cell. This is a pre-training config diagnostic, checkable before any GPU time is spent.

*Unit:* `fraction` — *lower_better*
