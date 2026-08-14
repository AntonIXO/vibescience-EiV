---
direction: higher_better
id: eiv_gen_peak_skill_bpm
name: EiV native-emission hr_peak skill vs strongest trivial baseline
requires_control: eiv_gen_peak_donor_gap_bpm
topic_tags: []
unit: bpm
---

## Description
MAE improvement (bpm) of the LM's NATIVE GENERATED hr_peak over the STRONGEST trivial baseline in the SAME evaluated population: max(always-predict-median, donor-prefix, hr_raw-derived = emitted hr_raw + median excursion). MUST be reported with n and paired-bootstrap CI95 over per-sample absolute errors, on the STRICT-CLEAN population (in_train & hr_cov>0.9 & zero_frac==0 & true_mean>40 & true_peak>=40), baseline median RECOMPUTED WITHIN that population. Exists because eiv_gen_peak_corr is a bare correlation that has produced two false passes here, and the prior '-5.88 bpm' was an unpowered n=96 estimate whose sign and magnitude moved 3.8 bpm with the filter. NEVER quote as a bare point estimate.

*Unit:* `bpm` — *higher_better*
