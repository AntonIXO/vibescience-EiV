---
created_at: '2026-08-12T07:38:07+00:00'
id: the-bucket-before-digits-schema-order-is-a-decode-side-defect-a-4b-lm-cannot-com
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-12T07:38:07+00:00'
topic_tags:
- control
- baseline
- cross-modal
- denominator
---

## Claim
The bucket-before-digits schema order is a decode-side defect: a 4B LM cannot compute the a>=b threshold comparison in-activation, so the categorical bucket token is decoupled from the model's own numeric belief (supported by the answer-first structured-output literature and the 9.11>9.9 failure class).

## Method
Pre-committed decision rule (scratch/self_consistency_analysis.py, committed efade74 before any capture output was read). Capture: scratch/run_v4w_emission_capture.sh -> outputs/probes/v4w_emissions_n128.json (128 raw texts + parsed slots + truths, production prompt, greedy, max_new_tokens 128). Fair grading: scratch/v4w_fair_grading.py. Rule-agreement ceiling: scratch/rule_mismatch_ceiling.py. All CPU except the 7-minute capture generation pass.

## Evidence
REFUTED by direct measurement on captured raw emissions, decision rule pre-committed at efade74 BEFORE data landed; capture at 9acf511.

SELF-CONSISTENCY (bucket token vs bucket(model's OWN emitted hr_peak/hr_raw digits), n=128 deterministic production-path generations from v4w phase_c):
  at 143/109, v4w's TRAINING-time rule: 128/128 = 1.0000
  at 157.72/127.00, the grading rule:   14/128  = 0.1094
  hrv 30/60 rule (never changed, clean control): 62/62 = 1.0000
The pre-committed rule required >=0.80 under the training rule with a >=0.15 margin over the grading rule for verdict A (instrument mismatch, comparator intact): observed 1.0000 vs 0.1094. Maximal verdict A.

Anton's counterexample (emitted hr_peak 125.1 WITH heart_rate_bucket 'elevated') is coherent under the training rule: elevated = peak>=109. The 'contradiction' existed only against cut points recomputed from a corpus rebuilt 8 minutes after v4w's manifest was written (created_at 16:28:46 vs corpus mtime 16:36; training log hdataset_ab.log records 'cut points from corpus: tachycardic>=143 elevated>=109'; v4w manifest predates the hr_buckets pinning block).

CONSEQUENCES:
1. A 4B instruction-tuned LM (MedGemma-1.5-4b-it + LoRA r=16) CAN threshold-compare 3-digit decimals in-activation, emitting the categorical result BEFORE the operand digits, at 190/190 coherence. The external literature prediction (answer-first field order collapses derived-field accuracy, GSM8K 95.75->53.75) does not transfer to this fine-tuned joint-emission setting.
2. Schema reorder (H-5 digits-first) and parse-time bucket derivation attack a non-existent defect for hr buckets: deriving the bucket from v4w's own digits at the pinned rule scores 0.4219 vs the token's 0.3906 -- both below majority 0.4453, because the PEAK DIGITS are weak (corr 0.269, skill -1.43 bpm), not the comparator.
3. Fair grading of v4w at its own rule: acc 0.5547, majority 0.5234, skill +0.0312 -- positive but INSIDE the 0.0781 seed band. v4w was never distinguishably better than control; it was graded against its own label rule while later arms were not (perfect-bucketer ceiling under cross-rule grading: 0.5391).
4. The binding constraint on hr bucket skill is the ENCODER's peak representation (avg-pooled readers carry level not excursion; ref pooling-and-order-statistics.md), not the LM decode path. The v5d hr_peak lattice collapse (1202 target values -> 6 emitted) remains a separate, real decode-side phenomenon -- but the threshold COMPARATOR is not the defect.

SCOPE: n=128 greedy deterministic emissions, one draw (default_rng(0)), v4w checkpoint; hrv control n=62. Does not rule out order effects under sampling temperature or for schemas the model was not fine-tuned on.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#control #baseline #cross-modal #denominator
