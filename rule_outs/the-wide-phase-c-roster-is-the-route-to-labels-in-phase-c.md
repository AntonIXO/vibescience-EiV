---
created_at: '2026-09-01T09:08:01+00:00'
former_ids: []
id: the-wide-phase-c-roster-is-the-route-to-labels-in-phase-c
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:08:01+00:00'
topic_tags: []
---

## Claim
Giving Phase C the wide 20-dataset roster is the route to using labels in Phase C, since it takes valence from 0 to 1,298 windows and arousal from 0 to 609.

## Method
Re-read the on-disk emission captures of all four wide-roster Phase C arms against the noise floors measured today, on both draws, reporting emit_sd/true_sd alongside MAE so calibration failures are separated from accuracy ones. No GPU, no retraining.

## Evidence
ALREADY TESTED, FOUR TIMES, AND IT FAILS DEPLOYMENT BADLY. lora_proxy_12ch_v11full, _v12conly, _v13flat and _v14long were all run on 2026-08-30 and their emission captures are on disk. Nobody had read them against a MEASURED noise floor, because the floors were only measured today -- the old supabase sigma (3.145) was wider than the trivial baseline, which is exactly why the vault records that single-seed comparisons on it were uninterpretable. At sigma 0.8601 / 0.7566 they are interpretable, and the answer is not close (scratch/_wide_roster_readout.py):

  DENSE (sigma 0.7566, k=2 band 1.513)
    arm            n     MAE      vs incumbent      z    emit/true scale
    v28affect01  128   2.851           +0.000   +0.00   1.11
    v36labels    128   4.017           +1.166   +1.54   1.32
    v11full      128  21.277          +18.426  +24.35   0.57
    v12conly     128  21.585          +18.734  +24.76   0.48
    v13flat      128  24.611          +21.760  +28.76   2.03
    v14long      128  22.161          +19.310  +25.52   2.76

  SUPA (sigma 0.8601, k=2 band 1.720)
    v11full      200   5.490           +2.460   +2.86   0.26
    v12conly     200   4.384           +1.354   +1.57   0.36
    v13flat      200   4.658           +1.628   +1.89   1.24
    v14long      200   4.954           +1.924   +2.24   1.20

Every wide-roster arm is 7-9x the incumbent's dense error, at z +24 to +29. This is not a marginal trade: it is the deployment collapse the campaign spent August diagnosing.

THE SCALE COLUMN NAMES THE FAILURE MODE. emit_sd/true_sd is 0.26-0.57 on v11full/v12conly (both draws) -- the arms are emitting a COMPRESSED range, i.e. regressing toward a constant -- while v13flat and v14long sit at 2.03-2.76, over-expressing it. Both are outside the [0.85, 1.35] domain in which the dense floor is valid, so the MAE figures are secondary: the primary finding is that these arms lost calibration entirely. The incumbent (1.11) and v36labels (1.32) are inside.

WHAT THIS SETTLES FOR THE STANDING GOAL. 'Labels in all three phases' via the Phase C roster has been tried and is refuted on deployment. The affect datasets are short lab sessions (CASE 41 min, POPANE ~40 min) against a production population of full wearable days; admitting them to the phase that trains the EMITTER trades the deployment distribution away. Phase A and B can carry them -- they are representation stages and their 19-dataset roster already includes them -- but Phase C cannot, and the reason is distributional, not architectural.

SO THE REMAINING LEVER IS THE ONE ALREADY QUEUED: v39scaled asks whether the caption's provenance leak matters, on the narrow roster, with the deployment distribution intact. That is the sharper question and it is running.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
