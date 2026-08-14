---
created_at: '2026-08-13T08:02:40+00:00'
former_ids: []
gates:
- g1-parse-failure-rate-0-at-max-new-tokens-96
- g2-truncation-guard-stays-in-product
- g3-no-supabase-mae-regression-beyond-band
hypothesis_id: null
id: rb-eos-stop-20260813
notes: []
priority: 3
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
status: abandoned
topic_tags: []
updated_at: '2026-08-13T20:12:43+00:00'
---

## Prompt
ABANDONED 2026-08-13: premise refuted by CPU probe scratch/e2_premise_check.py before any GPU was spent. EOS is ALREADY supervised on every json_slots target (gemma_runtime.py:610-614, loss_mask=True on the appended eos), and the v9band04 recipe shows 984/984 json_ok / 0 loops / max 96 of 128 tokens — the model stops by emitting EOS. Missing-EOS was never the loop mechanism (v6s44 looped WITH EOS supervised); the real historical causes were the OOD explanation-prompt default (fixed 6cb9b97) and old-recipe arms. Rule-out filed on the problem. Original prompt: E2 — EOS/stop supervision as rider on next retrain.

## Preconditions
Runs as a rider on the NEXT scheduled retrain (E1 or the seed-band arms if convenient as a separate single-variable arm) — not worth solo GPU. Truncation guard 6cb9b97 already protects the product meanwhile.

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Intended gates: `g1-parse-failure-rate-0-at-max-new-tokens-96`, `g2-truncation-guard-stays-in-product`, `g3-no-supabase-mae-regression-beyond-band`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
