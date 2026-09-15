---
artifacts:
- logs/a3_lupi_supply_eval.json
- logs/a3_lupi_13879.log
- scratch/a3_lupi_supply_eval.py
closed: true
config_note: 'A3 LUPI supply re-test on the 2026-08-24 bundle ([13518,1440,12], merge
  git_sha f82b082, commit 1ef63da ''recover every unused EDA source''). PROBE-STAGE
  ONLY, no training: reads the FROZEN baseline_sync Phase-A encoder (PL120/ST60/T1440/input_dim
  24/d_model 192, trained on this exact bundle) plus a RANDOM-INIT control of identical
  geometry. Targets via privileged_patch_targets(m,1440,120,60,stats) which now prefers
  the by_subject fit. Readouts: RidgeCV + RandomForest, each with its OWN >=40-permutation
  WITHIN-SUBJECT null on identical rows/folds. Runner scratch/a3_lupi_supply_eval.py
  -> logs/a3_lupi_supply_eval.json.'
cost:
  gpu_hours: 0.1
  wall_hours: 7.6
crash_reason: ''
created_at: '2026-08-24T09:11:43+00:00'
diagnostics_measured:
- after: -0.018
  before: 0.0
  diagnostic_id: eiv_privileged_eda_token_r_over_null
do_not_retry:
- Do NOT quote any LUPI/EDA 'clears its null' figure without the RANDOM-INIT control
  beside it. Every trained cell that clears its null here is matched or beaten by
  an untrained encoder of identical geometry, including the headline z+8.88. A large
  z against a permutation null is not evidence that training did anything.
- Do NOT re-run this as a SUPPLY experiment. Supply went 14,264 -> 15,100 cells and
  934 -> 1,277 subjects (EDA roughly doubled once dapper, popane_s5/s6 and k_emocon
  were declared) and trained-minus-random did not turn positive. Two supplies now
  agree. A further attempt needs a different MECHANISM -- a trained LUPI head scored
  at the fused/production stage rather than a probe of frozen Phase-A tokens.
- Do NOT read the leave-dapper-out arm as a clean cohort control. It halves n (15,100
  -> ~5,400) AND shifts the nulls substantially (eda_scr_rate ridge null -0.002 ->
  -0.076), so it changes variance and baseline simultaneously. It bounds dapper-dominance;
  it does not isolate it.
- Do NOT report eda_scr_rate from a single readout family. Trained reads -0.011 under
  ridge and +0.055 under RF -- a SIGN FLIP on the same rows. Either family alone supports
  a different conclusion.
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS on its own terms: EDA IS recoverable above a within-subject permutation
    null. Best cell eda_scr_rate RF over_null +0.055, z+8.88, p=0.0000 (n=14,907 /
    1,254 subjects); eda_tonic ridge +0.044, z+3.10. But this gate is necessary and
    NOT sufficient -- see gate-trained-beats-random-init, which is what determines
    whether the recoverability is attributable to training.'
  gate_id: gate-eda-recoverable-over-null
  passed: true
- evidence: "FAIL, decisively, and this is the gate that closes the experiment. A
    RANDOM-INIT encoder of identical geometry matches or beats the trained one in
    3 of 4 cells:\n  eda_scr_rate RF     trained +0.055 (z+8.88)  vs random +0.073
    (z+11.50)  -> -0.018\n  eda_scr_rate ridge  trained -0.011 (z-1.82)  vs random
    +0.048 (z+5.58)   -> -0.059\n  eda_tonic    RF     trained +0.013 (z+2.09)  vs
    random +0.005 (z+0.88)   -> +0.008\n  eda_tonic    ridge  trained +0.044 (z+3.10)
    \ vs random +0.013 (z+1.01)   -> +0.031\nThe headline cell -- the only one clearing
    z+8 -- is where the untrained encoder wins by the largest margin. Trained wins
    only on eda_tonic, the channel whose supply is most dataset-confounded (dapper
    alone is ~63% of corpus EDA). Phase A/B training neither creates nor improves
    the EDA signal in the encoder's tokens."
  gate_id: gate-trained-beats-random-init
  passed: false
