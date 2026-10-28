---
artifacts:
- logs/v41rebased_phase_a.log
- logs/v41rebased_phase_b.log
- logs/v41rebased_phase_c.log
- logs/v41rebased_phase_c_report.json
- logs/v41rebased_geometry.json
- logs/emission_v41rebased_heldout.json
- logs/emission_v41rebased_heldout_summary.json
- logs/g3_v41rebased_vs_incumbent.json
- logs/g3_random_controls.json
- logs/wsweep_raw_shallow.json
- outputs/checkpoints/v41rebased/phase_a
- outputs/checkpoints/v41rebased/phase_b
- outputs/adapters/v41rebased_c
- scratch/run_v41_rebased.sh
- scratch/g3_state_readout.py
closed: true
config_note: 'Full A->B->C at PL60/ST30 with sensor_micro_batches: 4 on A and B and
  `split: train` on ALL THREE stages, from the temp profile config (configs/training.yaml
  untouched as a recipe). Phase A carries state_weight 0.5, state_patch_weight 0.5,
  state_target soft, hr_coverage_denominator recording, 19-corpus roster; Phase B
  same geometry at batch 30; Phase C = band04''s 6-corpus filter, freeze_encoder,
  2600 steps, seed 42. Scored `--split heldout` on the 1,485-row pool (128-row draw),
  paired row-for-row against arm A0 of exp-t1. g3 read on the clean transition pool
  (flow vs stress, W=30 max, nurse_stress excluded, n=2136 / 113 subjects) with TWO
  readout families AND the random-init twin control that the registered diagnostic
  and references/null-and-control-design.md both require.'
cost:
  gpu_hours: 2.0
  wall_hours: 2.0
crash_reason: ''
created_at: '2026-09-02T18:53:17+00:00'
diagnostics_measured:
- after: 9.2609
  before: 9.0881
  diagnostic_id: eiv_effective_rank
- after: 2.8431
  before: 4.4292
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
- after: 0.9924
  before: 0.9939
  diagnostic_id: eiv_gen_between_sample_std
- after: 0.0603
  before: 0.0127
  diagnostic_id: eiv_momentary_state_auc_over_null
- after: 0.007
  before: -0.0303
  diagnostic_id: eiv_state_auc_trained_minus_random
do_not_retry:
- Do NOT quote g3's +0.0603 as evidence that training taught the encoder momentary
  state. Its own random-init twin reads +0.0533 on identical rows, folds and permutation
  seeds; the training contribution is +0.0070, inside the k=2 band. The number is
  real, the attribution is not.
- Do NOT re-run a momentary-state readout on ANY encoder without its random-init twin
  in the same invocation. scratch/g3_state_readout.py takes '@random' as a tag suffix
  and costs about a minute per arm. Six of six cells measured to date (three targets
  in references/null-and-control-design.md plus this arm's two readouts and the incumbent's
  two) have random-init >= trained or inside the band; a bare permutation z on this
  family has no track record of surviving the control.
- Do NOT attribute v41's 1.586 bpm hr_raw win to PL60 geometry, or to state supervision,
  or to split-honesty, or to the 19-corpus roster. All four changed at once against
  arm A0. Any single-lever claim needs a one-variable arm that was not run.
- Do NOT re-base a future gate on 2.843 as if it were a family mean. It is ONE seed.
  The split-honest repeat-run sigma still does not exist, and per-family sd varies
  58x across recipes in this project; g1's 0.7 mde remains provisional and inherited.
external_run: 'local: logs/v41rebased_phase_{a,b,c}.log, logs/emission_v41rebased_heldout.json,
  logs/g3_v41rebased_vs_incumbent.json, logs/g3_random_controls.json'
former_ids: []
gate_results:
- evidence: 'eff_rank 9.2609 on the v41rebased Phase A checkpoint vs the 7.0 veto
    (logs/v41rebased_geometry.json). No collapse: between_sample_var_share 0.652,
    cos_p50 0.089, slot_mean_spread 0.0094. Read before Phase B as the hypothesis
    prescribes, so the chain cost was never at risk. As the gate''s own text says,
    a PASS here says nothing about quality.'
  gate_id: g0-geometry-veto
  passed: true
