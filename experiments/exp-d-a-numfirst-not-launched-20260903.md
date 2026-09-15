---
artifacts: []
closed: true
config_note: 'scratch/run_d_a.sh: Phase C only off outputs/checkpoints/v41rebased/phase_b,
  target_style json_slots -> json_slots_numfirst with its own short prompt (fits both
  the 256-token train and 512-token serve budgets), scored greedy / medianK / constrained.
  The two-key config assertion passed and Phase C started at 01:17. It was STOPPED
  at 01:20, three minutes in, on its own preregistered g0.'
cost: null
crash_reason: 'STOPPED BY ITS OWN PREREGISTERED PRECONDITION, not by a crash. Gate
  g0 on this hypothesis reads: "PRECONDITION: Track A''s eiv_lm_hidden_hr_raw_ridge_mae_bpm
  on v41rebased_c <= 1.6; otherwise this arm is not launched." The driver''s automatic
  gate check ran at 01:16 and read 0.5576 from logs/a_hidden_tower_v41.json -- the
  row-target arm of the probe, which the SAME experiment had already invalidated (donor-prefix
  null 0.646 against a real-prefix 0.558: it was decoding the true heart_rate_bucket
  and hr_pct that the teacher-forced target places before hr_raw). The corrected minimal-context
  arm landed at 01:16 with 2.331, which is on the STOP side of the fork. Phase C was
  killed three minutes in and its adapter directory (outputs/adapters/v41rebased_da_c)
  is incomplete and must not be scored.'
created_at: '2026-09-02T22:25:02+00:00'
diagnostics_measured: []
do_not_retry: []
external_run: logs/d_a_phase_c.log
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss
id: exp-d-a-numfirst-not-launched-20260903
interpretation: null
key_numbers: {}
observed_effects: []
parent_experiment_id: exp-a-hr-tower-localisation-20260903
prediction_match: null
provenance: {}
recipe_fingerprint: {}
verdict: crashed
---

## Summary
Tests hypothesis [[d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss]].
Branches from [[exp-a-hr-tower-localisation-20260903]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.
External run: logs/d_a_phase_c.log

**Verdict:** crashed

**Crash:** STOPPED BY ITS OWN PREREGISTERED PRECONDITION, not by a crash. Gate g0 on this hypothesis reads: "PRECONDITION: Track A's eiv_lm_hidden_hr_raw_ridge_mae_bpm on v41rebased_c <= 1.6; otherwise this arm is not launched." The driver's automatic gate check ran at 01:16 and read 0.5576 from logs/a_hidden_tower_v41.json -- the row-target arm of the probe, which the SAME experiment had already invalidated (donor-prefix null 0.646 against a real-prefix 0.558: it was decoding the true heart_rate_bucket and hr_pct that the teacher-forced target places before hr_raw). The corrected minimal-context arm landed at 01:16 with 2.331, which is on the STOP side of the fork. Phase C was killed three minutes in and its adapter directory (outputs/adapters/v41rebased_da_c) is incomplete and must not be scored.

## Notes
NO MEASUREMENT WAS TAKEN, so this is not evidence about the target format -- the hypothesis stays retryable and its verdict is unmade.

WHY IT SHOULD NOT BE RETRIED AS WRITTEN. D-a predicted 2.843 -> <= 1.9 bpm on the split-honest 128. Track A measured the ceiling on that: a RidgeCV probe on the frozen residual stream at the emission token recovers only 2.331 bpm, so even a decoder that said perfectly what the stream knows would win about 0.5 bpm -- against a MEASURED seed floor of +/-0.416 (eiv_splithonest_heldout_hr_raw_mae_bpm, sd 0.208, n=2). The arm is not powered, and that is a fact about the interface rather than about the target format.

WHAT SURVIVES AND IS LANDED, default-off, for whoever raises the residual stream above 0.812 + the band: `target_style: json_slots_numfirst` (same thirteen slots, same @@ atoms, numbers before any enum) with prompts/json_slots_numfirst_prompt.md, which is the first Phase C prompt that fits BOTH the 256-token train budget and the 512-token serve budget -- so it also closes the train/serve skew every arm from v28 to v41 carried; and `--decode medianK|constrained` on the scorer, with the constrained allow-list fixed to admit any token containing a quote (the first version admitted a bare '"' this vocabulary never uses to close a value and trapped 126/128 rows in a digit loop).

COST AVOIDED: ~2.5 GPU-hours.

#optihealth #target-style #readout #medgemma #deployment-distribution #optihealth-eiv #matched-baseline #conditioning-collapse #subject-heldout
