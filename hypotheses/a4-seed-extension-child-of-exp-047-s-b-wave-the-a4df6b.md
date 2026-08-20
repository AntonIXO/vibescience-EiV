---
ambition_class: null
created_at: '2026-08-19T17:39:31+00:00'
former_ids: []
gap_closure_estimate: null
gates: []
id: a4-seed-extension-child-of-exp-047-s-b-wave-the-a4df6b
interventions:
- analog-state-supervision-ladder
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: ''
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
- subject-heldout
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- state-supervision
- wearables
- control
- epoch-resolution
- baseline
- sensor-text
---

## Statement
A4 SEED EXTENSION (child of exp-047's B wave). The PL60/ST30 1-hour-patch A→B chain's trained patch head carries real momentary state, and seed s43's clearing (A +0.0163 z+2.80, B +0.0189 z+3.23, clean pool, 200-perm within-window null, zero fitted parameters) was signal, not the seed lottery. TEST: 3 further full A→B chains at seeds 44/45/46, identical recipe (phase_a_12ch_analog_patch60 → b_patch60, state_weight 0.5, state_patch_weight 0.5, batch 8 at B), scored with the identical trained-head instrument (scratch/b_wave_eval.py, fixed scores + label permutation). PREREGISTERED DECISION RULE on the 5-seed PL60 B-stage set {42,43,44,45,46}: CONFIRM the lever if >=3 of 5 seeds have over_null > 0 AND >=2 of 5 have z >= +2.0 AND the pooled per-seed mean over_null >= +0.008; REFUTE (seed lottery) if <=1 seed reaches z >= +2.0; otherwise INCONCLUSIVE — report the seed band and stop buying PL60 arms until the instrument tightens or the corpus grows (K-EmoPhone/StressID). Secondary, non-blocking: A-vs-B paired comparison per seed — if B degrades the A-stage readout on >=4 of 5 seeds, the B adoption is doing harm and the state supervision should stay A-only.

## Rationale


## Plan


## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Applies [[analog-state-supervision-ladder]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).

#state-supervision #wearables #control #epoch-resolution #baseline #sensor-text #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
