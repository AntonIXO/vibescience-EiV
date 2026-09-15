---
created_at: '2026-08-05T15:47:15+00:00'
former_ids: []
gates:
- gate-prediction-entropy
- gate-null-delta-sign
- gate-bucket-dose-slope
hypothesis_id: null
id: rb-e3-bucket-token-loss-weight-20260805
notes:
- at: '2026-08-30T07:28:27+00:00'
  text: 'SUPERSEDED BY THE ROSTER FINDING, 2026-08-30 -- same reasoning as rb-e2-schema-reorder-20260805,
    queued 24 days and never started.


    This runbook proposes reweighting the bucket-token loss inside the C objective.
    But the C profile it was written against (band04 family) has now been measured
    to admit 7,592 of 14,092 windows and 44.2% of label-epochs on the synced bundle,
    with six label channels at ZERO reach. Reweighting a loss term over supervision
    that is half-absent tests the weight, not the mechanism.


    RE-QUEUE AFTER hypothesis a-full-a-b-c-retrain-whose-roster-actually-052f9a closes,
    against the v11 roster. If the v11 chain restores deployment health, this arm
    becomes interpretable for the first time; if it does not, the bucket-token weight
    is not the next thing to spend GPU on anyway.'
priority: 40
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: abandoned
topic_tags: []
updated_at: '2026-08-30T07:28:27+00:00'
---

## Prompt
In /root/optiHealth-EiV. HYPOTHESIS: lever (a), untested. The categorical slot gets the same loss weight as every other token, so gradient pressure on the ONE token that decides the label is diluted. Concentrate value_loss_weight on the bucket token.

Preregister with a MECHANISM gate on null_delta sign and the bucket dose slope. Same seed/samples/donors. Full gate + E23M + E23N suite.

WATCH FOR: this is the arm most likely to raise entropy WITHOUT raising conditioning - it can simply sharpen the output distribution. The dose-response control is what distinguishes those two outcomes. Arm 1 raised entropy 0 -> 0.370 while its dose slope went flat and its null delta flipped positive. ENTROPY RECOVERY IS NOT CONDITIONING RECOVERY. Note also that gate-prediction-entropy has now passed 0 of 3 attempts across prior arms; if this arm fails it too, that is four, and 'signal strength' stops being the best explanation - say so rather than proposing a fifth variant.

## Preconditions
Same seed/samples/donors. Preregister with mechanism gates.

## Log
- **2026-08-30T07:28:27+00:00** — SUPERSEDED BY THE ROSTER FINDING, 2026-08-30 -- same reasoning as rb-e2-schema-reorder-20260805, queued 24 days and never started.

This runbook proposes reweighting the bucket-token loss inside the C objective. But the C profile it was written against (band04 family) has now been measured to admit 7,592 of 14,092 windows and 44.2% of label-epochs on the synced bundle, with six label channels at ZERO reach. Reweighting a loss term over supervision that is half-absent tests the weight, not the mechanism.

RE-QUEUE AFTER hypothesis a-full-a-b-c-retrain-whose-roster-actually-052f9a closes, against the v11 roster. If the v11 chain restores deployment health, this arm becomes interpretable for the first time; if it does not, the bucket-token weight is not the next thing to spend GPU on anyway.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Intended gates: `gate-prediction-entropy`, `gate-null-delta-sign`, `gate-bucket-dose-slope`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
