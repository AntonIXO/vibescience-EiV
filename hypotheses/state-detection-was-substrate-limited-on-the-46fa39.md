---
ambition_class: mechanism
created_at: '2026-08-31T06:41:03+00:00'
former_ids: []
gap_closure_estimate: 0.5
gates:
- blocking: true
  description: 'g0 BLOCKING (auto): v30state phase_a eff_rank >= 7.0 (geometry gate
    in the runner, abort before state/B/C on failure).'
  diagnostic_id: eiv_effective_rank
  id: g0-geometry-holds
  mde: 1.0
- blocking: true
  description: 'g1 BLOCKING (state): ridge W30/max over_null > 0.05 on the momentary-state
    eval (nurse-free, within-window permutation null) -- vs the historical ceiling
    +0.034-0.037 measured ONLY on rank-collapsed encoders (analog family ranks 2.2-5.7).
    PASS -> state detection was substrate-limited, not information-limited; the healthy
    encoder unlocks it. FAIL -> state signal is genuinely weak in wrist channels at
    W=30 even on healthy geometry -- closes the substrate question honestly.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g1-state-beats-historical-ceiling
  mde: 0.02
- blocking: false
  description: 'g2 advisory (deployment no-harm): supabase MAE within v28''s band
    (2.23-3.83) and corr >= 0.60 -- state supervision must not cost the HR axis.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-hr-no-harm
  mde: 0.8
id: state-detection-was-substrate-limited-on-the-46fa39
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: 'If rank collapse was masking state signal (days indistinguishable
    => within-window transitions doubly so), the healthy substrate + patch-state supervision
    should clear +0.05. Against: exp-w8''s random-init parity suggests the tokens
    never carried state regardless. Prior ~40%. Calibration on this diagnostic 3/9.
    Gates decide.'
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
- sensor-text
- contrastive
- deployment-distribution
- wearables
- epoch-resolution
- baseline
- control
---

## Statement
STATE DETECTION WAS SUBSTRATE-LIMITED: on the geometry-healthy strat-A, adding state_patch_weight 0.1 + affect 0.1 (v30state chain) lifts momentary-state ridge over_null beyond the +0.05 bar (historical ceiling +0.037, all on rank-collapsed encoders) while keeping deployment in v28's band.

## Rationale
STATE TRACE SUMMARY (vault): momentary state within-window signal is real (E26: flow-vs-stress AUC 0.633 vs clock 0.513) but encoder tokens never beat shallow features on transitions (e27/e29 refuted); trained-vs-random-init parity (w8); wsweep ceiling +0.037 z 5.4; statemask hygiene changed nothing. ALL of it measured on encoders now known to be rank-collapsed. The healed substrate is a new regime; state_patch_weight (soft distributions over 23 patches, CORN-compatible) is the supervision built for exactly this and never yet run on a healthy A. One chain answers both the state question and the no-harm question.

## Plan
Queued behind v29. A = v20strat + affect 0.1 + state_patch_weight 0.1 (the 20,310-cell patch-level soft state, the supervision W8/E29 wanted) + statemask -> geometry gate -> STATE scoring (scratch/score_state_v30.py: ridge W30/max on v30-A vs v28-A as the no-state comparator, same rows/nulls) -> B@30 -> band04 C -> deployment scoring. THE INSIGHT FROM THE FULL STATE TRACE: every historical state-detection null (w8 random-init==trained; wsweep ceiling +0.037; e18/e26/e29 nulls; statemask noise) was measured on encoders the geometry sweep now shows were rank-collapsed (analog family 2.2-5.7, patch60 family 2.3-5.7) -- the state question has NEVER been asked of a healthy encoder. v30 asks it.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Gate `g0-geometry-holds` (blocking): g0 BLOCKING (auto): v30state phase_a eff_rank >= 7.0 (geometry gate in the runner, abort before state/B/C on failure).
Gate `g1-state-beats-historical-ceiling` (blocking): g1 BLOCKING (state): ridge W30/max over_null > 0.05 on the momentary-state eval (nurse-free, within-window permutation null) -- vs the historical ceiling +0.034-0.037 measured ONLY on rank-collapsed encoders (analog family ranks 2.2-5.7). PASS -> state detection was substrate-limited, not information-limited; the healthy encoder unlocks it. FAIL -> state signal is genuinely weak in wrist channels at W=30 even on healthy geometry -- closes the substrate question honestly.
Gate `g2-hr-no-harm` (advisory): g2 advisory (deployment no-harm): supabase MAE within v28's band (2.23-3.83) and corr >= 0.60 -- state supervision must not cost the HR axis.

#sensor-text #contrastive #deployment-distribution #wearables #epoch-resolution #baseline #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