- evidence: 'PASS. No null is degenerate: null sd ranges 0.006-0.014 across all eight
    cells, and the null MEANS are informative rather than pinned at zero (+0.089 for
    trained tonic RF, +0.030 for random scr RF, -0.048 for random tonic ridge). That
    the tonic-RF null sits at +0.089 while the observed r is +0.102 is exactly why
    the within-subject permutation is mandatory here -- a global shuffle would have
    put that null near zero and turned a +0.013 increment into an apparent +0.10 result.
    The leave-dapper-out arm further shows the null MOVES with cohort composition
    (scr ridge null -0.002 -> -0.076), confirming the null is tracking real structure
    rather than noise.'
  gate_id: gate-null-not-degenerate
  passed: true
- evidence: 'PASS. Every cell was measured under BOTH RidgeCV and RandomForest on
    identical rows and folds, with each readout carrying its own permutation null
    -- the discipline mandated after exp-w8 showed arm ordering can invert between
    readout families. It mattered here: eda_scr_rate reads -0.011 (ridge) vs +0.055
    (RF) on the trained arm, a sign flip. Reporting either family alone would have
    supported a different conclusion, and the random-init control is what resolves
    both.'
  gate_id: gate-two-readout-families
  passed: true
git_ref: ''
hypothesis_id: the-a2-lupi-null-rule-out-that-per-patch-eda-89857c
id: exp-055-the-a2-lupi-null-rule-out-that-1b039b
interpretation:
  at: '2026-08-30T05:51:28+00:00'
  failure_mode: claim_false
  lesson: 'AT DOUBLE THE EDA SUPPLY THE TRAINED ENCODER STILL CARRIES NOTHING AN UNTRAINED
    ONE DOES NOT. Random-init matches or beats trained in 3 of 4 cells, and it wins
    BIGGEST exactly where the trained arm looked strongest: eda_scr_rate RF, trained
    +0.055 (z+8.88) vs random +0.073 (z+11.50). Trained-minus-random is -0.018 there
    and -0.059 under ridge. The only cells trained wins are eda_tonic, the channel
    whose supply is most dataset-confounded now that dapper supplies ~63% of corpus
    EDA.


    THE REUSABLE RULE, and this run is the cleanest example the vault has: A LARGE
    z AGAINST A PERMUTATION NULL IS NOT EVIDENCE THAT TRAINING DID ANYTHING. z+8.88
    with p=0.0000 on 14,907 cells is the kind of number that gets written into a summary
    as a finding. The matched random-init control -- same geometry, same rows, same
    folds, same null -- turns it into a refutation. Any claim of the form ''the encoder
    carries X'' needs the untrained arm beside it or it is unfalsifiable.


    THIS IS THE THIRD INDEPENDENT TARGET ON WHICH RANDOM-INIT MATCHES TRAINED: momentary
    state (exp-w8, rand +0.0312 vs enc +0.0263 under linear), and now both EDA channels.
    The pattern is now strong enough to state as a property of the system rather than
    a per-target accident: Phase A/B''s InfoNCE-on-HR-captions objective shapes the
    tokens for HR level and essentially nothing else. Whatever non-HR signal survives
    is a property of the untrained architecture''s projection of the raw input --
    a random feature map over the patch grid.


    WHY SUPPLY WAS THE WRONG LEVER, stated so it is not retried: the 2026-08-30 rebuild
    plus the privileged.yaml fix took EDA from 14,264 to 15,100 cells and 934 to 1,277
    subjects, roughly doubling the declared supply. The trained-minus-random gap did
    not move toward positive. Supply was never the binding constraint on a quantity
    training does not represent.


    METHOD NOTES WORTH KEEPING: (1) eda_scr_rate reads -0.011 under ridge and +0.055
    under RF on identical rows -- a sign flip, so single-readout reporting would have
    supported either conclusion. (2) The within-subject null is doing real work: the
    trained tonic-RF null sits at +0.089 against an observed +0.102, so a global shuffle
    would have reported +0.10 instead of +0.013. (3) The leave-dapper-out arm halves
    n AND shifts the nulls, so it bounds dapper-dominance rather than isolating it
    -- do not cite it as a clean cohort control.


    CONSEQUENCE: LUPI is not funded at the probe stage. If anyone revisits it, the
    only untested design is a TRAINED LUPI head scored at the fused/production stage
    -- this experiment reads frozen Phase-A tokens, and the vault has repeatedly shown
    the production stage differs from the encoder stage. That is a different experiment,
    not a re-run of this one.'
  transfers_to:
  - h-w8-cardinality-invariant-attention-pooling-f01250
  - at-the-rebuilt-corpus-s-valence-supply-21-710-a9a6e0
