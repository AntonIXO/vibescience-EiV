---
created_at: '2026-09-01T10:27:38+00:00'
former_ids: []
id: state-target-soft-is-the-untried-fix-for-neutral-collapse
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:27:38+00:00'
topic_tags: []
---

## Claim
state_target: soft is the untried fix for the affect corpora's neutral collapse -- cross-entropy against the empirical per-epoch distribution would recover the minority states the majority reduction discards, and it is a profile key rather than new code.

## Method
Grepped state_target through src/ and configs/, found the three analog profiles and their run metrics, then read the closed experiment exp-047 and its do_not_retry list before queueing anything.

## Evidence
IT WAS TRIED IN AUGUST, THE LADDER RAN AT TWO SEEDS, AND ITS RESULT IS ALREADY A DO-NOT-RETRY. I proposed this one step ago as though it were new. Checking before queueing found the whole thing on disk and in the vault.

THE PROFILES EXIST AND WERE RUN: phase_a_12ch_analog_{hard,soft,ordinal}, each at s42 and s43, 80 steps, metrics in logs/phase_a_12ch_analog_*_metrics.jsonl. The key is wired end to end -- cli_train.py reads state_target at lines 259 (A), 501 (B) and 790 (LoRA), passing it into all three phases; state_distribution() is datasets.py:198. Not a no-op.

EXPERIMENT exp-047-analog-state-supervision-a1 CLOSED 2026-08-19, verdict `supports`, and its own interpretation says READ THE VERDICT AS DIRECTION-ONLY -- 'the committed magnitude failed'. Its failure mode is recorded as `test_invalid`.

AND ITS SECOND do_not_retry IS EXACTLY THE INFERENCE I WAS ABOUT TO MAKE:
  'Do NOT attribute clean-pool enc_token over_null gains (+0.02..+0.04 RF band) to the analog state objectives: the HARD control (s43 z+2.39) and the OLD-CORPUS v5d incumbent (z+1.84, trained before the analog ladder existed and before the corpus grew) sit in the same band.'
So soft/ordinal do not separate from the HARD control they were built to beat. The band is encoder-generic.

THE FIRST do_not_retry NAMES A POOL DEFECT that would have contaminated any rerun: 43% of the full transition pool is nurse_stress shift-schedule labels, and even zero-parameter raw arms lose significance there (raw_shallow z +7.14 clean -> +1.73 full). Any state-axis evaluation must exclude nurse_stress or reweight the null.

WHAT SURVIVES OF MY MEASUREMENT: the arithmetic is still right and still useful -- ten of twelve datasets carry two states inside one 120-minute patch, and the CASE window really is {neutral 33, flow 3, stress 5} reduced to `neutral`. What is wrong is the CONCLUSION that a distributional target is therefore untried and promising. It was tried, at two seeds, and did not separate from its own hard control.

SEPARATELY, AND STILL LIVE: hypothesis the-window-level-ce-state-term-trains-on-a35561 is `refuted` but describes a real defect measured on the v11 roster -- 3,851 of 9,479 windows (40.6%) map to a `neutral` one-hot with no presence mask at pipeline.py:2108. CLAUDE.md records mask_unlabelled_state now defaults true, so that is fixed; the refutation is of the FIX's predicted benefit, not of the defect.

METHOD NOTE: this is the second time today I have proposed something the record already settled (the first was early-vs-late presence, which turned out to have three seeds per side on disk). Both were caught by looking before queueing, and both cost about four minutes. The pattern is that a mechanism I derive from first principles has usually already occurred to someone with the same data.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
