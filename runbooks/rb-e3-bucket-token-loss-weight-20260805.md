---
created_at: '2026-08-05T15:47:15+00:00'
gates:
- gate-prediction-entropy
- gate-null-delta-sign
- gate-bucket-dose-slope
hypothesis_id: null
id: rb-e3-bucket-token-loss-weight-20260805
priority: 40
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: queued
topic_tags: []
updated_at: '2026-08-05T15:47:15+00:00'
---

## Prompt
In /root/optiHealth-EiV. HYPOTHESIS: lever (a), untested. The categorical slot gets the same loss weight as every other token, so gradient pressure on the ONE token that decides the label is diluted. Concentrate value_loss_weight on the bucket token.

Preregister with a MECHANISM gate on null_delta sign and the bucket dose slope. Same seed/samples/donors. Full gate + E23M + E23N suite.

WATCH FOR: this is the arm most likely to raise entropy WITHOUT raising conditioning - it can simply sharpen the output distribution. The dose-response control is what distinguishes those two outcomes. Arm 1 raised entropy 0 -> 0.370 while its dose slope went flat and its null delta flipped positive. ENTROPY RECOVERY IS NOT CONDITIONING RECOVERY. Note also that gate-prediction-entropy has now passed 0 of 3 attempts across prior arms; if this arm fails it too, that is four, and 'signal strength' stops being the best explanation - say so rather than proposing a fifth variant.

## Preconditions
Same seed/samples/donors. Preregister with mechanism gates.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Intended gates: `gate-prediction-entropy`, `gate-null-delta-sign`, `gate-bucket-dose-slope`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
