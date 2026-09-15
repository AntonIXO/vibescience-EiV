---
created_at: '2026-09-01T07:11:46+00:00'
former_ids: []
id: the-6-29-bpm-supabase-seed-band-is-an-absolute-floor-at-every-error-level
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:11:46+00:00'
topic_tags:
- deployment-distribution
- baseline
---

## Claim
The 6.29 bpm seed band recorded for eiv_supabase_hr_raw_mae_bpm is an ABSOLUTE floor that applies to every arm at every error level, so no single-seed comparison on deployment MAE is ever interpretable and the metric cannot gate a promotion.

## Method
Observational scaling analysis over every multi-seed capture family already on disk: group emission_*_{supa,dense}_summary.json by base tag with a trailing _sNN seed token, compute each family's band (max-min) and its mean MAE, then regress band on mean. No GPU, no new runs. Caveat stated in the script output: the families differ in recipe as well as error level, so this is a PRIOR for how to read the band, not a replacement for a matched family.

## Evidence
Eleven seed families already on disk, measured from their own capture summaries (scratch/_seed_band_scaling.py, zero GPU):
  SUPA   ab_ctrl n=3 mean 3.36 band 1.34 (rel 0.40) | conly n=2 mean 3.14 band 0.80 (0.25) | w6avg n=3 mean 2.93 band 0.45 (0.15) | w6msk n=3 mean 3.49 band 0.39 (0.11) | c_patch120 n=2 mean 24.07 band 0.96 (0.04)
  DENSE  w6avg n=3 mean 3.18 band 0.92 (0.29) | w6msk n=3 mean 3.23 band 1.51 (0.47) | conly n=3 mean 4.09 band 3.39 (0.83) | ab_ctrl n=2 mean 6.37 band 7.61 (1.19) | c_patch120 n=2 mean 26.54 band 10.48 (0.40)
  VAULT  v7noatom n=3 mean 12.27 band 6.29 (rel 0.51) -- the family the 6.29 figure comes from

corr(mean MAE, band) = +0.561 over 11 families; linear fit band = 0.221*mean + 1.243; median relative band 0.40.

The decisive observation is the low-error families themselves: w6msk sits at mean 3.49 with a band of 0.39, and w6avg at 2.93 with 0.45 -- an order of magnitude below the 6.29 figure, on the SAME diagnostic and the same 200-row supabase draw. An absolute 6.29 floor is therefore contradicted by direct measurement, not merely by extrapolation. The 6.29 number is real but belongs to arms whose MAE is 9.5-15.8, i.e. arms still partly captured by the emission attractor the atom fix removed.

Projection at today's level (arms at MAE 1.86-3.03, mean ~2.5): multiplicative reading gives ~0.99 bpm, the linear fit ~1.80 bpm. So the v36-vs-incumbent gaps of 1.168 (supa) and 1.166 (dense) are AT THE EDGE of the band -- neither dismissible as noise nor safe to promote on. That is precisely why the matched Phase-C seed family is the right next spend rather than a fourth single-seed arm.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].

#deployment-distribution #baseline