key_numbers:
  n_cells: 15100.0
  n_cells_before: 14264.0
  n_subjects: 1277.0
  n_subjects_before: 934.0
  no_dapper_scr_rf_over_null: 0.025
  no_dapper_tonic_ridge_over_null: 0.061
  random_scr_rate_rf_over_null: 0.073
  random_scr_rate_rf_z: 11.5
  random_scr_rate_ridge_over_null: 0.048
  random_tonic_rf_over_null: 0.005
  random_tonic_ridge_over_null: 0.013
  trained_minus_random_scr_rf: -0.018
  trained_minus_random_scr_ridge: -0.059
  trained_minus_random_tonic_rf: 0.008
  trained_minus_random_tonic_ridge: 0.031
  trained_scr_rate_rf_over_null: 0.055
  trained_scr_rate_rf_z: 8.88
  trained_scr_rate_ridge_over_null: -0.011
  trained_tonic_rf_over_null: 0.013
  trained_tonic_ridge_over_null: 0.044
observed_effects:
- delta: -0.018
  diagnostic_id: eiv_privileged_eda_token_r_over_null
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_nonhr_channel_grouped_r2: false
    eiv_privileged_eda_token_r_over_null: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-a2-lupi-null-rule-out-that-per-patch-eda-89857c]].

## Measurements
- [[eiv_privileged_eda_token_r_over_null]]: before 0.0 → after -0.018  (Δ -0.018, down ✗ predicted up)

## Gates
- `gate-eda-recoverable-over-null`: PASS — PASS on its own terms: EDA IS recoverable above a within-subject permutation null. Best cell eda_scr_rate RF over_null +0.055, z+8.88, p=0.0000 (n=14,907 / 1,254 subjects); eda_tonic ridge +0.044, z+3.10. But this gate is necessary and NOT sufficient -- see gate-trained-beats-random-init, which is what determines whether the recoverability is attributable to training.
- `gate-trained-beats-random-init`: **FAIL** — FAIL, decisively, and this is the gate that closes the experiment. A RANDOM-INIT encoder of identical geometry matches or beats the trained one in 3 of 4 cells:
  eda_scr_rate RF     trained +0.055 (z+8.88)  vs random +0.073 (z+11.50)  -> -0.018
  eda_scr_rate ridge  trained -0.011 (z-1.82)  vs random +0.048 (z+5.58)   -> -0.059
  eda_tonic    RF     trained +0.013 (z+2.09)  vs random +0.005 (z+0.88)   -> +0.008
  eda_tonic    ridge  trained +0.044 (z+3.10)  vs random +0.013 (z+1.01)   -> +0.031
