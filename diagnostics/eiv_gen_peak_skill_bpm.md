---
direction: higher_better
id: eiv_gen_peak_skill_bpm
name: EiV native-emission hr_peak skill vs strongest trivial baseline
noise_k: 2.0
noise_n: 3
noise_note: 'Phase-C SEED band on emitted hr_peak skill, seeds 42/43/44 off the identical
  Phase-B init with the only variable being the Phase-C seed: -3.260 / -1.6723 / -0.8362
  bpm, RANGE 2.4238 bpm (= eiv_phase_c_seed_band_range_bpm). sigma taken as range/2
  = 1.2119, so k=2 reproduces the full measured band as the detection bar — which
  is exactly the rule exp-seed-band-20260809 preregistered: ''minimum detectable effect
  for all future Phase-C arms must be derived from THIS band: >2.42 bpm on emitted
  hr_peak skill for a single-seed comparison''. NOTE the experiment states the 3-draw
  range UNDERSTATES true spread, so this floor is a LOWER BOUND. Source: exp-seed-band-20260809
  gate gate-band-vs-legacy-spread (PASS).'
noise_sigma: 1.2119
noise_source: measured
requires_control: eiv_gen_peak_donor_gap_bpm
topic_tags: []
unit: bpm
---

## Description
MAE improvement (bpm) of the LM's NATIVE GENERATED hr_peak over the STRONGEST trivial baseline in the SAME evaluated population: max(always-predict-median, donor-prefix, hr_raw-derived = emitted hr_raw + median excursion). MUST be reported with n and paired-bootstrap CI95 over per-sample absolute errors, on the STRICT-CLEAN population (in_train & hr_cov>0.9 & zero_frac==0 & true_mean>40 & true_peak>=40), baseline median RECOMPUTED WITHIN that population. Exists because eiv_gen_peak_corr is a bare correlation that has produced two false passes here, and the prior '-5.88 bpm' was an unpowered n=96 estimate whose sign and magnitude moved 3.8 bpm with the filter. NEVER quote as a bare point estimate.

*Unit:* `bpm` — *higher_better*