- evidence: Emitted/true sd ratio 0.9924 on the held-out draw's hr_raw slot, well
    inside [0.85, 1.35]; the comparator A0 reads 0.9939. Read BEFORE g1, as prescribed.
    This is the gate that killed v30state (2.77) and v36labels (1.59/2.08) while their
    in-sample numbers looked healthy, so v41's MAE is comparable to A0's rather than
    a scale artefact. hr_peak scale 0.748 vs A0's 0.787 -- both under-express the
    peak range, which bounds how far the hr_peak improvement can be pushed.
  gate_id: g2-calibration-in-band
  passed: true
- evidence: 'PASSES, and not marginally: v41rebased 2.8431 bpm vs arm A0''s 4.4292
    on the identical 128-row draw from the 1,485-row held-out pool, with all 128 row
    indices shared and the truth values verified equal row-for-row. The gate is NO-HARM
    with mde 0.7, so anything up to 5.129 would have passed; v41 is 1.586 bpm BETTER,
    bootstrap CI95 [+0.992, +2.173] over 10,000 resamples, better on 74.2% of rows
    -- the CI excludes zero, so the direction is not a draw. Skill over the always-predict-median
    constant (6.876 on these rows) goes +2.447 -> +4.033 bpm; corr 0.820 -> 0.924.
    hr_peak moves the same way, 18.360 -> 14.288 MAE (paired +4.071, CI95 [+1.805,
    +6.287]), taking that slot from WORSE than the median constant (-0.381 skill)
    to +3.691. NOT ATTRIBUTABLE TO ONE LEVER: v41 differs from A0 in four things at
    once -- patch geometry (PL60/ST30, 47 patches vs PL120/ST60, 23), state supervision
    on Phase A, split-honesty of Phase A/B (A0''s tower is the incumbent''s, trained
    with split=None), and the Phase A/B roster (19 corpora vs the incumbent''s). The
    direction of the split confound favours the conclusion rather than threatening
    it: v41 is the MORE constrained arm on that axis and still wins.'
  gate_id: g1-no-harm-vs-the-split-honest-comparator
  passed: true
- evidence: 'PASSES ON THE PREREGISTERED CRITERION, and the criterion is narrower
    than it looks. Gate: momentary state over_null > +0.02 on the clean pool (flow
    vs stress, W=30 max, nurse_stress excluded, n=2136 / 666 windows / 113 subjects).
    Measured on the gate readout (ridge on max-pooled covering-patch fused features,
    GroupKFold OOF, 100 within-window perms): v41rebased +0.0603 (AUC 0.6327, z +4.37,
    p 0.0000) against the PL120 incumbent''s +0.0127 (AUC 0.5777, z +0.98, p 0.20).
    Above E29''s zero-parameter grid floor (+0.0309) and 3x the threshold, so the
    arm has NOT destroyed information -- which is the specific failure the gate was
    written to catch. SECOND READOUT FAMILY, required by the diagnostic''s 2026-08-18
    amendment: the arms'' own trained window heads read -0.0067 (v41rebased) and -0.0265
    (v28affect01), i.e. both AT OR BELOW their own permutation null. Same direction,
    opposite level. THE CONTROL THE GATE TEXT DOES NOT NAME AND references/null-and-control-design.md
    rule 1 demands: the random-init twin of each arm, identical geometry and seeded
    weights, identical rows, folds and permutation seeds (logs/g3_random_controls.json).
    v41rebased@random reads ridge +0.0533 (AUC 0.6240, z +4.16, p 0.0000) and v28affect01@random
    +0.0430 (z +3.59). So trained-minus-random is +0.0070 for v41 and -0.0303 for
    the incumbent, and +0.0070 sits INSIDE the k=2 band of +/-0.0141 on the newly
    registered eiv_state_auc_trained_minus_random. READ THE GATE LITERALLY AND IT
    IS RIGHT: the hypothesis claims the signal SURVIVES training, and it does -- v41
    preserves what the incumbent destroyed. It does NOT claim training creates the
    signal, and this experiment measures that it does not. The handcrafted 15-feature
    raw_shallow RF bound on the same W=30 cell (+0.0666, z +5.02) still exceeds every
    learned and every random encoder readout in the table.'
  gate_id: g3-does-momentary-state-survive-training
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: v41-pl60-state-chain-rebased-and-split-honest
id: exp-v41rebased-pl60-splithonest-20260902
interpretation:
  at: '2026-09-18T11:14:23+00:00'
  failure_mode: null
  lesson: 'PL60/ST30 buys HR outright and buys state in a way its own gate could not
    see — and both halves needed the random-init twin plus a second estimator family
    to read correctly. HR: the trained PL60 tower beats its random twin by 1.275 bpm
    hr_raw and 5.746 bpm hr_peak (paired, CI95 excludes zero) and the trained PL120
    tower by 0.235 / 3.299, yet at RANDOM init PL60 is WORSE than PL120 on hr_raw
    (2.086 vs 1.687) — so PL60''s HR advantage is entirely LEARNED. State, and this
    CORRECTS the first reading of this experiment: g3''s gate cell (single-span ridge)
    put trained-minus-random at +0.0070, inside the band, which reads as "training
    added nothing". Under a gradient-boosting readout on the same rows the same contrast
    is +0.0349, and under a {30,120,240} multi-span ridge +0.0367 — both far outside
    it. The twin FALLS (+0.0533 to +0.0405) while the trained encoder RISES (+0.0603
    to +0.0852) as the readout widens, which is the signature of non-linear span-sensitive
    structure a single-span ridge cannot express. The honest verdict is therefore:
    Phase A/B does put momentary-state information into the tokens, the gate readout
    saw about a third of it, and most of what it learned is redundant with 15 handcrafted
    HR/HRV features (union trained-minus-random +0.0116 / +0.0145). Operationally:
    never gate a representation claim on one estimator family, and never omit the
    random twin — `scratch/g3_state_readout.py --chain ''tag@random=<phase_a>''` costs
    a minute and `scratch/g5_pool_ceiling.py` prices the whole ladder in CPU minutes.


    PROVENANCE CORRECTION 2026-09-18: the documented scratch/run_v41_rebased.sh writes
    split=train to YAML, but src/cli_train.py at saved commit ad78bc2082d0 forwards
    split only for C; Phase A allowed_subject_keys was not forwarded (saved allowed_subject_count=null)
    and B had no allowlist. This static evidence blocks the WHOLE-PIPELINE subject-held-out
    claim for the documented path. Stored captures/numerical comparisons are preserved;
    no historical rerun or unknown launch path is asserted. A new campaign will rebuild
    a verified train-only common upstream tower. Numerical readout-error differences
    between fitted heads are not an information-theoretic decomposition.'
  transfers_to:
  - the-llm-is-a-formatter-a-small-attention-head-matches-it-on-hr
  - a-held-out-scored-chain-combining-every-079038
  - eda-carries-state-information-beyond-corpus-identity
  - e29-state-geometry-innocent-weights-guilty-20260815
