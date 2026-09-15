---
artifacts:
- outputs/checkpoints/v11statemask/phase_a
- logs/v11statemask_phase_a.log
- logs/v11statemask_phase_a_report.json
- logs/statemask_w30_eval.json
- logs/statemask_w30_eval.log
- scratch/run_v11statemask.sh
- scratch/score_statemask_w30.py
- tests/test_window_state_presence_mask.py
closed: true
config_note: 'phase_a_12ch_v11statemask: single variable vs phase_a_12ch_v11full (verified
  by dict diff: only mask_unlabelled_state absent->True). Same 20-ds roster, state_target
  soft, 2000 steps, seed as profile. g1/g2 already satisfied by tests/test_window_state_presence_mask.py
  (12 passed, suite green). Runner scratch/run_v11statemask.sh. Scoring: W=30/max
  pooling, nurse-free pool, per instruction NOT W=120.'
cost:
  gpu_hours: 0.12
  wall_hours: 0.25
crash_reason: ''
created_at: '2026-08-30T11:12:42+00:00'
diagnostics_measured:
- after: 0.0303
  before: 0.0336
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- 'Do NOT expect a measurable state-AUC gain from mask_unlabelled_state alone: measured
  at W=30/max, nurse-free, both instruments — ridge over_null +0.0336→+0.0303 (−0.0033,
  inside the mde 0.02), trained window head applied per patch −0.0235→−0.0219 (both
  arms indistinguishable). The hypothesis''s own prediction (+0.00..+0.03, ''the honest
  primary claim is the MECHANISM'') was correct.'
- 'Do NOT score this diagnostic with the trained WINDOW head per patch and read the
  absolute number: it is off-distribution (z ≈2.8 BELOW its null on both arms); only
  the contrast between arms is valid on that instrument.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'tests/test_window_state_presence_mask.py: 12 passed. Scored-row count
    asserted equal to labelled-window count directly on the batch; empty-labelled
    batch returns a differentiable finite zero (patch_state_loss contract). The v11statemask
    training run consumed the mask live (mask_unlabelled_state=True read by cli_train.py:262-263,
    threaded to pipeline window_state_loss at pipeline.py:2203-2207); RC=0, 0 tracebacks,
    219.66 s.'
  gate_id: g1-fabricated-neutral-no-longer-trained
  passed: true
- evidence: Unit test asserts masked == unmasked to 1e-6 on a fully-labelled batch;
    works under state_target hard AND soft; full suite green (868 passed earlier today,
    12 new tests included). mask_unlabelled_state defaults to False so every existing
    profile is byte-identical.
  gate_id: g2-no-op-where-every-window-is-labelled
  passed: true
- evidence: 'Advisory. Re-measured on the [14092] bundle (scratch/_argmax_flattening_recheck.py):
    corpora with ZERO labelled windows that previously trained 100% fabricated neutral
    now contribute nothing to ce_state: dreamt (100 win), popane_s7 (213), popane_s3
    (142), dapper (913), k_emocon (31) plus the pre-fix census set big_ideas_glycemic/optihealth_supabase/mmash/brain_wearable_monitoring.
    lifesnaps unlabelled windows (4,819 labelled of ~7,568) and pmdata''s unlabelled
    share also stop fabricating.'
  gate_id: g3-which-corpora-stop-fabricating
  passed: true
git_ref: ''
hypothesis_id: the-window-level-ce-state-term-trains-on-a35561
id: exp-v11statemask-phase-a-20260830
interpretation:
  at: '2026-08-30T11:20:16+00:00'
  failure_mode: noise
  lesson: 'Removing the fabricated-neutral 40.6% of the window ce_state gradient (mask_unlabelled_state)
    changes the momentary-state diagnostic by −0.0033 — pure noise, exactly the +0.00..+0.03
    band the hypothesis itself predicted with exp-w8 as prior (random-init matches
    trained here). The ''refutes'' verdict applies to the metric claim only: all three
    mechanism gates PASSED, the fix is correct-by-test, default-off, and should simply
    ride along in future arms rather than be tested again as a lever. Confirms the
    standing pattern: the window-level state head extracts ~nothing state-shaped from
    Phase A tokens, so cleaning its supervision cannot buy AUC — anything that wants
    state signal must go through the patch axis or a different objective, not through
    ce_state hygiene.'
  transfers_to:
  - the-window-level-ce-state-term-trains-on-a35561
key_numbers:
  head_over_null_v11full: -0.0235
  head_over_null_v11statemask: -0.0219
  n_rows: 2154.0
  phase_a_seconds: 219.66
  ridge_auc_both_arms: 0.6013
  ridge_over_null_v11full: 0.0336
  ridge_over_null_v11statemask: 0.0303
  tests_passed: 12.0
