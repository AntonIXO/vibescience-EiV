---
created_at: '2026-08-08T08:18:45+00:00'
former_ids:
- blocker-for-h1b-and-for-every-future-conditioning-claim-repair-p3-and-p6-and-mak
gates: []
hypothesis_id: null
id: blocker-for-h1b-and-for-every-future-303644
notes: []
priority: 1
problem_id: two-probes-in-the-shipped-eiv-probe-ca39d5
status: queued
topic_tags: []
updated_at: '2026-08-08T08:18:45+00:00'
---

## Prompt
BLOCKER FOR H1b AND FOR EVERY FUTURE CONDITIONING CLAIM. Repair p3 and p6, and make the suite
incapable of reporting a number it did not measure.

MEASURED SYMPTOMS, from the first complete end-to-end 1-minute run
(outputs/probes/v3_full_suite.json, adapter outputs/checkpoints/12ch_v3/phase_c):
  p3_counterfactual : swap_measured=False, swap_total=0, swap_correct=0, swap_attribution_acc=0.0
  p6_shuffle_control: bucket_acc_ordered=0.0, bucket_acc_shuffled=0.0, shuffle_gap=0.0
In both cases 0.0 is what the report shows and 0.0 is NOT what was measured -- nothing was measured.
A reader sees "swap attribution 0.0" and concludes the conditioning is dead; a reader sees
"shuffle gap 0.0" and concludes there is no shortcut. Neither conclusion is supported.

WHY IT IS URGENT RIGHT NOW. The same run shows the classic readout gap: hr is decodable from the
LLM hidden states at R2 0.9649 (MAE 1.16 bpm) while the GENERATED bucket scores 0.4609 against a
0.569 always-predict-majority baseline, and sensor attention mass is 0.1277 against a 0.2238 uniform
baseline. p3 and p6 are exactly the instruments that would tell us whether that gap is a dead
conditioning path or a dominant output prior, and both are dark. Until they work, H1b's two arms
cannot be interpreted causally: a positive could be a shifted prior, and a null is indistinguishable
from a broken probe path.

STEPS
1. Find why p3 never enters its loop. swap_total=0 means zero pairs were constructed, so the failure
   is in pair CONSTRUCTION, not in generation -- note p4 on the same run and the same checkpoint
   produced buckets with parse_failure_rate 0.0000, so the model does emit parseable answers.
2. Same for p6: both arms are exactly 0.0, so its own bucket extraction is failing where p4's
   succeeds. Diff the two paths; p4 was just repaired in commit bba9bcf and may have diverged from
   what p6 still assumes.
3. STRUCTURAL FIX, more important than either point fix: a probe that cannot run must emit null,
   never 0.0, and the harness must mark the run as incomplete. Three of seven probes in this suite
   were returning numbers that could not mean what they appeared to mean (p4 was the third -- it
   graded the daily MEAN with hardcoded 60/80/100 cut points while training emits a bucket from the
   daily PEAK at corpus cut points 145.6/112.0, so a PERFECT model would have scored 24.11%).
   That is a suite-level audit, not three isolated bugs.
4. Re-run the corrected suite on the existing 1-minute adapter
   (outputs/adapters/medgemma_12ch_v3_1min_e2e) so there is a trustworthy baseline to compare
   H1b's arms against. No retraining needed for this step.

ACCEPTANCE: p3 reports swap_measured=True with swap_total>0, p6 reports two DIFFERENT arm values,
and any probe that fails emits null plus an explicit reason in the report.

## Links
Queued for problem [[two-probes-in-the-shipped-eiv-probe-ca39d5]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
