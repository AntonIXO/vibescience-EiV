---
created_at: '2026-08-05T22:30:47+00:00'
id: the-hr-peak-native-emission-deficit-can-be-quoted-as-5-88-bpm-vs-always-predict-
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T22:30:47+00:00'
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Claim
The hr_peak native-emission deficit can be quoted as '-5.88 bpm vs always-predict-median' (or -6.48 clean sparse) as a stable, well-established property of the system. ALSO ELIMINATED as explanations for it: (a) the adapter is a draw from the Stage C mode-collapse basin documented in run_12ch_pipeline.sh ('constant ~54-67 bpm output regardless of input', ~50% of runs); (b) the model emits an independent sample from the correct marginal p(hr_peak), which would make the negative skill pure arithmetic; (c) the negative sign is an artifact of zero-contaminated samples in the evaluation population.

## Method
exp-e33-collapse-and-premise-audit-20260805. Five pure-CPU probes over cached measurements, ~1 min, zero GPU, no model loaded. Triggered by READING THE PROJECT'S OWN PIPELINE SCRIPT (run_12ch_pipeline.sh), whose header documents Stage C bistability and a mode-collapse basin -- a fact that had never been checked against the adapter under study despite matching the failure signature.

probe_e33a_collapse_signature.py -- dispersion test against the documented collapse basin: std(emitted)/std(true), fraction of emissions inside the 54-67 bpm band, distinct-value count, plus the sharp DONOR-SWAP test (a collapsed model ignores the prefix, so real and donor emissions must be near-identical).

probe_e33b_prior_sampling.py -- tests whether emissions are an independent draw from the correct marginal, using the GINI MEAN DIFFERENCE (closed-form sorted-array identity, cross-checked against 400 explicit permutation draws) as the exact predicted MAE under that null, plus a shuffle control.

probe_e33c_alignment_audit.py -- pairing and provenance audit of the two preceding probes.

probe_e33d_premise_reconciliation.py -- puts the sidecar path (source of the -5.88 figure) and the npz path on the same footing and compares their populations directly.

probe_e33e_contamination_artifact.py -- progressive decontamination of the sidecar's OWN rows (same decode, same parse, same model) using the strict-clean criteria applied everywhere else in E26-E33, with the baseline median RECOMPUTED WITHIN each filtered population (importing a median across populations is itself a sign-flipping error) and a bootstrap CI95 at every step.

## Evidence
(a) MODE COLLAPSE -- REFUTED EMPHATICALLY.
  std(emitted)/std(true)                        0.9999    (collapse requires ~0)
  fraction of emissions in the documented 54-67 bpm band   0.0000
  emitted n=300: mean 116.78, std 20.758, min 80.4, median 110.6, max 175.2, 129 distinct values
  true    n=1000: mean 120.43, std 23.315, min 74.5, median 114.31, max 185.0
DONOR SWAP, the sharp test: mean |real - donor| emission = 22.184 bpm, only 0.33% bit-identical, corr(real, donor) = 0.0260, donor MAE 24.179 vs real 14.263 (real prefix worth +9.916 bpm). A collapsed model ignores the prefix; this one demonstrably does not. Stage C bistability is real and documented, but THIS adapter landed in the good basin.

(b) PRIOR SAMPLING -- REFUTED, though the arithmetic is worth retaining.
Gini mean difference of true hr_peak: 25.609 bpm (test, closed form) and 26.439 (train), cross-checked by 400 explicit permutation draws at 25.610 +/- 0.924. Actual free-running decode MAE 14.263 -- an 11.346 bpm gap below the prior-sampling prediction. Shuffling the emissions against truth costs +9.982 bpm (14.263 -> 24.245), so emissions are strongly paired to their own samples. KEEP THE IDENTITY AS A NULL: Gini mean difference >= mean absolute deviation from the median for ANY distribution, so a model sampling the correct marginal is MATHEMATICALLY GUARANTEED negative skill. It is just not the mechanism here.

(c) CONTAMINATION ARTIFACT -- REFUTED as a sign-flipper, but it exposed that the EVAL POPULATION IS UNFIT.
The sidecar population (n=96, the -5.88 measurement path) contains 8 rows whose true 'daily peak' is 0.0 bpm -- physiologically impossible for a MAXIMUM -- and 25.0% below 90 bpm versus 5.0% strict-clean. Distribution mean 100.07 / std 38.18 / median 99.96 / min 0.0, versus strict-clean 120.43 / 23.32 / 114.31 / 74.5. Progressive decontamination with the baseline median RECOMPUTED WITHIN each population:
  ALL rows (as the premise was scored)   n=96  MAE 28.13  base 25.76  SKILL -2.37 [-7.01,+2.38]  r=0.604
  drop true peak == 0 bpm                n=88  MAE 25.08  base 18.93  SKILL -6.15 [-10.22,-1.89] r=0.154
  + require daily mean > 40 bpm          n=81  MAE 23.69  base 19.06  SKILL -4.64 [-8.43,-0.65]  r=0.202
  + require coverage > 0.9 (STRICT-CLEAN) n=34  MAE 22.94  base 19.49  SKILL -3.45 [-8.52,+2.02]  r=0.422
  drop true peak < 90 bpm (aggressive)   n=72  MAE 23.07  base 17.74  SKILL -5.33 [-9.27,-1.49]  r=0.084
The sign stays negative at every level, so contamination does not flip it. But the point estimate swings across a 3.8 bpm range purely with the filter, the strict-clean CI95 CROSSES ZERO at n=34, and removing the impossible zeros makes skill WORSE (-2.37 -> -6.15) because those rows inflated the constant baseline more than the model's error.

WHY THE HEADLINE FIGURE IS NOT QUOTABLE: it is a bare point estimate from n=96 on a population containing physiologically impossible targets, and on the strict-clean subset the effect is -3.45 with a confidence interval that includes zero. The deficit appears real but is SMALLER and FAR LESS CERTAIN than -5.88 conveys.

TWO OF MY OWN ERRORS, CAUGHT BY THE PREREGISTERED PROVENANCE GATE AND RETRACTED:
  * corr(emitted, true) reported as 0.0870 used pairing y[:300] instead of y[te]; fr_pred is stored over the TEST SPLIT. Correct value 0.5791. An 'independent sample from the marginal' story had already been started on the wrong number.
  * A claimed 'SIGN CONTRADICTION' between +4.229 bpm and -5.88 bpm was spurious: probe_e26_digit_ceiling.py:200 documents free_run as an 'Autoregressive decode driven by the fitted linear classifiers', so fr_pred is a FITTED-PROBE decode while the sidecar's pred_hr_peak is a parsed NATIVE emission. Different quantities; comparing them manufactured a false crisis.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#cross-modal #medgemma #collapse #sensor-text
