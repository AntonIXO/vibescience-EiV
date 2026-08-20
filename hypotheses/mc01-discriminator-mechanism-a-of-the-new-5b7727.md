---
ambition_class: null
created_at: '2026-08-20T00:35:17+00:00'
former_ids: []
gap_closure_estimate: null
gates: []
id: mc01-discriminator-mechanism-a-of-the-new-5b7727
interventions:
- analog-state-supervision-ladder
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: ''
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags: []
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- deployment-distribution
- collapse
- denominator
- sensor-text
- epoch-resolution
- state-supervision
- wearables
---

## Statement
MC01 DISCRIMINATOR. Mechanism (a) of the new-corpus-encoder problem: the Phase-A InfoNCE pool's 974 sparse windows (admitted by min_coverage 0.0, absent from every old-corpus encoder's training) degrade the soft-token prefix for deployment emission. TEST: one chain byte-identical to ab_ctrl except Phase A min_coverage 0.1 (a_mc01 → b_mc01 → c_mc01_s42, band04 law, seed 42), audited on the same supabase-200/dense-128 draws. DECISION RULE: mc01 MAE <= 4.5 ⇒ mechanism (a) isolated (the floor change restores the old pool law); mc01 >= 5.5 ⇒ (a) rejected, proceed to caption/cache byte-diff (mechanism b/c); between ⇒ inconclusive, report and proceed to b/c anyway. Note ab_ctrl (6.03) is the control at 0.0 — same profiles, same session, same code — so this is a clean paired comparison with one variable. Interpretability caveat: single seed; the C family band is 0.60 bpm, and the effect under test is ~3 bpm — 5x the band, resolvable at one seed.

## Rationale


## Plan


## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Applies [[analog-state-supervision-ladder]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).

#deployment-distribution #collapse #denominator #sensor-text #epoch-resolution #state-supervision #wearables
