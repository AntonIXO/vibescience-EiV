---
created_at: '2026-09-01T10:44:40+00:00'
former_ids: []
id: the-model-learned-the-peak-coverage-bias
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:44:40+00:00'
topic_tags: []
---

## Claim
The model learned the peak target's coverage bias, so its emitted peak tracks HR coverage and the compressed emission scale is a learned reproduction of a biased label.

## Method
Joined each emission capture to M_mask by sample index, computed per-row HR coverage, regressed emitted peak on coverage, then examined the coverage distribution of each evaluation draw when the supabase slopes came out negative.

## Evidence
NOT SUPPORTED, AND THE SIGN IS WRONG ON THE POPULATION THAT MATTERS (scratch/_peak_bias_reaches_emissions.py). Joined every capture back to the canonical mask by sample index (200/200 and 128/128 joined, no losses) and regressed the EMITTED peak on the row's HR coverage.

  capture                slope (bpm per unit coverage)   corr
  v36labels s42 supa            -6.82                  -0.055
  v36labels s43 supa            -6.57                  -0.039
  v36labels s44 supa           -23.37                  -0.153
  v28affect01 s42 supa          -1.90                  -0.019
  v28affect01 s43 supa          -4.72                  -0.048
  v36labels s42 dense           +7.16                  +0.126
  v28affect01 s42 dense        +21.11                  +0.310

The target's own slope over the same range is about +15.7 bpm. On DENSE the emitted slopes are positive and of that order (+7.2, +21.1), consistent with the model having learned the bias. On SUPABASE every slope is NEGATIVE -- the emitted peak goes DOWN as coverage goes up, which the biased-target account cannot produce.

THE REASON IS IN THE COVERAGE DISTRIBUTION, and it invalidates the supabase regression rather than the hypothesis:
  supabase  cov min 0.001  p10 0.226  p50 0.349  p90 0.444  max 0.598   sd 0.1034
  dense     cov min 0.006  p10 0.113  p50 0.373  p90 0.973  max 1.000   sd 0.2798

Supabase HR coverage spans 0.23-0.44 between the deciles and never exceeds 0.60. There is almost no coverage variation to regress on -- sd 0.103 against dense's 0.280 -- so those slopes are fitted over a quarter of the range and all five |corr| are under 0.16. They are noise, not evidence of a negative relationship.

WHAT THIS ACTUALLY ESTABLISHES, and it is worth more than the hypothesis was:
  1. The deployment population is UNIFORMLY SPARSE on HR (median coverage 0.349, max 0.598), not a mix of sparse and dense days. Every supabase row sits in the region where the subsampling test measured the target to be 5-9 bpm low.
  2. So the coverage-bias mechanism is not a gradient the model can learn ACROSS supabase rows -- it is a near-constant offset applied to ALL of them. A constant downward offset on the target is exactly what produces an emitted peak that is uniformly too low, which is the 21.8-24.3 vs 17.4 deficit.
  3. The dense draw, which does span the coverage range, shows the learned positive slope. So the model CAN track coverage; supabase simply offers it nothing to track.

CORRECTION TO MY PREVIOUS ENTRY: I wrote that supabase has 'mean HR coverage 0.789 with a long sparse tail'. That figure is from the problem's original 2026-08-12 description and does NOT match the current eval rows, which read a median of 0.349 and a maximum of 0.598. Either the population changed or the 0.789 refers to a different coverage definition. The number I should have used is the one measured on the rows being scored.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
