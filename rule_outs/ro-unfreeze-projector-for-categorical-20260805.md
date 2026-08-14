---
created_at: '2026-08-05T15:47:14+00:00'
former_ids: []
id: ro-unfreeze-projector-for-categorical-20260805
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T15:47:14+00:00'
topic_tags: []
---

## Claim
Unfreezing the projector (lever c) is a route to fixing the CATEGORICAL bucket slot.

## Method
Full arm run + E23M/E23N mechanism controls re-run on arm1 with identical seed, samples and donors as baseline. Closed as exp-e23-arm1-unfreeze-projector-20260805.

## Evidence
Every generation metric improved (entropy 0->0.370, peak_corr +0.159->+0.604, hr_raw FULL +0.604->+0.740, bucket_acc 0.253->0.354) while the mechanism INVERTED: swap delta +0.418 -> -1.128 and null delta -1.750 -> +1.090, i.e. a norm-matched random Gaussian prefix scores the true label BETTER than the real encoder output. Bucket dose slope r=+0.204 p=9.7e-05 -> r=-0.073 p=0.166, CI now includes 0. The two slots swapped roles: unfreezing reallocated per-sample signal to the NUMERIC slots. Keep it for hr_raw/peak; it is refuted as a categorical fix. Verdict directional_only, NOT a null result.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].
