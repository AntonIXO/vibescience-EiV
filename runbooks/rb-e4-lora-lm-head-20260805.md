---
created_at: '2026-08-05T15:47:15+00:00'
former_ids: []
gates:
- gate-prediction-entropy
- gate-null-delta-sign
- gate-vram-ceiling
hypothesis_id: hyp-e9-lmhead-lora-20260814
id: rb-e4-lora-lm-head-20260805
notes:
- at: '2026-08-20T08:20:38+00:00'
  text: '2026-08-20 triage: ALREADY EXECUTED. This runbook''s arm ran as exp-e9-lmhead-lora-20260814
    (verdict refutes) with follow-up exp-e11-linear-head-20260814 (directional_only);
    checkpoint outputs/checkpoints/v11lmhead_s42, captures logs/e9_*/v11lmhead_*.
    The lm_head LoRA lever was bought and scored 5 days before this triage — the runbook
    was simply never marked. The E26-scoped expectation (~4 bpm coarse magnitude)
    was the tested claim; the refute closes it. Successor (numeric head / binned-ordinal
    target) named in the runbook''s own ''wrong lever'' note, unqueued by choice.'
priority: 15
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: done
topic_tags: []
updated_at: '2026-08-20T08:20:38+00:00'
---

## Prompt
rb-e4-lora-lm-head -- LoRA the readout (lm_head), not just the transformer blocks.

=== STATUS: UNBLOCKED by exp-e26-digit-ceiling-20260805, but RE-SCOPED. ===
This is a PARTIAL-CREDIT arm worth ~4 bpm, NOT a fix for hr_peak. Launch only with that expectation recorded in the experiment's predicted_effects.

=== HISTORY OF THE CAPACITY ARGUMENT (read this before citing any number) ===
1. E25 claimed 'E4 is not capacity-limited' from a RidgeCV readout (MAE 11.815, R2 +0.5107). WRONG FUNCTION CLASS -- RidgeCV maps h -> a SCALAR, and lm_head cannot emit a scalar. That claim was WITHDRAWN.
2. E26 re-measured in lm_head's REAL class: per-position multinomial classifier over {'0'..'9','.',END}, argmax, FREE-RUNNING decode with each predicted digit fed back via KV cache, n_train=700/n_test=300.

=== WHAT E26 ESTABLISHED ===
PASSES (value level, per-sample verified):
  free-running decoded MAE   14.263 bpm
  always-predict-median      18.492 bpm      CI95 of difference [-6.008, -2.493], excludes 0
  donor-prefix free-running  24.179 bpm      WORSE than median; CI95 vs median [+3.685, +7.681]
  CI95 (FR - donor) [-12.320, -7.519]  => the ~4.2 bpm gain is genuinely per-sample.
So a rank>=10 LoRA on lm_head CAN realise a correction worth ~4.2 bpm over the trivial predictor. Capacity is sufficient for THAT.

FAILS (per-position digit accuracy, after donor correction):
  ZERO non-leading digit positions clear BOTH bars (>+0.05 over majority AND >0 over their own donor).
  pos0 +0.0167/+0.0367 | pos1 +0.0433/+0.0800 | pos2 +0.0600/-0.0233 | pos3 +0.0333/+0.0267 | pos4 +0.0467/-0.0433   (vs_majority / vs_donor)
  The tenths digit (pos4) shows NOTHING: it clears the majority bar but its donor beats it.
=> The readout recovers MAGNITUDE (~90 vs ~110 vs ~130), NOT precise digits.

=== EXPECTED PRIZE, STATED HONESTLY ===
~4 bpm of coarse magnitude correction. Current hr_peak skill is -5.88 bpm vs always-predict-median, so this arm plausibly moves the sign but will NOT produce accurate peak values. If you need accurate peaks, this is the wrong lever -- go to the architecture question (a numeric head that regresses a scalar, or binned/ordinal target encoding).

