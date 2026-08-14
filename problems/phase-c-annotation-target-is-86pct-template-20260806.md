---
created_at: '2026-08-06T13:50:59+00:00'
id: phase-c-annotation-target-is-86pct-template-20260806
paper_refs: []
problem_tags:
- optihealth-eiv
- data-contract-violation
- conditioning-collapse
status: open
title: The json_slots annotation target is 86% fixed template and omits 5 of the 13
  prompted slots
topic_tags:
- cross-modal
- medgemma
- sensor-text
updated_at: '2026-08-06T15:19:56+00:00'
---

## Description
=== UPDATE 2026-08-09. RE-MEASURED ON THE LIVE CORPUS. BOTH HEADLINE CLAIMS ARE NOW STALE, AND THE
EVAL/TRAIN POPULATION DEFECT IS RESOLVED. Unpreregistered; problem evidence, no calibration weight. ===

Measured through the PRODUCTION target builder (InstructionDataset target_style=json_slots, key
target_response), n=500 per population, on the live corpus [10344,1440,12] and the REBUILT
data/eval_v3_1min. Each population measured in its OWN PROCESS, because hr_label_bucket's cut points
are process-global and datasets.py:1738 correctly refuses a second bundle
(scratch/h3_preflight_template_share.py).

                              PHASE C TRAINING pop      STRICT-CLEAN eval pop
  heart_rate_bucket             100.0%  (4 distinct)      100.0%  (4)
  hr_pct                        100.0%  (82)              100.0%  (78)
  hr_raw                        100.0%  (268)             100.0%  (259)
  hr_peak                       100.0%  (370)             100.0%  (357)
  hrv_bucket / hrv_pct / hrv_raw 44.8%  (3/82/187)         45.4%  (3/78/188)
  deep_pct / rem_pct / awake_pct 92.0%  (40/36/28)         92.2%  (34/37/24)
  rr                             40.8%  (3)                41.6%  (3)
  temp                           56.0%  (1  CONSTANT)      55.4%  (1  CONSTANT)
  state                         100.0%  (3)               100.0%  (3)
  mean target length             217.4 chars               218.1 chars
  sensor-varying content          94.7 chars = 43.5%        94.8 chars = 43.5%
  FIXED TEMPLATE SHARE            56.5%                     56.5%

--- 1. THE 86% HEADLINE IS OBSOLETE: TEMPLATE SHARE IS NOW 56.5% ---
The 2026-08-06 measurement found 86.2% / 86.1% on the two populations. It is now 56.5% on BOTH.
The objective is no longer dominated by boilerplate to the degree that framing assumed. The
"entropy is in the boilerplate" argument is weakened by a factor of ~3 in the quantity it rests on.
It is NOT eliminated -- a majority of the target is still fixed -- but any arm justified by "86% of
the target is template" must be re-justified at 56.5%.

--- 2. THE FIVE "PHANTOM SLOTS" ARE NO LONGER PHANTOM, AND THE POPULATION DEFECT IS GONE ---
The record's central correction was that hrv_bucket/hrv_pct/hrv_raw/rr/temp were 0.0% present on the
strict-clean eval population against 76.2% in training -- "the eval population and the training
population are not the same task". That is RESOLVED: hrv_* is 44.8% train / 45.4% eval and rr is
40.8% / 41.6%. The two populations now differ by less than one percentage point on EVERY slot.
The eval rebuild (which re-derived the strict-clean pool from the live corpus rather than a
five-hours-stale one) is what fixed it.
CONSEQUENCE: p4 hrv_acc is now a legitimate metric on this eval population. It was not before.

--- 3. WHAT SURVIVES, AND IS NOW THE ONLY LIVE PART OF THIS PROBLEM ---
(a) temp is a LITERAL CONSTANT on both populations (1 distinct value over 500 targets, ~55% present).
    The model is trained to emit it and credited for reproducing it.
(b) deep_pct/rem_pct/awake_pct are NO LONGER constants (40/36/28 distinct values vs 1 previously),
    so the "p4 deep_sleep_acc=1.0 is satisfied by a constant" defect is fixed. p4 now reports
    deep_sleep NaN with total=0 on this eval population, which is null-discipline working, not a bug.
(c) state carries 3 distinct values and is still derived from dataset METADATA via infer_state_label,
    NOT from the sensor tensor -- so it still cannot test sensor grounding.