observed_effects:
- delta: -0.0033
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_momentary_state_auc_over_null: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-window-level-ce-state-term-trains-on-a35561]].

## Measurements
- [[eiv_momentary_state_auc_over_null]]: before 0.0336 → after 0.0303  (Δ -0.0033, down ✗ predicted up)

## Gates
- `g1-fabricated-neutral-no-longer-trained`: PASS — tests/test_window_state_presence_mask.py: 12 passed. Scored-row count asserted equal to labelled-window count directly on the batch; empty-labelled batch returns a differentiable finite zero (patch_state_loss contract). The v11statemask training run consumed the mask live (mask_unlabelled_state=True read by cli_train.py:262-263, threaded to pipeline window_state_loss at pipeline.py:2203-2207); RC=0, 0 tracebacks, 219.66 s.
- `g2-no-op-where-every-window-is-labelled`: PASS — Unit test asserts masked == unmasked to 1e-6 on a fully-labelled batch; works under state_target hard AND soft; full suite green (868 passed earlier today, 12 new tests included). mask_unlabelled_state defaults to False so every existing profile is byte-identical.
- `g3-which-corpora-stop-fabricating`: PASS — Advisory. Re-measured on the [14092] bundle (scratch/_argmax_flattening_recheck.py): corpora with ZERO labelled windows that previously trained 100% fabricated neutral now contribute nothing to ce_state: dreamt (100 win), popane_s7 (213), popane_s3 (142), dapper (913), k_emocon (31) plus the pre-fix census set big_ideas_glycemic/optihealth_supabase/mmash/brain_wearable_monitoring. lifesnaps unlabelled windows (4,819 labelled of ~7,568) and pmdata's unlabelled share also stop fabricating.

**Verdict:** refutes

## Interpretation
**Failure mode:** `noise`

Removing the fabricated-neutral 40.6% of the window ce_state gradient (mask_unlabelled_state) changes the momentary-state diagnostic by −0.0033 — pure noise, exactly the +0.00..+0.03 band the hypothesis itself predicted with exp-w8 as prior (random-init matches trained here). The 'refutes' verdict applies to the metric claim only: all three mechanism gates PASSED, the fix is correct-by-test, default-off, and should simply ride along in future arms rather than be tested again as a lever. Confirms the standing pattern: the window-level state head extracts ~nothing state-shaped from Phase A tokens, so cleaning its supervision cannot buy AUC — anything that wants state signal must go through the patch axis or a different objective, not through ce_state hygiene.

Transfers to: [[the-window-level-ce-state-term-trains-on-a35561]]

## Key numbers
- `head_over_null_v11full`: -0.0235
- `head_over_null_v11statemask`: -0.0219
- `n_rows`: 2154
- `phase_a_seconds`: 219.66
- `ridge_auc_both_arms`: 0.6013
- `ridge_over_null_v11full`: 0.0336
- `ridge_over_null_v11statemask`: 0.0303
- `tests_passed`: 12

## Artifacts
- `outputs/checkpoints/v11statemask/phase_a`
- `logs/v11statemask_phase_a.log`
- `logs/v11statemask_phase_a_report.json`
- `logs/statemask_w30_eval.json`
- `logs/statemask_w30_eval.log`
- `scratch/run_v11statemask.sh`
- `scratch/score_statemask_w30.py`
- `tests/test_window_state_presence_mask.py`

## Do not retry
- Do NOT expect a measurable state-AUC gain from mask_unlabelled_state alone: measured at W=30/max, nurse-free, both instruments — ridge over_null +0.0336→+0.0303 (−0.0033, inside the mde 0.02), trained window head applied per patch −0.0235→−0.0219 (both arms indistinguishable). The hypothesis's own prediction (+0.00..+0.03, 'the honest primary claim is the MECHANISM') was correct.
- Do NOT score this diagnostic with the trained WINDOW head per patch and read the absolute number: it is off-distribution (z ≈2.8 BELOW its null on both arms); only the contrast between arms is valid on that instrument.

## Notes
MECHANISM ARM, and it landed exactly as preregistered: all three gates PASS (fabricated supervision removed, asserted by test; no-op on labelled batches; corpora census reported), while the diagnostic moved −0.0033 — within noise and inside the stated +0.00..+0.03 prediction band. Consistent with exp-w8 (random-init matches trained on this diagnostic): the window ce_state gradient was not creating state signal, so removing its fabricated 40.6% changes nothing measurable. The fix's value is contractual (no more training toward labels nobody recorded), not metric. Both arms scored with identical instruments, shared permutation nulls, same 2,154 rows / same subjects.

#state-supervision #control #baseline #wearables #epoch-resolution #data-contract-violation #matched-baseline #optihealth-eiv #subject-heldout