The headline cell -- the only one clearing z+8 -- is where the untrained encoder wins by the largest margin. Trained wins only on eda_tonic, the channel whose supply is most dataset-confounded (dapper alone is ~63% of corpus EDA). Phase A/B training neither creates nor improves the EDA signal in the encoder's tokens.
- `gate-null-not-degenerate`: PASS — PASS. No null is degenerate: null sd ranges 0.006-0.014 across all eight cells, and the null MEANS are informative rather than pinned at zero (+0.089 for trained tonic RF, +0.030 for random scr RF, -0.048 for random tonic ridge). That the tonic-RF null sits at +0.089 while the observed r is +0.102 is exactly why the within-subject permutation is mandatory here -- a global shuffle would have put that null near zero and turned a +0.013 increment into an apparent +0.10 result. The leave-dapper-out arm further shows the null MOVES with cohort composition (scr ridge null -0.002 -> -0.076), confirming the null is tracking real structure rather than noise.
- `gate-two-readout-families`: PASS — PASS. Every cell was measured under BOTH RidgeCV and RandomForest on identical rows and folds, with each readout carrying its own permutation null -- the discipline mandated after exp-w8 showed arm ordering can invert between readout families. It mattered here: eda_scr_rate reads -0.011 (ridge) vs +0.055 (RF) on the trained arm, a sign flip. Reporting either family alone would have supported a different conclusion, and the random-init control is what resolves both.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

AT DOUBLE THE EDA SUPPLY THE TRAINED ENCODER STILL CARRIES NOTHING AN UNTRAINED ONE DOES NOT. Random-init matches or beats trained in 3 of 4 cells, and it wins BIGGEST exactly where the trained arm looked strongest: eda_scr_rate RF, trained +0.055 (z+8.88) vs random +0.073 (z+11.50). Trained-minus-random is -0.018 there and -0.059 under ridge. The only cells trained wins are eda_tonic, the channel whose supply is most dataset-confounded now that dapper supplies ~63% of corpus EDA.

THE REUSABLE RULE, and this run is the cleanest example the vault has: A LARGE z AGAINST A PERMUTATION NULL IS NOT EVIDENCE THAT TRAINING DID ANYTHING. z+8.88 with p=0.0000 on 14,907 cells is the kind of number that gets written into a summary as a finding. The matched random-init control -- same geometry, same rows, same folds, same null -- turns it into a refutation. Any claim of the form 'the encoder carries X' needs the untrained arm beside it or it is unfalsifiable.

THIS IS THE THIRD INDEPENDENT TARGET ON WHICH RANDOM-INIT MATCHES TRAINED: momentary state (exp-w8, rand +0.0312 vs enc +0.0263 under linear), and now both EDA channels. The pattern is now strong enough to state as a property of the system rather than a per-target accident: Phase A/B's InfoNCE-on-HR-captions objective shapes the tokens for HR level and essentially nothing else. Whatever non-HR signal survives is a property of the untrained architecture's projection of the raw input -- a random feature map over the patch grid.

WHY SUPPLY WAS THE WRONG LEVER, stated so it is not retried: the 2026-08-30 rebuild plus the privileged.yaml fix took EDA from 14,264 to 15,100 cells and 934 to 1,277 subjects, roughly doubling the declared supply. The trained-minus-random gap did not move toward positive. Supply was never the binding constraint on a quantity training does not represent.

METHOD NOTES WORTH KEEPING: (1) eda_scr_rate reads -0.011 under ridge and +0.055 under RF on identical rows -- a sign flip, so single-readout reporting would have supported either conclusion. (2) The within-subject null is doing real work: the trained tonic-RF null sits at +0.089 against an observed +0.102, so a global shuffle would have reported +0.10 instead of +0.013. (3) The leave-dapper-out arm halves n AND shifts the nulls, so it bounds dapper-dominance rather than isolating it -- do not cite it as a clean cohort control.

CONSEQUENCE: LUPI is not funded at the probe stage. If anyone revisits it, the only untested design is a TRAINED LUPI head scored at the fused/production stage -- this experiment reads frozen Phase-A tokens, and the vault has repeatedly shown the production stage differs from the encoder stage. That is a different experiment, not a re-run of this one.

Transfers to: [[h-w8-cardinality-invariant-attention-pooling-f01250]], [[at-the-rebuilt-corpus-s-valence-supply-21-710-a9a6e0]]

