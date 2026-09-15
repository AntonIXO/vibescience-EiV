---
direction: higher_better
id: eiv_eda_state_cmi_over_null
name: eiv_eda_state_cmi_over_null
noise_k: 3.0
noise_n: null
noise_note: Sigma is measured per cell inside the run itself as the sd of 200 within-stratum
  permutations, so the noise floor is re-measured on every cell rather than carried
  between them. The gate is stated in z units (z >= 3 on BOTH estimator families)
  for exactly that reason.
noise_sigma: null
noise_source: null
requires_control: null
topic_tags: []
unit: nats over permutation null
---

## Description
Conditional mutual information I(EDA ; momentary state | stratum) in nats, MINUS its within-stratum permutation null, at the (window, epoch) cells that carry BOTH a privileged EDA observation and a state label. Stratum is dataset or subject (reported separately). Plug-in MI is biased upward and the bias depends on n and bin count, so the raw MI is never the number that decides anything -- the permutation null holds n, the bin edges and both marginals fixed and destroys only the pairing, absorbing the bias exactly. Computed under all three standardisations privileged_stats.json offers (pooled / by_dataset / by_subject) and by TWO estimator families (binned plug-in, and the Ross 2014 k-NN estimator via sklearn), because exp-055's do_not_retry records a sign flip on eda_scr_rate between readout families on identical rows. Its companion confound metric is I(EDA ; dataset): a large marginal with a null conditional is the signature of a corpus indicator rather than a physiological signal.

*Unit:* `nats over permutation null` — *higher_better*