PROPOSED ACTION, reduced in scope: drop or fix `temp` only. The 13-slot prompt no longer needs
pruning; 12 of 13 slots now render with real variation.

--- 4. METHOD NOTE ---
This is the THIRD plan premise in two days found stale because the corpus moved under it (the others:
the A/B-vs-C cohort split, which does not exist on the *_wl chain; and the dryad "broadcast defect",
which the anchor fix had already repaired). Each re-measurement cost roughly one CPU minute.
STANDING RULE: re-measure a premise on the live artifacts immediately before building an arm on it.
A number older than the most recent merge is a hypothesis, not a fact.

#optihealth-eiv #data-contract-violation #conditioning-collapse

=== CORRECTION 2026-08-06, SAME DAY, BEFORE ANYONE ACTED ON THIS. I OVERCLAIMED SECTION 1. ===
Caught while building probe_e36: a target rendered for a sample outside the strict-clean filter DID contain
hrv_bucket/hrv_pct/hrv_raw/temp. Re-measured with the slot-presence rate computed per population (n=400-500 each):

  slot presence            STRICT-CLEAN eval pop        PHASE C TRAINING pop (lifesnaps+pmdata)
  hrv_bucket/pct/raw             0.0%                            76.2%  (73.8% on the 500-sample redraw)
  rr                             0.0%                            76.2%
  temp                           0.0%                            76.2%
  deep/rem/awake_pct        present but CONSTANT 0          present AND VARYING

WHAT WAS WRONG: "_build_json_slots_target omits 5 of the 13 prompted slots" as a property of the TARGET BUILDER.
It does not. It renders all 13 whenever the underlying channels are present, which on the Phase C training
distribution is ~76% of samples.

WHAT IS ACTUALLY TRUE, AND IS A WORSE PROBLEM: the STRICT-CLEAN EVALUATION POPULATION IS NOT REPRESENTATIVE OF
THE TRAINING DISTRIBUTION. The strict-clean filter (hr_cov>0.9 & zero_frac==0 & mean>40) selects days with
excellent HR coverage, and those days have ZERO HRV/RR/temp coverage -- 0/500, against 73.8% in training.
So EVERY number in E26-E36, including my own exp-e34 and exp-e35, is measured on a subpopulation where 5 of the
13 slots the model was trained to emit are structurally absent and 3 more are frozen at a constant. The eval
population and the training population are not the same task. That is a NEW defect, it belongs to this problem,
and it was invisible until slot presence was measured per-population.

WHAT SURVIVES UNCHANGED: the headline. Recomputed independently on both populations, counting a slot as varying
only if it takes >1 distinct value in that population:
  STRICT-CLEAN   mean target 175.1 chars, varying 24.1 = 13.8%  -> 86.2% fixed template
  TRAINING POP   mean target 247.8 chars, varying 34.1 = 13.9%  -> 86.1% fixed template
The template share is 86% on BOTH, so the "the objective's entropy is in the boilerplate" argument does not
depend on the error. Sections 3, 4 and 5 stand. Section 2 stands only for the strict-clean population.
Also unchanged: p4 hrv_acc = 0.0 everywhere is still not a model failure, but the reason is now different --
the probe evaluates on a population where the hrv slot is not rendered at all, not that it is never trained.

METHOD NOTE FOR THE VAULT: I generalised from a single 600-sample draw taken entirely inside one filter, and
stated a property of the CODE from a property of the DATA. The check that caught it cost one forward pass.
Any claim of the form "the pipeline never does X" must be measured on at least two populations before it is
written down.

MEASURED 2026-08-06 on the strict-clean population (n=600 targets rendered through the PRODUCTION path src/training/datasets.py::_build_json_slots_target, plus a 10-sample native-generation dump on adapter medgemma_12ch_e33.seed_1). Unpreregistered: this is problem evidence, not an experiment, and carries no calibration weight.

--- 1. FIVE OF THIRTEEN PROMPTED SLOTS ARE NEVER IN THE TARGET ---
prompts/json_slots_prompt.md demands 13 keys. The rendered target contains 8, identically on 600/600 samples:
  heart_rate_bucket, hr_pct, hr_raw, hr_peak, deep_pct, rem_pct, awake_pct, state
