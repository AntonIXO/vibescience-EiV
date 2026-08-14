---
created_at: '2026-08-05T15:47:14+00:00'
former_ids: []
gates:
- gate-peak-excursion-increment
- gate-null-delta-sign
- gate-bucket-dose-slope
hypothesis_id: null
id: rb-e1-dual-avg-max-patching-20260805
notes: []
priority: 20
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: abandoned
topic_tags: []
updated_at: '2026-08-05T17:51:25+00:00'
---

## Prompt
!!! ABANDONED 2026-08-05 (STEP 2 SITE GATE FAILED) — DO NOT IMPLEMENT, DO NOT RETRAIN !!!

The CPU gate this runbook required BEFORE any GPU time was run at the ACTUAL production pooling
site and FAILED. Recorded as rule_out ro-dual-avg-max-pooling-downstream-20260805.

WHY THE ANSWER CHANGED AGAIN. Step 1 re-aimed this runbook from 'upstream patching' to
'downstream readers' on the basis of a probe that pooled the RAW ENCODER output to ONE vector
per channel and NEVER RAN THE PROJECTOR. Production pools AFTER query_proj + cross_attention +
RMSNorm, to k=4 slots per channel, followed by a unit-RMS renormalisation. Measured at the real
site (scratch/probe_e1_step2_sitegate.py replicates cross_modal.py:218-281 and swaps ONLY the
operator), the incumbent avg-pool is NOT peak-blind:

  arm                                      R2->max  increment
  1 avg k=4  (INCUMBENT, 96 slots)          0.8966   +0.4067
  2 max k=4  (96 slots)                     0.8810   +0.3911   <- max-only is WORSE than avg-only
  3 avg k=2 + max k=2 (INTERVENTION)        0.9056   +0.4157   <- +0.0090, within noise
  4 avg k=4 + max k=4 (192 slots, 2x VRAM)  0.8925   +0.4026   <- MORE capacity, WORSE

On the strict-clean population (no zero-bpm steps, mean>40, n=1641) the incumbent scores
increment +0.7211 against a control R2(max|true mean)=0.0873. The peak is already there.

CONTROLS ALL PASS (scratch/probe_e1_step2_controls.py): shuffled target -0.0315 (no leak);
trained +0.7211 vs random-init +0.5929 (training matters, unlike the Step 1 arm); presence
channels zeroed +0.7261 (read from VALUE channels, not the missingness pattern).

BONUS FINDING, matters for every probe in this problem: the population filter every prior probe
used (mask coverage>0.9, mean>20) is ZERO-CONTAMINATED — 59.1% of those 4010 samples contain at
least one zero-bpm 'present' step, mean zero fraction 0.334, population hr_mean 44.7 bpm. Use the
STRICT filter (no zero-bpm steps at all, mean>40, n=1641, hr_mean 69.9) for anything measuring
hr_peak. scratch/probe_e1_step2_deconfound.py implements both.

WHERE THE PROBLEM ACTUALLY IS. Representation R2 for hr_peak = 0.81 while E0 measured hr_peak
generation skill at NEGATIVE vs an always-predict-median constant (-5.88 bpm FULL, -6.48 clean
SPARSE). The information is present and the readout cannot use it. That is a READOUT problem.
Go to rb-e4-lora-lm-head-20260805 (now top-ranked) or rb-e2-schema-reorder-20260805.
Do not spend another arm on the representation.

--- SUPERSEDED CONTENT BELOW, KEPT FOR PROVENANCE ONLY ---
Step 1 (hyp-eiv-patching-is-linear-not-average-20260805, directional_only) correctly established
that src/models/patching.py does unfold + nn.Linear(8->192) and does NOT average within patches.
That part stands. Its conclusion that the fix belongs in the downstream readers does not.

## Preconditions
REVISED 2026-08-05: STEP 1 has been RUN (CPU, no GPU) and it RE-AIMED this runbook. Do NOT implement the upstream version. See the STOP block at the top of the prompt. Requires canonical tensors merged with --window-days 1. Same seed/samples/donors as prior arms. Preregister with a mechanism gate on null_delta SIGN before any training run.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Intended gates: `gate-peak-excursion-increment`, `gate-null-delta-sign`, `gate-bucket-dose-slope`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
