---
ambition_class: null
created_at: '2026-08-19T21:19:25+00:00'
former_ids: []
gap_closure_estimate: null
gates: []
id: w-sweep-rb-window-size-x-pooling-state-sweep-73469a
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
status: confirmed
supersedes: null
topic_tags:
- state-supervision
- pooling
- epoch-resolution
- control
- wearables
- baseline
- sensor-text
---

## Statement
W-SWEEP (rb-window-size-x-pooling-state-sweep-20260819, executes exp-048's residual). The trained per-patch state heads carry a real momentary-state effect that exp-048 could not resolve because (a) single seeds are under-powered and (b) W=120 — the only eval window ever used — is nearly the worst informative window. Claims: (1) the pooled 5-seed estimator (common 200-perm set, mean-of-seeds vs null-of-means) clears p<0.01 at some (W, pooling) cell; (2) over_null falls monotonically with W and inverts by W=240 (the trained head reads recent minutes, not the day); (3) max-pooling over covering patches >= mean (excursion-lever transfer); (4) PL60 supervision > PL120 at matched W. HONESTY BOUND (g4): raw_shallow re-priced at every W on the same rows — any encoder claim is relative to its own W's handcrafted bound, which no learned arm is predicted to reach.

## Rationale


## Plan


## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Applies [[analog-state-supervision-ladder]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).

#state-supervision #pooling #epoch-resolution #control #wearables #baseline #sensor-text #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
