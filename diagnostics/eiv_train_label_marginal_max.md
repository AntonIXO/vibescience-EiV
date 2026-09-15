---
direction: lower_better
id: eiv_train_label_marginal_max
name: eiv_train_label_marginal_max
noise_k: 2.0
noise_n: 4
noise_note: 'MEASURED 2026-09-01 AS EXACTLY ZERO, because the diagnostic as described
  is DETERMINISTIC, not because it was assumed to be (scratch/_label_marginal_determinism.py).


  PROTOCOL: constructed the production Phase-C InstructionDataset for lora_proxy_12ch_wl_band04
  four times, reseeding random / numpy / torch between constructions (seeds 2000-2003),
  and hashed the get_sampler_weights() vector each time. All four SHA-256 digests
  identical (fdae767e0d051385), n=7566, sum 6875.062327. The weights are a pure function
  of the profile and the corpus, so re-running the same config cannot move this number
  and its seed sigma is 0.


  A WRONG ZERO WAS NEARLY REGISTERED, AND THE TELL IS WORTH RECORDING. The first attempt
  built SensorTextPairDataset (the Phase A/B pair dataset) and also got four bit-identical
  digests -- a clean determinism result on the WRONG OBJECT. sampler_balance_strength
  (0.5) and sampler_weight_cap (4.0) are read only by InstructionDataset; on the pair
  dataset every weight came back exactly 1.0 and the sum equalled n, i.e. a balancing
  sampler that balanced nothing. Sum != n (6875.06 vs 7566) is the check that the
  knobs are actually live, and any future re-measurement should assert it.


  WHAT THE ZERO DOES NOT COVER. The REALISED marginal -- what a finite training run
  actually draws under those weights -- does move: top dataset share 0.5978 / 0.5992
  / 0.6036 / 0.6075 over 20k draws, sd 0.0044. That is a DRAW floor and belongs to
  the sampling, not to this diagnostic. If a future evaluation records the realised
  marginal rather than the weight-implied one, it must not be compared against sigma
  0.


  CONSEQUENCE: the 3 evaluations riding on this diagnostic are config checks and can
  be read at face value -- any difference between two profiles is real, since neither
  number has sampling error. This is the one diagnostic in the vault where a bare
  comparison is legitimate.'
noise_sigma: 0.0
noise_source: measured
requires_control: null
topic_tags: []
unit: fraction
---

## Description
Largest share of the sampler-weighted HR-label marginal actually presented to the LM during Phase C, over the 4 target labels (bradycardic/resting/elevated/tachycardic). Measured from the production get_sampler_weights() path, not reasoned about. 0.25 means perfectly balanced; the natural corpus prior is 0.289. Values near 0.5 indicate the sampler is equalizing a partition the target text does not use, which manufactures a saturated prior the LM can exploit as a shortcut: at 0.50 tachycardic the LM predicted 'tachycardic' for every input while scoring per-cell accuracy 1.00 on that one saturated cell. This is a pre-training config diagnostic, checkable before any GPU time is spent.

*Unit:* `fraction` — *lower_better*