key_numbers:
  comparator_A0_hr_peak_mae_bpm: 18.3595
  comparator_A0_hr_raw_mae_bpm: 4.4292
  eff_rank: 9.2609
  frac_rows_v41_better: 0.742
  heldout_hr_peak_mae_bpm: 14.2881
  heldout_hr_raw_mae_bpm: 2.8431
  incumbent_state_over_null_ridge: 0.0127
  incumbent_state_trained_minus_random_ridge: -0.0303
  n_paired_emission_rows: 128.0
  n_state_rows: 2136.0
  paired_ci95_hi: 2.1732
  paired_ci95_lo: 0.9917
  paired_delta_hr_peak_bpm: 4.0714
  paired_delta_hr_raw_bpm: 1.5861
  phase_c_final_loss: 1.49
  raw_shallow_bound_over_null: 0.0666
  scale_ratio_hr_raw: 0.9924
  state_over_null_head: -0.0067
  state_over_null_head_random_twin: -0.0011
  state_over_null_ridge: 0.0603
  state_over_null_ridge_random_twin: 0.0533
  state_trained_minus_random_ridge: 0.007
  trivial_hr_raw_mae_bpm: 6.8762
observed_effects:
- delta: 0.1728
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: -1.5861
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: down
- delta: -0.0015
  diagnostic_id: eiv_gen_between_sample_std
  direction: down
- delta: 0.0476
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
- delta: 0.0373
  diagnostic_id: eiv_state_auc_trained_minus_random
  direction: up
parent_experiment_id: exp-t1-a0-matched-llm-paired-20260902
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_between_sample_std: false
    eiv_momentary_state_auc_over_null: true
    eiv_splithonest_heldout_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: supports
---

