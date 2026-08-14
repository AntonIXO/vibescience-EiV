---
created_at: '2026-08-05T15:47:15+00:00'
former_ids: []
gates:
- gate-prediction-entropy
- gate-null-delta-sign
- gate-bucket-dose-slope
hypothesis_id: null
id: rb-e2-schema-reorder-20260805
notes: []
priority: 30
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: queued
topic_tags: []
updated_at: '2026-08-05T15:47:15+00:00'
---

## Prompt
In /root/optiHealth-EiV. HYPOTHESIS: the JSON schema emits heart_rate_bucket as SLOT 1, so the LM must commit to a category BEFORE writing the HR numbers it is demonstrably good at. Moving hr_peak/hr_raw ahead of the bucket lets the label condition autoregressively on the model's own emitted digits - a far stronger causal path than a soft prefix fighting a template prior. It also shrinks the prior the bucket must beat (the one thing Arm 1 did achieve: prior spread 5.08 -> 2.83 nats).

Supporting: hr_raw generation corr +0.740 full / +0.965 sparse (BUT see runbook rb-e0-per-sample-audit-20260805 - may be a bimodality artifact); hr_peak only +0.276 full-coverage; full-coverage bucket entropy 0.073 with 47/48 still tachycardic.

KNOWN RISK: a prior attempt to re-derive the bucket post-hoc from the model's own generated hr_peak did NOT rescue it (outputs/probes/e23k_arm1_derived.json: slot entropy 0.249 vs derived 0.320, both fail the 0.50 gate). So the gain must come from AUTOREGRESSIVE CONDITIONING DURING GENERATION, not post-hoc recomputation. Design the arm accordingly and state this risk in the preregistration.

Full gate + E23M + E23N suite, same seed/samples/donors.

## Preconditions
Same seed/samples/donors as prior arms. Preregister with a mechanism gate.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Intended gates: `gate-prediction-entropy`, `gate-null-delta-sign`, `gate-bucket-dose-slope`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