MISSING: hrv_bucket, hrv_pct, hrv_raw, rr, temp. The model is instructed to emit them and never shown one.
CONSEQUENCE: p4_bucket_accuracy/hrv_acc = 0.0000 in EVERY probe JSON in the project's history. That metric has always been scoring a slot the model was never trained to produce. It is not a model failure.

--- 2. FOUR OF THE EIGHT SURVIVING SLOTS ARE LITERAL CONSTANTS ---
Distinct values over n=600 strict-clean samples:
  hr_pct     1 distinct ('50',  100.0%)      deep_pct   1 distinct ('0', 100.0%)
  rem_pct    1 distinct ('0',   100.0%)      awake_pct  1 distinct ('0', 100.0%)
deep/rem/awake are 0 because the sleep_prob_* channels sum to ~0.031 per step instead of ~1.0 (see problem canonical-mask-marks-degenerate-zero-channels-as-present-20260805). This is why p4 deep_sleep_acc = 1.0000 everywhere: it is satisfied by emitting a constant, and P4 has been reporting it as a pass.
'state' carries 0.78 bits (2 distinct, 77% 'stress') and is derived from dataset METADATA via infer_state_label, NOT from the sensor tensor -- so it cannot test sensor grounding either.

--- 3. THE HEADLINE NUMBER ---
mean target length 175.1 chars; mean sensor-varying content 24.1 chars = 13.8%.
=> 86.2% OF EVERY ANNOTATION IS A FIXED TEMPLATE EMITTABLE WITHOUT READING THE SENSOR.
The entire sensor-dependent payload is hr_raw (7.87 bits) + hr_peak (8.49 bits) + the bucket derived from the peak (1.53 bits). Everything else in the cross-entropy is boilerplate.

--- 4. THE MODEL DOES EXACTLY WHAT THAT OBJECTIVE REWARDS ---
Native generation, seed_1, 10 samples spanning true peak 69.1 -> 185.0 bpm:
  template reproduced perfectly (parse rate 0.975 at n=320; all 8 keys, right order, right types)
  heart_rate_bucket = 'tachycardic' on 10/10 (and 302/312 at n=320)
  hr_peak emitted 121,130,130,130,130,130,131,120,130,131 while truth went 69.1 -> 185.0
  hr_raw emitted 51-61 with the tenths digit ALWAYS '.0' while truth ran 54.2 -> 79.5
  worst case idx 13776: true peak 69.1 bpm (bradycardic) -> emitted 'tachycardic', hr_peak 121.0
Format learned, content not. That is the signature of an objective whose entropy is in the boilerplate.

--- 5. WHY THIS COMPOUNDS WITH THE LOSS BALANCE ---
The CLM term is itself only 3.4-4.8% of Phase C's total loss (median across the three e33 seed logs; 0.6-2.5% over the first 500 steps), the rest being the probe_weight=1.0 auxiliary hidden-state regression on unnormalised bpm-scale targets. So the sensor-dependent 13.8% of the text sits inside a term that is itself a few percent of the objective. STATED AS A CAVEAT, NOT A MEASUREMENT: loss-magnitude share is not gradient share; rb-e33 gradient attribution is the correct confirmation and should be run before this framing is treated as established.

--- 6. WHAT THIS DOES NOT EXPLAIN ---
It does NOT explain why hr_peak fails while hr_raw partially works, since both sit in the same 13.8% under the same objective (hr_raw corr +0.55/+0.57 at emission on both seeds; hr_peak +0.126/-0.023). And it does not contradict exp-e34's localisation: the information IS present at the emission position (fitted decode +4.2 bpm skill, donor gap +9.916) and unused by the model's own head (-1.9 bpm, donor gap +0.48).

--- 7. PROPOSED FIXES, CHEAPEST FIRST ---
(a) delete the 5 phantom slots from the prompt OR render them in the target -- and stop scoring hrv_acc until one of those happens; (b) drop or fix the three sleep percentages and hr_pct rather than training the model to emit constants and then crediting itself for them (P4 deep_sleep_acc 1.0); (c) re-weight the token loss toward the sensor-dependent spans, which is runbook rb-e3-bucket-token-loss-weight, still queued and now much better motivated; (d) re-measure the CLM/probe loss balance.

#cross-modal #medgemma #sensor-text #optihealth-eiv #data-contract-violation #conditioning-collapse

#cross-modal #medgemma #sensor-text #optihealth-eiv #data-contract-violation #conditioning-collapse
