---
created_at: '2026-08-17T22:06:19+00:00'
former_ids: []
id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
paper_refs: []
problem_tags:
- optihealth-eiv
- conditioning-collapse
- subject-heldout
- matched-baseline
status: open
title: Phase C is trainable only for JSON slot filling; the product surface is conversational
  annotation + coaching
topic_tags:
- target-style
- optihealth
- medgemma
- deployment-distribution
- readout
updated_at: '2026-08-17T22:06:19+00:00'
updates: []
---

## Description
OPEN QUESTION (registered 2026-08-18, exp-chat-coach-20260818).

Every Phase-C arm ever trained on this project uses `target_style: json_slots` — a 13-key
schema whose entropy sits in slot values. The optiHealth product surface is a coaching
assistant that must ANNOTATE and EXPLAIN a day in prose. Nothing in the pipeline has ever
been trained for that, so 'can the same soft-token prefix support a prose surface without
losing numeric grounding?' is UNANSWERED, not answered negatively.

WHAT IS ALREADY KNOWN (do not re-litigate):
- json_slots hr_raw emission skill is POSITIVE and seed-robust: dense-128 skill
  +4.08/+2.44/+4.36 bpm at seeds 42/43/44 (v9band04), corr 0.87/0.71/0.91.
- hr_peak skill is NEGATIVE on every seed (-2.88/-2.73/-3.16). Encoder excursion limit,
  not a target-style property.
- Phase-C seed band: 0.0781 p4 hr_acc, 2.42 bpm hr_peak skill, 2.26 bpm hr_raw skill.
  Any chat-vs-json delta smaller than that is noise.

THE CONFABULATION FRAMING IS REFUTED (measured before launch, 2026-08-18). An earlier
version of this framing said json_slots 'forces a value into every key' and therefore
fabricates absent channels. `_build_json_slots_target` guards every slot with np.isfinite,
so absent channels are ALREADY omitted from the target, and shipped arms emit a slot their
own row's target would have omitted only 5/2585 (0.2%, v9band04_s42 supabase), 9/1290
(0.7%, dense-128), 3/2556 (0.1%, v11lmhead supabase). Whole-channel confabulation is real
but small and json_slots is not its cause. Script: scratch/where_is_confabulation.py.

THE SURVIVING, MEASURED DEFECT IS THIN EVIDENCE. np.isfinite passes on ONE observed epoch
out of 1440. On optihealth_supabase — the PRODUCTION population — respiratory rate is
backed by 1-4 observed epochs on 213/223 rows and skin temperature on 219/223; only 6 and
4 rows are truly absent. Corpus-wide 227 rows state rr and 219 state temp from <15 observed
epochs. json_slots states these as flat facts because a JSON enum has nowhere to put a
hedge. Script: scratch/thin_evidence_census.py.

A SECOND, INDEPENDENT DEFECT FOUND WHILE SCOPING: the json_slots prompt is 327 templated
tokens against an effective_max_prompt_tokens of 256, so it is SILENTLY TRUNCATED at
training and inference. The lost 71 tokens are the tail of the schema — the `temp` and
`state` key definitions and the 'output ONLY the JSON object' instruction. Every shipped
Phase-C arm was trained with a decapitated prompt. Script: scratch/check_prompt_truncation.py.
The chat_coach prompt is 216 templated tokens and fits. This is a confound in the pending
A/B and must be stated with any result: chat gets a whole prompt, json does not.

DATA SITUATION (load-bearing): the historical Phase-C training bundle data/canonical/
(10344,1440,12) is GONE from this disk. Only eval_v3_1min (5955,1440,12) survives, in the
pre-epoch-label format. Converted to the current contract as data/canonical_1min_conv
(17,725 label events recovered through the declared labels.yaml source mapping). Label
TIMESTAMPS were destroyed upstream and are NOT recoverable; events sit at sentinel epochs
so the declared majority reduction in infer_state_label returns the historical day-level
answer. NEVER use this bundle for state_at(epoch) or within-day transition claims.
Because the training corpus changed, a FRESH json_slots control is retrained on the same
bundle — comparing chat against the shipped v9band04 numbers would confound target_style
with the corpus change.

#target-style #optihealth #medgemma #deployment-distribution #readout #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
