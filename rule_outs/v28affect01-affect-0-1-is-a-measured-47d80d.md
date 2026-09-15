---
created_at: '2026-08-31T08:36:07+00:00'
former_ids: []
id: v28affect01-affect-0-1-is-a-measured-47d80d
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-31T08:36:07+00:00'
topic_tags: []
---

## Claim
v28affect01 (affect 0.1) is a MEASURED improvement over the healed v25r30 recipe on deployment hr_raw MAE, and should be designated the baseline on the strength of its 3.030-vs-3.203 bpm gap.

## Method
scratch/noise_floor_emission.py (written 2026-08-31) -> logs/noise_floor_emission.json, logs/noise_floor_run.log. Row bootstrap over the FIXED, seeded 200-row supabase capture; the capture itself is bit-reproducible, so the modelled uncertainty is which deployment windows the draw contained. Cross-corpus pairings are refused rather than computed: baseline_sync shares 43 idx values with v28affect01 and all 43 carry different ground truth (13,518-window bundle vs 14,041), so that comparison is reported UNPAIRABLE.

## Evidence
Paired row bootstrap, 4000 resamples, seed 0, on the 200 SHARED rows of the two captures (identical ground truth verified cell by cell, so the pairing is legitimate). v28affect01 vs v25r30: d(hr_raw MAE) = -0.173 bpm against a paired sigma of 0.195 -> |z| = 0.89, i.e. INSIDE one sigma of the draw noise. The headline 3.030 vs 3.203 gap is therefore not a demonstrated difference on this instrument. For scale, the SEED band on this same metric is 3.19 bpm (exp-052/054), an order of magnitude larger again, so the gap is far inside seed noise too.

WHAT DOES CLEAR THE FLOOR, on the same bootstrap: hr_raw corr +0.076 = +2.44 sigma, and hr_peak corr +0.175 = +2.52 sigma. Both exceed the preregistered k=2. So affect 0.1 has a real effect and it is a CORRELATION/ranking effect, not an error-magnitude effect.

Context (unpaired, single-arm sigma): v28affect01 hr_raw corr 0.793 +- 0.039, MAE 3.030 +- 0.155; v25r30 corr 0.717 +- 0.041, MAE 3.203 +- 0.177. v28 vs v27affect (the 0.3 dose) IS decisive on both: dMAE -1.659 = -8.47 sigma, dcorr +0.134 = +3.66 sigma, so the U-curve refutation of the 0.3 dose stands unchanged.

## Links
Rules out for problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