## Summary
Tests hypothesis [[v41-pl60-state-chain-rebased-and-split-honest]].
Branches from [[exp-t1-a0-matched-llm-paired-20260902]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.
External run: local: logs/v41rebased_phase_{a,b,c}.log, logs/emission_v41rebased_heldout.json, logs/g3_v41rebased_vs_incumbent.json, logs/g3_random_controls.json

## Measurements
- [[eiv_effective_rank]]: before 9.0881 → after 9.2609  (Δ +0.1728, up)
- [[eiv_splithonest_heldout_hr_raw_mae_bpm]]: before 4.4292 → after 2.8431  (Δ -1.5861, down ✗ predicted none)
- [[eiv_gen_between_sample_std]]: before 0.9939 → after 0.9924  (Δ -0.0015, down ✗ predicted none)
- [[eiv_momentary_state_auc_over_null]]: before 0.0127 → after 0.0603  (Δ +0.0476, up ✓ predicted up)
- [[eiv_state_auc_trained_minus_random]]: before -0.0303 → after 0.007  (Δ +0.0373, up)

## Gates
- `g0-geometry-veto`: PASS — eff_rank 9.2609 on the v41rebased Phase A checkpoint vs the 7.0 veto (logs/v41rebased_geometry.json). No collapse: between_sample_var_share 0.652, cos_p50 0.089, slot_mean_spread 0.0094. Read before Phase B as the hypothesis prescribes, so the chain cost was never at risk. As the gate's own text says, a PASS here says nothing about quality.
- `g2-calibration-in-band`: PASS — Emitted/true sd ratio 0.9924 on the held-out draw's hr_raw slot, well inside [0.85, 1.35]; the comparator A0 reads 0.9939. Read BEFORE g1, as prescribed. This is the gate that killed v30state (2.77) and v36labels (1.59/2.08) while their in-sample numbers looked healthy, so v41's MAE is comparable to A0's rather than a scale artefact. hr_peak scale 0.748 vs A0's 0.787 -- both under-express the peak range, which bounds how far the hr_peak improvement can be pushed.
- `g1-no-harm-vs-the-split-honest-comparator`: PASS — PASSES, and not marginally: v41rebased 2.8431 bpm vs arm A0's 4.4292 on the identical 128-row draw from the 1,485-row held-out pool, with all 128 row indices shared and the truth values verified equal row-for-row. The gate is NO-HARM with mde 0.7, so anything up to 5.129 would have passed; v41 is 1.586 bpm BETTER, bootstrap CI95 [+0.992, +2.173] over 10,000 resamples, better on 74.2% of rows -- the CI excludes zero, so the direction is not a draw. Skill over the always-predict-median constant (6.876 on these rows) goes +2.447 -> +4.033 bpm; corr 0.820 -> 0.924. hr_peak moves the same way, 18.360 -> 14.288 MAE (paired +4.071, CI95 [+1.805, +6.287]), taking that slot from WORSE than the median constant (-0.381 skill) to +3.691. NOT ATTRIBUTABLE TO ONE LEVER: v41 differs from A0 in four things at once -- patch geometry (PL60/ST30, 47 patches vs PL120/ST60, 23), state supervision on Phase A, split-honesty of Phase A/B (A0's tower is the incumbent's, trained with split=None), and the Phase A/B roster (19 corpora vs the incumbent's). The direction of the split confound favours the conclusion rather than threatening it: v41 is the MORE constrained arm on that axis and still wins.
- `g3-does-momentary-state-survive-training`: PASS — PASSES ON THE PREREGISTERED CRITERION, and the criterion is narrower than it looks. Gate: momentary state over_null > +0.02 on the clean pool (flow vs stress, W=30 max, nurse_stress excluded, n=2136 / 666 windows / 113 subjects). Measured on the gate readout (ridge on max-pooled covering-patch fused features, GroupKFold OOF, 100 within-window perms): v41rebased +0.0603 (AUC 0.6327, z +4.37, p 0.0000) against the PL120 incumbent's +0.0127 (AUC 0.5777, z +0.98, p 0.20). Above E29's zero-parameter grid floor (+0.0309) and 3x the threshold, so the arm has NOT destroyed information -- which is the specific failure the gate was written to catch. SECOND READOUT FAMILY, required by the diagnostic's 2026-08-18 amendment: the arms' own trained window heads read -0.0067 (v41rebased) and -0.0265 (v28affect01), i.e. both AT OR BELOW their own permutation null. Same direction, opposite level. THE CONTROL THE GATE TEXT DOES NOT NAME AND references/null-and-control-design.md rule 1 demands: the random-init twin of each arm, identical geometry and seeded weights, identical rows, folds and permutation seeds (logs/g3_random_controls.json). v41rebased@random reads ridge +0.0533 (AUC 0.6240, z +4.16, p 0.0000) and v28affect01@random +0.0430 (z +3.59). So trained-minus-random is +0.0070 for v41 and -0.0303 for the incumbent, and +0.0070 sits INSIDE the k=2 band of +/-0.0141 on the newly registered eiv_state_auc_trained_minus_random. READ THE GATE LITERALLY AND IT IS RIGHT: the hypothesis claims the signal SURVIVES training, and it does -- v41 preserves what the incumbent destroyed. It does NOT claim training creates the signal, and this experiment measures that it does not. The handcrafted 15-feature raw_shallow RF bound on the same W=30 cell (+0.0666, z +5.02) still exceeds every learned and every random encoder readout in the table.

