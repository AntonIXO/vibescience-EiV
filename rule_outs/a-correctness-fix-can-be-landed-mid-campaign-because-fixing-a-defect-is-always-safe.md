---
created_at: '2026-09-01T07:56:05+00:00'
former_ids: []
id: a-correctness-fix-can-be-landed-mid-campaign-because-fixing-a-defect-is-always-safe
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:56:05+00:00'
topic_tags: []
---

## Claim
A correctness fix to the training path can be landed at any time, because fixing a defect can only improve things and does not need to be treated as an experimental variable.

## Method
Timeline reconstruction: git log timestamp of the fix commit against the seed-family driver's own stage banners and the adapter mtimes, then a two-direction verification of the flag (scratch/_prompt_budget_audit.py with and without EIV_RESOLVE_NESTED_CONTEXT).

## Evidence
REFUTED BY A NEAR-MISS I CAUSED TODAY, recorded so the pattern is not repeated.

At 10:53 I committed ba5a112, a genuine fix: resolve_model_context_tokens could not see medgemma's nested text_config, so the budget fell back to 256 and the 317-token json_slots prompt was silently truncated by 61 tokens -- losing the `temp` and `state` key definitions and the 'Output ONLY the JSON object' instruction from EVERY training and inference prompt.

The Phase-C seed family was running at the time. Cell 1 (v36labels seed 43) started 10:41 under the old behaviour; cells 2-4 would have started after 10:53 under the new one. The family's entire purpose is to isolate the SEED, and I would have put a prompt change inside it -- so the resulting band would have mixed seed variance with a recipe change, and the promotion decision it exists to inform would have been unreadable.

WHY THE INTUITION IS WRONG: a fix that changes what the model SEES is a recipe change regardless of being a fix. Arms v28 through v37 are comparable to each other precisely BECAUSE they all ran under the truncated prompt. Landing the fix mid-campaign does not make old results better, it makes them incomparable to new ones.

WHAT I DID INSTEAD: gated it behind EIV_RESOLVE_NESTED_CONTEXT=1, default off, so the running family is untouched and every recorded arm stays reproducible. Verified both directions -- default: context None, budget 256, prompt truncated by 61 tokens; flag on: context 1024, budget 456, prompt fits with 139 tokens spare. tests/test_context_resolution_guard.py has 7 tests, one of which FAILS if the flag ever defaults on.

THE RULE THIS BUYS: before landing anything that touches the training or inference path, check whether an arm is in flight. If one is, the change ships behind a flag that is off by default, and gets its own one-variable arm.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
