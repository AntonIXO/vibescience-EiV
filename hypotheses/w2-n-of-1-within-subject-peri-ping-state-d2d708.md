---
ambition_class: null
created_at: '2026-08-20T03:36:28+00:00'
former_ids: []
gap_closure_estimate: null
gates: []
id: w2-n-of-1-within-subject-peri-ping-state-d2d708
interventions:
- analog-state-supervision-ladder
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
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
- n-of-1
- wearables
- control
- epoch-resolution
- baseline
- sensor-text
---

## Statement
W2 — N-OF-1 WITHIN-SUBJECT PERI-PING STATE (executes rb-w2-within-subject-peri-ping-state-20260815; NOT a banned retry — rule-out shortening-the-analysis-window… is explicitly scoped to CROSS-subject and reserves the within-subject case). CLAIM: in the N-of-1 regime, where the trait constant is absorbed by construction, peri-ping physiology (HR/HRV over [ping-W, ping)) carries skill over the strongest per-subject trivial — the regime the product actually serves (eiv annotate is per-user). DESIGN: per-subject models, forward-chaining expanding-window splits (never random folds), lifesnaps subjects with >=30 labelled pings + dryad subjects; pmdata excluded as a pre-commitment (retrospective morning diary, top-epoch share 0.060). Arms per W in {30,120,480,1440}: t0 running positive rate, t1 persistence, t2 clock-only, t3 HR/HRV features (e16's hr_features verbatim), t4 t3+clock. PRIMARY: per-subject AUC(t4) minus max(t0,t1,t2), aggregated as a distribution (median + bootstrap CI + sign test), never a pooled AUC. GATES: g1 blocking median skill >= +0.05 CI>0; g2 blocking fraction positive >= 0.60 sign-p < 0.05; g3 info W-profile shape committed above. A clean null here, on top of E14's daily-features positive, would localize within-subject skill to the DAILY timescale and close the sub-daily N-of-1 question.

## Rationale


## Plan


## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Applies [[analog-state-supervision-ladder]].
Predicts [[eiv_affect_multichannel_increment]] (up).

#state-supervision #n-of-1 #wearables #control #epoch-resolution #baseline #sensor-text #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