**Verdict:** supports

## Interpretation
PL60/ST30 buys HR outright and buys state in a way its own gate could not see — and both halves needed the random-init twin plus a second estimator family to read correctly. HR: the trained PL60 tower beats its random twin by 1.275 bpm hr_raw and 5.746 bpm hr_peak (paired, CI95 excludes zero) and the trained PL120 tower by 0.235 / 3.299, yet at RANDOM init PL60 is WORSE than PL120 on hr_raw (2.086 vs 1.687) — so PL60's HR advantage is entirely LEARNED. State, and this CORRECTS the first reading of this experiment: g3's gate cell (single-span ridge) put trained-minus-random at +0.0070, inside the band, which reads as "training added nothing". Under a gradient-boosting readout on the same rows the same contrast is +0.0349, and under a {30,120,240} multi-span ridge +0.0367 — both far outside it. The twin FALLS (+0.0533 to +0.0405) while the trained encoder RISES (+0.0603 to +0.0852) as the readout widens, which is the signature of non-linear span-sensitive structure a single-span ridge cannot express. The honest verdict is therefore: Phase A/B does put momentary-state information into the tokens, the gate readout saw about a third of it, and most of what it learned is redundant with 15 handcrafted HR/HRV features (union trained-minus-random +0.0116 / +0.0145). Operationally: never gate a representation claim on one estimator family, and never omit the random twin — `scratch/g3_state_readout.py --chain 'tag@random=<phase_a>'` costs a minute and `scratch/g5_pool_ceiling.py` prices the whole ladder in CPU minutes.

PROVENANCE CORRECTION 2026-09-18: the documented scratch/run_v41_rebased.sh writes split=train to YAML, but src/cli_train.py at saved commit ad78bc2082d0 forwards split only for C; Phase A allowed_subject_keys was not forwarded (saved allowed_subject_count=null) and B had no allowlist. This static evidence blocks the WHOLE-PIPELINE subject-held-out claim for the documented path. Stored captures/numerical comparisons are preserved; no historical rerun or unknown launch path is asserted. A new campaign will rebuild a verified train-only common upstream tower. Numerical readout-error differences between fitted heads are not an information-theoretic decomposition.

Transfers to: [[the-llm-is-a-formatter-a-small-attention-head-matches-it-on-hr]], [[a-held-out-scored-chain-combining-every-079038]], [[eda-carries-state-information-beyond-corpus-identity]], [[e29-state-geometry-innocent-weights-guilty-20260815]]

## Key numbers
- `comparator_A0_hr_peak_mae_bpm`: 18.3595
- `comparator_A0_hr_raw_mae_bpm`: 4.4292
- `eff_rank`: 9.2609
- `frac_rows_v41_better`: 0.742
- `heldout_hr_peak_mae_bpm`: 14.2881
- `heldout_hr_raw_mae_bpm`: 2.8431
- `incumbent_state_over_null_ridge`: 0.0127
- `incumbent_state_trained_minus_random_ridge`: -0.0303
- `n_paired_emission_rows`: 128
- `n_state_rows`: 2136
- `paired_ci95_hi`: 2.1732
- `paired_ci95_lo`: 0.9917
- `paired_delta_hr_peak_bpm`: 4.0714
- `paired_delta_hr_raw_bpm`: 1.5861
- `phase_c_final_loss`: 1.49
- `raw_shallow_bound_over_null`: 0.0666
- `scale_ratio_hr_raw`: 0.9924
- `state_over_null_head`: -0.0067
- `state_over_null_head_random_twin`: -0.0011
- `state_over_null_ridge`: 0.0603
- `state_over_null_ridge_random_twin`: 0.0533
- `state_trained_minus_random_ridge`: 0.007
- `trivial_hr_raw_mae_bpm`: 6.8762

