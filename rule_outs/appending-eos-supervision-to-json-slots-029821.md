---
created_at: '2026-08-13T20:12:43+00:00'
former_ids:
- appending-eos-supervision-to-json-slots-targets-e2-rb-eos-stop-20260813-is-a-nec
id: appending-eos-supervision-to-json-slots-029821
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-13T20:12:43+00:00'
topic_tags:
- deployment-distribution
- optihealth
---

## Claim
Appending EOS supervision to json_slots targets (E2, rb-eos-stop-20260813) is a necessary retrain to fix the decode repeat-loop/parse-failure defect

## Method
CPU premise probe scratch/e2_premise_check.py: (a) static read of the training collate path, (b) tokenizer-level replica of suffix construction for a real json_slots target (google/medgemma-1.5-4b-it), (c) stop-behavior scan of all 984 real v9band04 capture emissions (token-length vs budget, repeated-key detection, json_ok).

## Evidence
prepare_training_inputs already appends eos_token_id=1 (<eos>) to EVERY target suffix with loss_mask=True (gemma_runtime.py:610-614); collate replica: last supervised label == eos for a real 13-slot json_slots target — the 'targets end at } with no EOS supervision' premise is FALSE, and was already false for the loop-affected arms (v6s44's 5.5% parse failures happened WITH EOS supervised, so missing-EOS was never the loop mechanism). In the v9band04 recipe the defect is absent outright: 984/984 emissions json_ok across 3 seeds × (supabase-200 + dense-128); re-tokenized emission length max 96 vs 128 budget (dense median ~66), 0 rows >=120 tokens, 0 repeated-key loops — the model stops by emitting EOS. Historical repeat loops are accounted for by the OOD explanation-prompt default (fixed 6cb9b97). Annotate truncation guard stays as belt-and-braces. No retrain justified.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].

#deployment-distribution #optihealth