=== METHODOLOGICAL RULE DISCOVERED IN E26 (applies to any numeric-emission probe here) ===
Teacher-forced decode is NOT an upper bound on free-running decoded error for multi-token numerics. Measured: TF 60.873 bpm vs FR 14.263 bpm -- the oracle-prefix arm was catastrophically WORSE. Under teacher forcing a leading-digit error is unrecoverable ('9' then '9.7' for a true 119.7); free-running decode is self-consistent. ALWAYS measure free-running.
Also: every accuracy gate needs BOTH a majority baseline AND a donor null. Scoring against majority alone produced a FALSE PASS in both E25 and E26.

=== WHAT REMAINS TRUE (localisation chain, unaffected) ===
1. prefix carries the peak: R2 0.8084, increment +0.7211 (strict-clean n=1641)
2. survives 34 frozen layers, linearly present at the emitting token: R2 0.6850 neutral context, donor null -0.0014
3. emitted value has NEGATIVE skill vs always-predict-median: -5.88 bpm full, -6.48 bpm clean sparse
4. frozen head emission measured directly: MAE 30.602, corr -0.089 -- worse than a constant; observed emitting '90.0' against true 100.3 / 118.6 / 94.0

=== BLOCKING DESIGN ISSUE -- STILL UNRESOLVED, RESOLVE BEFORE LAUNCH ===
lm_head.weight IS TIED TO THE INPUT EMBEDDINGS (verified: identical data_ptr; lm_head(hidden_states[-1]) == logits, max abs diff 0.0e+00). A naive LoRA on lm_head also perturbs the input embedding table for all 262145 tokens, including the sensor sentinels the conditioning rides on. Choose and state one in config_note:
  (a) untie: clone lm_head.weight into an independent Linear before attaching the adapter;
  (b) add a separate un-tied output head and adapt only that;
  (c) accept the coupling -- only after a cheap probe shows it does not degrade the prefix.
Assert input-embedding weights are bit-identical before/after adapter attach.

=== CODE PREMISE (verified) ===
src/training/lora.py:15-23 DEFAULT_TARGET_MODULES = q/k/v/o_proj + gate/up/down_proj; lm_head and the final norm are ABSENT.
src/training/pipeline.py:2896 calls attach_lora_adapters(runtime.model, rank/alpha/dropout) with NO target_modules override and NO config path => needs a CODE change (add lora_target_modules threaded from configs/training.yaml), not a YAML edit.

=== GATES ===
gate-prediction-entropy | gate-null-delta-sign | gate-vram-ceiling (<11.25 GiB on the 5070)
gate-prediction-entropy has passed 0 of 3. If E4 makes it 0-for-4, stop adding cheap levers and give the architecture question a real hearing.

=== EVALUATION ===
Same seed/samples/donors. scratch/eval_e23_gates.py (per-sample sidecar), scratch/diag_e23M_effect_vs_prior.py, scratch/diag_e23N_dose_response.py. Report hr_peak skill in bpm vs always-predict-median on the STRICT clean population, never as a bare correlation. Expect ~4 bpm, not a fix.

## Preconditions
Same seed/samples/donors. RTX 5070 budget: stay under 11.25 GiB, no OOM.

## Log
- **2026-08-20T08:20:38+00:00** — 2026-08-20 triage: ALREADY EXECUTED. This runbook's arm ran as exp-e9-lmhead-lora-20260814 (verdict refutes) with follow-up exp-e11-linear-head-20260814 (directional_only); checkpoint outputs/checkpoints/v11lmhead_s42, captures logs/e9_*/v11lmhead_*. The lm_head LoRA lever was bought and scored 5 days before this triage — the runbook was simply never marked. The E26-scoped expectation (~4 bpm coarse magnitude) was the tested claim; the refute closes it. Successor (numeric head / binned-ordinal target) named in the runbook's own 'wrong lever' note, unqueued by choice.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Run as [[hyp-e9-lmhead-lora-20260814]].
Intended gates: `gate-prediction-entropy`, `gate-null-delta-sign`, `gate-vram-ceiling`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
