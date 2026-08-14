---
created_at: '2026-08-05T15:47:14+00:00'
former_ids: []
gates:
- gate-sparse-corr-survives-zero-exclusion
- gate-skill-vs-constant-baseline
hypothesis_id: null
id: rb-e0-per-sample-audit-20260805
notes: []
priority: 10
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: abandoned
topic_tags: []
updated_at: '2026-08-05T16:46:01+00:00'
---

## Prompt
COMPLETED 2026-08-05. Per-sample persistence implemented and audit executed. Findings recorded to vibescience as rule_out 'the-claim-arm-1-was-a-genuine-win-for-the-numeric-slots-is-only-half-true-and-mu'.

ARTIFACTS NOW IN REPO:
- scratch/eval_e23_gates.py PATCHED: now writes a per-sample sidecar <out>_per_sample.json (idx, group, true_hr_mean, true_hr_peak, true_bucket, hr_coverage, parsed, pred_hr_raw, pred_hr_peak, pred_bucket). Offline decomposition no longer needs a GPU re-run.
- scratch/audit_e0_population.py: reconstructs the EXACT scored population with NO GPU by replaying the deterministic filter + seed 0.
- scratch/audit_e0_bimodality.py: CLUMP-ONLY artifact-ceiling control.
- scratch/audit_e0_decompose.py: the decomposition report.
- outputs/probes/e0_arm1_gates_persample.json, _per_sample.json (96 rows), e0_arm1_decomposition.json, e0_population_audit.json, e0_bimodality_ceiling.json

RESULT SUMMARY (re-run reproduced Arm 1 aggregates BIT-EXACTLY, so rows are the original population):
1. hr_raw SPARSE +0.965 is NOT a bimodality artifact -> +0.830 clean (only 14% lost), BEATS the clump-only ceiling +0.9065, skill +4.27 bpm vs median. The hr_raw claim SURVIVES, weaker.
2. hr_peak IS an artifact -> SPARSE +0.646 -> +0.198 clean (69.3% was the zero cluster), does NOT beat the clump-only ceiling +0.8789, clean skill -6.48 bpm (LOSES to a constant).
3. NEW, not anticipated by this runbook: hr_peak on FULL coverage (zero contamination present = 0) also LOSES to a constant, skill -5.88 bpm, despite corr +0.276.
4. NEW: the preregistered outcome gate gate-peak-corr-positive PASSED at +0.604 pooled on that artifact. Withdraw the credit.
5. NEW: 'SPARSE entropy 0.537 would pass the 0.50 gate alone' is WITHDRAWN -> 0.245 clean, acc 0.306 vs 0.333 majority baseline. The apparent bucket diversity was 'bradycardic' emitted on degenerate all-zero samples.

DOWNSTREAM CONSEQUENCE: runbook rb-e1-dual-avg-max-patching-20260805's framing of hr_peak as 'conditions poorly at +0.276' is too generous -- hr_peak has NEGATIVE skill against a constant in both regimes. E1's honest-ceiling note becomes more important, not less.

STATUS NOTE: closed as 'abandoned' rather than 'done' only because it recorded rule_outs and no preregistered hypothesis (audit, not an experiment => no calibration weight). Work is COMPLETE; do not re-open.

--- ORIGINAL PROMPT BELOW ---
In /root/optiHealth-EiV. scratch/eval_e23_gates.py currently persists only aggregate stats, not per-sample predictions, which blocks all offline decomposition. Patch it to dump per-sample rows (true hr_mean, true hr_peak, predicted hr_raw, predicted hr_peak, predicted bucket, true bucket, hr coverage) to JSON.

Then re-analyse outputs/adapters/medgemma_12ch_arm1_attnfix:
1. The SPARSE-split hr_raw corr of +0.965 is SUSPECTED TO BE A BIMODALITY ARTIFACT.
2. Report skill vs a constant baseline next to EVERY correlation.
3. Record findings against problem phase-c-hr-peak-saturated-prior-20260805.

## Preconditions
No GPU needed (~10 min). Adapter outputs/adapters/medgemma_12ch_arm1_attnfix and scratch/eval_e23_gates.py must exist.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Intended gates: `gate-sparse-corr-survives-zero-exclusion`, `gate-skill-vs-constant-baseline`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