## Artifacts
- `logs/v41rebased_phase_a.log`
- `logs/v41rebased_phase_b.log`
- `logs/v41rebased_phase_c.log`
- `logs/v41rebased_phase_c_report.json`
- `logs/v41rebased_geometry.json`
- `logs/emission_v41rebased_heldout.json`
- `logs/emission_v41rebased_heldout_summary.json`
- `logs/g3_v41rebased_vs_incumbent.json`
- `logs/g3_random_controls.json`
- `logs/wsweep_raw_shallow.json`
- `outputs/checkpoints/v41rebased/phase_a`
- `outputs/checkpoints/v41rebased/phase_b`
- `outputs/adapters/v41rebased_c`
- `scratch/run_v41_rebased.sh`
- `scratch/g3_state_readout.py`

## Do not retry
- Do NOT quote g3's +0.0603 as evidence that training taught the encoder momentary state. Its own random-init twin reads +0.0533 on identical rows, folds and permutation seeds; the training contribution is +0.0070, inside the k=2 band. The number is real, the attribution is not.
- Do NOT re-run a momentary-state readout on ANY encoder without its random-init twin in the same invocation. scratch/g3_state_readout.py takes '@random' as a tag suffix and costs about a minute per arm. Six of six cells measured to date (three targets in references/null-and-control-design.md plus this arm's two readouts and the incumbent's two) have random-init >= trained or inside the band; a bare permutation z on this family has no track record of surviving the control.
- Do NOT attribute v41's 1.586 bpm hr_raw win to PL60 geometry, or to state supervision, or to split-honesty, or to the 19-corpus roster. All four changed at once against arm A0. Any single-lever claim needs a one-variable arm that was not run.
- Do NOT re-base a future gate on 2.843 as if it were a family mean. It is ONE seed. The split-honest repeat-run sigma still does not exist, and per-family sd varies 58x across recipes in this project; g1's 0.7 mde remains provisional and inherited.

## Notes
All four gates pass; the headline and the caveat point in different directions and both are load-bearing.

WHAT IS NEW AND SOLID. This is the project's FIRST fully split-honest chain -- `split: train` on Phase A, B and C -- and it is the best held-out HR reader recorded: hr_raw 2.843 bpm MAE against a 6.876 always-predict-median constant (skill +4.033), corr 0.924, calibration 0.992. Against the matched split-honest comparator (arm A0 of exp-t1, band04 + `split: train` on the FROZEN incumbent PL120 tower) it wins by 1.586 bpm paired over 128 shared rows, CI95 [+0.992, +2.173], better on 74.2%. hr_peak moves from worse-than-the-constant (-0.381 skill) to +3.691. Note the comparator direction: A0's tower was trained with split=None, so A0 had a leak v41 does not, and v41 still wins.

WHAT THE GATE TEXT LET THROUGH. g3 reads +0.0603 over its permutation null on the clean pool -- three times its threshold, and against the PL120 incumbent's +0.0127. Left there, it says the state levers worked. The random-init twin, which the gate text never named and which the parent diagnostic and references/null-and-control-design.md rule 1 both require, reads +0.0533 on identical rows, folds and permutation seeds. Training contributed +0.0070, inside the +/-0.0141 band. The signal the ridge reads is the PL60 patch grid, not the weights.

WHAT DID CHANGE, AND IT IS NOT NOTHING. The incumbent's training DESTROYS state information: +0.0127 trained against +0.0430 at random init, i.e. -0.0303, and -0.0480 on the head readout. v41's training is information-neutral (+0.0070 ridge, -0.0056 head). So the levers moved the training delta by +0.0373 -- from actively destructive to harmless. That is a mechanism result about what Phase A/B optimisation does to non-HR structure, and it is the first time the destruction has been measured against a matched twin rather than inferred.

THE CEILING THAT FRAMES EVERYTHING. 15 handcrafted HR/HRV features + RF on the same W=30 cell read +0.0666 (z +5.02, logs/wsweep_raw_shallow.json). Every learned encoder, every random encoder and every trained head measured here sits below that. No representation this project has trained has ever beaten handcrafted features on momentary state.

WHAT THIS DOES NOT SAY. Nothing about seeds (n=1). Nothing about which of the four changed levers bought the HR win. Nothing about whether the state slot should ship -- the hypothesis pre-committed to dropping it on a g3 null, and g3 passed as written, so that decision is Anton's to make on the distinction between "survives" and "is learned" rather than mine to make on a gate.

#deployment-distribution #optihealth #readout #optihealth-eiv #subject-heldout #matched-baseline
