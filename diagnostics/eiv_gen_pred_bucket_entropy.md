---
direction: higher_better
id: eiv_gen_pred_bucket_entropy
name: eiv_gen_pred_bucket_entropy
noise_k: 2.0
noise_n: 7
noise_note: 'Measured 2026-09-01 on three same-recipe 3-seed families already on disk
  (scratch/_emission_noise_floors.py), the same nine dense-128 captures used for the
  other emission floors. Buckets are the corpus-derived caption edges (bradycardic
  <60, resting 60-124, elevated 124-155, tachycardic >=155), entropy normalised by
  log(4).


  Per-family seed SD: ab_ctrl 0.1639 (0.5860/0.2606/0.4586), w6avg 0.0604 (0.3220/0.2244/0.2114),
  w6msk 0.0823 (0.3991/0.2370/0.2929).


  SCALE SCREEN APPLIED and re-derived for this diagnostic: excluding ab_ctrl seeds
  42/45 (emit_sd/true_sd 2.33 and 2.17) narrows the pooled sigma 0.1115 -> 0.0722,
  1.54x. The direction makes mechanistic sense here -- an arm that over-expresses
  its range pushes emissions across more bucket boundaries and so scores spuriously
  HIGH entropy (0.586 and 0.459, the two largest values in the whole set). Registering
  the naive figure would have rewarded the scale defect twice: once by inflating the
  point estimate, once by widening the band that protects it.


  Registered 0.0722 rests on w6avg and w6msk (dof 4, 7 calibrated seeds screened);
  ab_ctrl retains one calibrated seed and so contributes no within-family SD.


  WHAT IT IMPLIES: calibrated arms sit at 0.21-0.40 normalised entropy, i.e. the emission
  distribution is concentrated in roughly one and a half of four buckets. With a k=2
  band of +/-0.144 spanning most of that range, this diagnostic can currently only
  distinguish a hard collapse from a non-collapse, not one healthy arm from another.'
noise_sigma: 0.0722
noise_source: measured
requires_control: null
topic_tags: []
unit: fraction
---

## Description
Shannon entropy of the DISTRIBUTION OF PREDICTED BUCKETS over a label-stratified sample, normalized by log(n_buckets). Detects the saturated-prior failure that correlation and output-spread both miss: a model that maps every true class to one predicted class (e.g. bradycardic/resting/elevated/tachycardic all -> tachy) still shows non-zero output std and can score per-cell accuracy 1.00 on the saturated cell, while carrying zero discriminative skill. Near 0 means all predictions land in one bucket. Must be read together with eiv_gen_peak_corr: high entropy with near-zero corr means the model varies without tracking truth, low entropy means it has collapsed onto a prior.

*Unit:* `fraction` — *higher_better*