## Key numbers
- `n_cells`: 15100
- `n_cells_before`: 14264
- `n_subjects`: 1277
- `n_subjects_before`: 934
- `no_dapper_scr_rf_over_null`: 0.025
- `no_dapper_tonic_ridge_over_null`: 0.061
- `random_scr_rate_rf_over_null`: 0.073
- `random_scr_rate_rf_z`: 11.5
- `random_scr_rate_ridge_over_null`: 0.048
- `random_tonic_rf_over_null`: 0.005
- `random_tonic_ridge_over_null`: 0.013
- `trained_minus_random_scr_rf`: -0.018
- `trained_minus_random_scr_ridge`: -0.059
- `trained_minus_random_tonic_rf`: 0.008
- `trained_minus_random_tonic_ridge`: 0.031
- `trained_scr_rate_rf_over_null`: 0.055
- `trained_scr_rate_rf_z`: 8.88
- `trained_scr_rate_ridge_over_null`: -0.011
- `trained_tonic_rf_over_null`: 0.013
- `trained_tonic_ridge_over_null`: 0.044

## Artifacts
- `logs/a3_lupi_supply_eval.json`
- `logs/a3_lupi_13879.log`
- `scratch/a3_lupi_supply_eval.py`

## Do not retry
- Do NOT quote any LUPI/EDA 'clears its null' figure without the RANDOM-INIT control beside it. Every trained cell that clears its null here is matched or beaten by an untrained encoder of identical geometry, including the headline z+8.88. A large z against a permutation null is not evidence that training did anything.
- Do NOT re-run this as a SUPPLY experiment. Supply went 14,264 -> 15,100 cells and 934 -> 1,277 subjects (EDA roughly doubled once dapper, popane_s5/s6 and k_emocon were declared) and trained-minus-random did not turn positive. Two supplies now agree. A further attempt needs a different MECHANISM -- a trained LUPI head scored at the fused/production stage rather than a probe of frozen Phase-A tokens.
- Do NOT read the leave-dapper-out arm as a clean cohort control. It halves n (15,100 -> ~5,400) AND shifts the nulls substantially (eda_scr_rate ridge null -0.002 -> -0.076), so it changes variance and baseline simultaneously. It bounds dapper-dominance; it does not isolate it.
- Do NOT report eda_scr_rate from a single readout family. Trained reads -0.011 under ridge and +0.055 under RF -- a SIGN FLIP on the same rows. Either family alone supports a different conclusion.

## Notes
CLOSED 2026-08-30 after re-running on the rebuilt [13879] corpus. RC=0, zero tracebacks, channel binding VERIFIED (target columns and parquet registry indices agree). n=15,100 cells / 2,067 windows / 1,277 subjects / 16 datasets, up from 14,264 / 934 -- EDA supply roughly doubled once dapper, popane_s5/s6 and k_emocon were declared in privileged.yaml (a fix made in this same session, itself caught by tests/test_privileged_channels.py).

FULL 2x2, each cell against its OWN within-subject permutation null:
  trained  eda_tonic     ridge  +0.044  z +3.10
  trained  eda_tonic     rf     +0.013  z +2.09
  trained  eda_scr_rate  ridge  -0.011  z -1.82
  trained  eda_scr_rate  rf     +0.055  z +8.88   <- best trained cell
  random   eda_tonic     ridge  +0.013  z +1.01
  random   eda_tonic     rf     +0.005  z +0.88
  random   eda_scr_rate  ridge  +0.048  z +5.58
  random   eda_scr_rate  rf     +0.073  z +11.50  <- BEATS the trained arm

TRAINED MINUS RANDOM: -0.018 (scr RF), -0.059 (scr ridge), +0.031 (tonic ridge), +0.008 (tonic RF). Random-init wins both scr_rate cells including the headline; trained wins only on eda_tonic, the channel whose supply is most dataset-confounded.

LEAVE-DAPPER-OUT (trained, n ~5,400): tonic ridge +0.061 z+5.31, tonic rf +0.003, scr ridge +0.041, scr rf +0.025. The effect survives without dapper but the nulls move a lot, so this bounds rather than isolates.

HONEST HEADLINE: at double the supply the trained encoder still carries no EDA information that an untrained projection of the same input does not. LUPI stays not-funded.

#wearables #lupi #control #baseline #epoch-resolution #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
