---
created_at: '2026-09-01T08:08:27+00:00'
former_ids: []
id: coverage-dilution-is-the-live-second-root-cause-of-deployment-error
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:08:27+00:00'
topic_tags: []
---

## Claim
Coverage dilution (ROOT CAUSE 2) is a live, unclosed defect on the dense deployment population: because the encoder zero-fills masked cells and 0.0 sits at the population median, a day with HR coverage c pools toward median + c*(truth - median), giving a signed-error law (emit - truth) = (c - 1)*(truth - median) with slope 1.0.

## Method
Regressed the record's own dilution law on the dense 128-row emission captures of five arms (scratch/_dilution_law_audit.py), reading per-row HR coverage from M_mask and the fill value from population_stats.json. No GPU.

## Evidence
REFUTED ON THE CURRENT CORPUS AND THE CURRENT ARMS. The record itself already refuted this law on supabase for the July v4w/v5c/v5d arms (slopes -1.5 / -0.47 / +0.42, R2 <= 0.11) while keeping it open as 'a separate defect verified on a different regime'. It has now been re-tested on the dense 128-row draw for all five arms of the v28-v36 campaign, on the post-population-norm corpus [14041,1440,12], and it does not hold there either.

Regression of (emit - truth) on (coverage - 1)*(truth - 72.60), 128 rows each, hr_mean = channel 0, coverage read from M_mask:

  arm             n     slope      R2
  v28affect01   128    -0.093   0.025
  v33dispersion 128    -0.181   0.062
  v34nopeak     128     0.131   0.029
  v35wide       128    -0.209   0.062
  v36labels     128    -0.236   0.048

Predicted slope is 1.0. Four of five are NEGATIVE, the fifth is +0.131, and no arm explains more than 6.2% of the signed-error variance. The law predicts a mean signed error of -1.113 bpm at the draw's mean coverage of 0.443; the observed biases are -1.600, +1.069, -2.370, -2.286 and -0.593, which do not even agree in SIGN across arms fed identical rows.

THE PREMISE IS ALSO NOW WEAKER THAN WHEN IT WAS WRITTEN. The record's mechanism requires 0.0 to sit at the population median. Under the pre-2026-08 rolling per-user z-score that was exactly true. Measured today (scratch/_input_variance_audit.py), the zero fill sits at -0.02 SD of hr_mean -- still near the centre, so the premise survives -- but hr_mean now carries only 13.35% of first-layer input variance instead of 96.07%, so pooling toward the fill on ONE channel can no longer dominate the emission the way the single wearable_exam_stress row suggested.

WHAT REMAINS TRUE: the encoder genuinely has no mask input. TemporalEncoder.forward(self, x) takes the tensor alone, so a masked cell IS indistinguishable from a measured value at the population centre. That is a real architectural gap and it is worth its own arm. What is refuted is the specific quantitative claim that it explains the deployment error, on either population, on any arm ever measured.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
