---
direction: lower_better
id: eiv_prefix_swap_bootstrap_p
name: EiV prefix-swap bootstrap p-value
topic_tags: []
unit: fraction
---

## Description
Fraction of bootstrap resamples of the paired prefix-swap delta whose mean is <= 0, i.e. the bootstrap one-sided p-value for 'the conditioning slot reads the sensor input'. Complements a Wilcoxon signed-rank test, which only asks about the median/ranks and can report significance while the MEAN's confidence interval still spans zero -- exactly the discrepancy that occurred at n=40 (Wilcoxon p=0.045 but bootstrap 95% CI of the mean [-0.034, +0.855], Cohen d 0.293). Recording it forces the distinction between 'more pairs moved the right way than not' and 'the average effect is reliably positive'. Lower is stronger evidence of genuine conditioning.

*Unit:* `fraction` — *lower_better*
