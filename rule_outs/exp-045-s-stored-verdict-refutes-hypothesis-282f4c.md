---
created_at: '2026-08-18T03:44:28+00:00'
former_ids: []
id: exp-045-s-stored-verdict-refutes-hypothesis-282f4c
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-18T03:44:28+00:00'
topic_tags:
- target-style
- control
- baseline
- optihealth
---

## Claim
exp-045's stored verdict 'refutes' / hypothesis_status 'refuted' means the chat_coach target style FAILED, i.e. that switching Phase C from json_slots to conversational annotation + coaching costs numeric grounding. A future session reading the verdict field alone would conclude the lever is dead and should not be retried.

## Method
Direct comparison of the stored verdict field against the four preregistered gate_results recorded on the same experiment, plus reconstruction of how close_experiment derives its verdict from predicted_effects direction matching. Arms: 4 Phase-C runs (1 json_slots control + 3 chat_coach seeds) on data/canonical_1min_conv split=train, scored on a subject-disjoint held-out test split (13 subjects, 0 overlap with the 89 trained on) and on the 200-row production supabase draw. All 12 pipeline stages RC=0.

## Evidence
ALL FOUR PREREGISTERED GATES PASSED, computed by scratch/score_chat_coach_gates.py:
  g1 BLOCKING (hr_raw skill within the 2.26 bpm seed band): PASS.
     json control +4.828 bpm; chat +4.961 / +5.428 / +3.896, median +4.961.
     delta +0.134 bpm against a bar of +2.568. |delta| is 6% of the seed band.
  g2 BLOCKING (extraction rate >= 0.90): PASS. chat 1.000 on all three seeds
     (128/128 each) vs json 119/128 = 0.930.
  g3 INFO: chat abstains on 63-64% of test rows and hedges on 98-99% of PRODUCTION
     supabase rows; json 0.000 on both by construction.
  g4 INFO trap-check: PASS. hr_peak stays negative on every arm (chat -0.487/-1.095/
     -2.528, json -2.117), so no instrument artifact.

WHY THE STORED VERDICT SAYS OTHERWISE — two recording faults, both mine, neither about
the science:
  (1) The three predicted_effects were all declared direction 'none' (the hypothesis
      predicted NO CHANGE, which is the scientifically correct prediction for an
      equivalence test). The close_experiment verdict engine compares observed direction
      against predicted direction and scores any non-zero delta as a miss. An equivalence
      hypothesis is therefore UNSCOREABLE by that engine: it can only ever return
      'refutes' unless the delta is exactly 0.000. The gates, which were written for
      exactly this shape, all passed.
  (2) I recorded eiv_dense128_hr_raw_mae_bpm 'after' as 2.185 — chat's BEST seed — instead
      of the median 2.651. That is precisely the error the seed-band reference file warns
      against ('report the distribution, never the best draw'). The correct median-vs-json
      comparison is 2.651 vs 2.761, a 0.11 bpm difference; the value I stored overstated
      chat by 0.47 bpm. The experiment was already closed and is immutable, so the number
      cannot be edited in place — hence this rule-out.

The correct one-line reading of exp-045: POWERED NULL on the difference. Prose costs no
detectable grounding versus JSON at a 2.26 bpm minimum detectable effect. It is NOT a
demonstration that chat is better, and it is NOT a refutation of the lever.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].

#target-style #control #baseline #optihealth
