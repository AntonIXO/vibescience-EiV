---
artifacts: []
closed: true
config_note: 'One GPU extraction pass, NO training, ~40 min on RTX 5070. Adapter medgemma_12ch_e23v2,
  4-bit, strict-clean population, E24c neutral context, SEED 0, n=1000 -> n_train=700
  / n_test=300, same donor construction as E26. TWO forward passes per sample (real
  prefix + donor prefix), all stages captured from the SAME pass: S0_projector (soft-token
  tensor mean-pooled over slots, pre-injection), S1_layer0_prefix (layer-0 residual
  stream at sensor positions, mean-pooled), S2_L8/L17/L25 and S3_L34 (residual stream
  at the hr_peak emission position). Per stage: explained-variance spectrum, participation
  ratio, corr(PC_i, hr_peak) i=1..10, signal-variance alignment (variance-weighted
  mean |corr|), and a PCA+RidgeCV rank sweep k in {1,3,10,20,50,100,300} with DONOR
  and SHUFFLED arms at every k. APPARATUS BUG CAUGHT IN SMOKE TEST AND FIXED BEFORE
  THE REAL RUN: sensor positions were being located with the slot mask (which indexes
  the 96 soft tokens, not the assembled context), silently selecting constant prompt
  positions -> zero variance -> NaN spectra at S1. Fixed to use token_type_ids (1=sensor,
  gemma_runtime.py:373); verified at n=12 that S1 alignment then matches S0 to 4 decimals.
  Script scratch/probe_e30_variance_localisation.py, decision rule in docstring, smoke-tested
  twice. Committed to branch (b) before running.'
cost: null
crash_reason: ''
created_at: '2026-08-05T21:42:06+00:00'
diagnostics_measured:
- after: 10.27
  before: 7.605
  diagnostic_id: eiv_digit_readout_decoded_mae
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'FAILED as specified, and the failure is informative. I committed to branch
    (b): S0 alignment >0.30 declining monotonically to S3 <0.30. Observed alignment
    by stage: S0 0.1602, S1 0.1602, S2_L8 0.1143, S2_L17 0.1680, S2_L25 0.1176, S3_L34
    0.1033. S0 is 0.1602 -- BELOW my 0.30 bar and only just above the 0.15 branch-(a)
    threshold -- and the trajectory is NOT monotone (L17 at 0.1680 is the highest
    value in the whole series, above S0). So neither branch (a) nor branch (b) is
    cleanly instantiated by the ALIGNMENT statistic: alignment is uniformly LOW everywhere,
    from the projector output onward, with no clear decline.


    CRITICALLY, the alignment statistic turned out to be the wrong summary, and the
    rank sweep in the same run tells a clean story that alignment obscures. Full-dimensional
    Ridge MAE by stage: S0 7.605, S1 7.608, S2_L8 9.026, S2_L17 9.723, S2_L25 10.159,
    S3_L34 10.270. That IS monotone (after S1) and it is the decision-relevant quantity:
    DECODABILITY degrades steadily through the forward pass, losing 2.66 bpm from
    projector to emission position. The signal is off-manifold ALREADY at S0 (low
    alignment, needs k>=100 to decode) AND additionally degrades through the stack.
    Both branch-(a) and branch-(b) mechanisms are operating; my error was assuming
    they were mutually exclusive.'
  gate_id: gate-alignment-trajectory
  passed: false
- evidence: 'PASS at every k at every stage, with no exceptions. Donor MAE is flat
    and skill-free throughout: S0 19.222-19.375, S1 19.222-19.375, S2_L8 19.226-19.441,
    S2_L17 19.225-19.359, S2_L25 19.227-19.352, S3_L34 19.230-19.347. Every value
    is WORSE than always-predict-median (18.492), and critically the donor arms do
    NOT improve with k at any stage while the real arms improve by 8-11 bpm over the
    same range. donor_null_ok=True for all six stages. This is what makes the cross-stage
    decodability comparison trustworthy: the per-stage differences reflect real per-sample
    prefix information, not extra components fitting the marginal.'
  gate_id: gate-donor-null-all-stages
  passed: true
- evidence: 'PASS at every k at every stage. Shuffled-target MAE ranges: S0 19.181-19.732,
    S1 19.181-19.732, S2_L8 19.206-19.450, S2_L17 19.248-19.446, S2_L25 19.230-19.412,
    S3_L34 19.228-19.339. Never better than always-predict-median (18.492), including
    at k=300 against n_train=700 where overfitting was the specific risk. shuffled_null_ok=True
    for all six stages. The monotone real-arm improvement with k, and the cross-stage
    decodability decline, are therefore real signal rather than capacity artefact.'
  gate_id: gate-shuffled-null-all-stages
  passed: true
- evidence: 'PASS, EXACTLY -- to three decimal places, far inside the 1.5 bpm tolerance.
    S3_L34 full-dimensional Ridge = 10.270 vs e29''s 10.270; S3_L34 k=3 = 18.407 vs
    e29''s 18.407. Also reproduced: PC1 0.4782 (e29 0.4782), PC1-10 0.8368 (e29 0.8368),
    participation ratio 3.53 (e29 3.92 -- the only visible difference, from a re-drawn
    donor permutation and PCA component count), and corr(PC_i, peak) i=1..10 matching
    e29''s values including the distinctive PC1 = 0.042 and PC2 = 0.358. A fresh extraction
    with an independently written script reproducing e29 bit-for-bit at the anchor
    position establishes that the pipeline is identical and every cross-stage comparison
    in this experiment is admissible.'
  gate_id: gate-anchor-reproduces-e29
  passed: true
- evidence: VERIFIED IN THE REAL RUN. S0_projector and S1_layer0_prefix agree to 4
    decimals on every reported statistic -- alignment 0.1602/0.1602, PC1 0.2677/0.2677,
    PC1-10 0.8035/0.8035, participation ratio 8.21/8.21, full-dim MAE 7.605/7.608,
    and identical corr(PC_i,peak) vectors. That is the expected sanity property (layer
    0 at the sensor positions IS essentially the injected prefix) and it confirms
    the token_type_ids fix works at n=1000, not just at n=12. The original bug --
    locating sensor positions with the slot mask from _slot_mask_from_channel_mask,
    which indexes the 96 soft tokens rather than the assembled context -- produced
    constant prompt positions, zero variance across samples, and NaN spectra, and
    would have silently invalidated the entire S1 stage had the n=12 smoke test not
    been run first. Recorded in the optihealth-eiv skill so it is not reintroduced.
  gate_id: gate-s1-apparatus-verified
  passed: true
- evidence: 'HONOURED, and this experiment demonstrated exactly why the caveat was
    needed -- in an unexpected way. The alignment statistic I preregistered as PRIMARY
    proved to be a poor summary: it is uniformly low (0.10-0.17) at every stage and
    non-monotone, while the full-dimensional decodability in the SAME run declines
    cleanly and monotonically (7.605 -> 10.270 bpm). A variance-weighted mean of |corr|
    over the top 50 PCs compresses away precisely the high-rank low-variance structure
    that e29 showed carries this signal, so it was the wrong instrument for this question
    even though it was the instrument I committed to. The decodability trajectory
    is reported as the substantive result and the alignment trajectory is reported
    as a failed summary statistic, not quietly swapped. Standing caveats reaffirmed:
    these are FITTED readouts on FROZEN features; the trajectory shows WHERE decodability
    is lost, not that restoring it would restore generation quality; S0 and S1 are
    mean-pooled summaries and a per-slot analysis could differ.'
  gate_id: gate-scope-alignment-is-descriptive
  passed: true
git_ref: ''
hypothesis_id: the-hr-peak-signal-enters-the-lm-well-ba9dd9
id: exp-e30-variance-localisation-20260805
interpretation:
  at: '2026-08-18T21:08:43+00:00'
  failure_mode: test_invalid
  lesson: 'The preregistered PRIMARY statistic was the wrong instrument and the run''s
    real answer came from its secondary sweep, so read this record for the decodability
    numbers and ignore the alignment ones. Signal-variance alignment (variance-weighted
    mean |corr(PC_i,y)| over the top 50 PCs) is uniformly LOW and NON-monotone at
    every stage - S0 0.1602, S1 0.1602, L8 0.1143, L17 0.1680 (the series maximum,
    above S0), L25 0.1176, L34 0.1033 - against a preregistered bar of >0.30 declining
    monotonically; a variance-weighted average over leading components is structurally
    blind to the high-rank low-variance directions this signal lives in, so NEVER
    preregister it for this question again. Full-dimensional RidgeCV decodability
    in the SAME run (n=1000, 700/300, SEED 0) is monotone and decision-relevant: S0
    projector 7.605, S1 7.608, L8 9.026, L17 9.723, L25 10.159, L34 emission position
    10.270 bpm, against always-predict-median 18.492 - the frozen forward pass costs
    2.665 bpm. Donor arms 19.222-19.441 and shuffled arms 19.181-19.732 at every k
    at every stage, both worse than a constant and neither improving with k, and the
    L34 anchor reproduces exp-e29 exactly (10.270/10.270, k=3 18.407/18.407). ACTIONABLE:
    the representation ENTERING the LM (7.605) is materially better than the record
    assumed, and every E24-E29 number measured at/after the emission position measured
    a degraded copy - re-site readout-side levers accordingly. Scope: fitted readouts
    on FROZEN features, mean-pooled over 96 slots; this bounds what each stage permits,
    not what training finds.'
  transfers_to:
  - the-hr-peak-signal-at-the-projector-4aa472
  - the-hr-peak-failure-is-a-readout-965f58
  - hyp-e7-projector-bottleneck-20260814
  - hyp-e8-lm-readout-transfer-20260814
key_numbers: {}
observed_effects:
- delta: 2.665
  diagnostic_id: eiv_digit_readout_decoded_mae
  direction: up
parent_experiment_id: exp-e29-prefix-rank-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_peak_excursion_increment_r2: false
provenance: {}
recipe_fingerprint: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[the-hr-peak-signal-enters-the-lm-well-ba9dd9]].
Branches from [[exp-e29-prefix-rank-20260805]].

## Measurements
- [[eiv_digit_readout_decoded_mae]]: before 7.605 → after 10.27  (Δ +2.665, up)

## Gates
- `gate-alignment-trajectory`: **FAIL** — FAILED as specified, and the failure is informative. I committed to branch (b): S0 alignment >0.30 declining monotonically to S3 <0.30. Observed alignment by stage: S0 0.1602, S1 0.1602, S2_L8 0.1143, S2_L17 0.1680, S2_L25 0.1176, S3_L34 0.1033. S0 is 0.1602 -- BELOW my 0.30 bar and only just above the 0.15 branch-(a) threshold -- and the trajectory is NOT monotone (L17 at 0.1680 is the highest value in the whole series, above S0). So neither branch (a) nor branch (b) is cleanly instantiated by the ALIGNMENT statistic: alignment is uniformly LOW everywhere, from the projector output onward, with no clear decline.

CRITICALLY, the alignment statistic turned out to be the wrong summary, and the rank sweep in the same run tells a clean story that alignment obscures. Full-dimensional Ridge MAE by stage: S0 7.605, S1 7.608, S2_L8 9.026, S2_L17 9.723, S2_L25 10.159, S3_L34 10.270. That IS monotone (after S1) and it is the decision-relevant quantity: DECODABILITY degrades steadily through the forward pass, losing 2.66 bpm from projector to emission position. The signal is off-manifold ALREADY at S0 (low alignment, needs k>=100 to decode) AND additionally degrades through the stack. Both branch-(a) and branch-(b) mechanisms are operating; my error was assuming they were mutually exclusive.
- `gate-donor-null-all-stages`: PASS — PASS at every k at every stage, with no exceptions. Donor MAE is flat and skill-free throughout: S0 19.222-19.375, S1 19.222-19.375, S2_L8 19.226-19.441, S2_L17 19.225-19.359, S2_L25 19.227-19.352, S3_L34 19.230-19.347. Every value is WORSE than always-predict-median (18.492), and critically the donor arms do NOT improve with k at any stage while the real arms improve by 8-11 bpm over the same range. donor_null_ok=True for all six stages. This is what makes the cross-stage decodability comparison trustworthy: the per-stage differences reflect real per-sample prefix information, not extra components fitting the marginal.
- `gate-shuffled-null-all-stages`: PASS — PASS at every k at every stage. Shuffled-target MAE ranges: S0 19.181-19.732, S1 19.181-19.732, S2_L8 19.206-19.450, S2_L17 19.248-19.446, S2_L25 19.230-19.412, S3_L34 19.228-19.339. Never better than always-predict-median (18.492), including at k=300 against n_train=700 where overfitting was the specific risk. shuffled_null_ok=True for all six stages. The monotone real-arm improvement with k, and the cross-stage decodability decline, are therefore real signal rather than capacity artefact.
- `gate-anchor-reproduces-e29`: PASS — PASS, EXACTLY -- to three decimal places, far inside the 1.5 bpm tolerance. S3_L34 full-dimensional Ridge = 10.270 vs e29's 10.270; S3_L34 k=3 = 18.407 vs e29's 18.407. Also reproduced: PC1 0.4782 (e29 0.4782), PC1-10 0.8368 (e29 0.8368), participation ratio 3.53 (e29 3.92 -- the only visible difference, from a re-drawn donor permutation and PCA component count), and corr(PC_i, peak) i=1..10 matching e29's values including the distinctive PC1 = 0.042 and PC2 = 0.358. A fresh extraction with an independently written script reproducing e29 bit-for-bit at the anchor position establishes that the pipeline is identical and every cross-stage comparison in this experiment is admissible.
- `gate-s1-apparatus-verified`: PASS — VERIFIED IN THE REAL RUN. S0_projector and S1_layer0_prefix agree to 4 decimals on every reported statistic -- alignment 0.1602/0.1602, PC1 0.2677/0.2677, PC1-10 0.8035/0.8035, participation ratio 8.21/8.21, full-dim MAE 7.605/7.608, and identical corr(PC_i,peak) vectors. That is the expected sanity property (layer 0 at the sensor positions IS essentially the injected prefix) and it confirms the token_type_ids fix works at n=1000, not just at n=12. The original bug -- locating sensor positions with the slot mask from _slot_mask_from_channel_mask, which indexes the 96 soft tokens rather than the assembled context -- produced constant prompt positions, zero variance across samples, and NaN spectra, and would have silently invalidated the entire S1 stage had the n=12 smoke test not been run first. Recorded in the optihealth-eiv skill so it is not reintroduced.
- `gate-scope-alignment-is-descriptive`: PASS — HONOURED, and this experiment demonstrated exactly why the caveat was needed -- in an unexpected way. The alignment statistic I preregistered as PRIMARY proved to be a poor summary: it is uniformly low (0.10-0.17) at every stage and non-monotone, while the full-dimensional decodability in the SAME run declines cleanly and monotonically (7.605 -> 10.270 bpm). A variance-weighted mean of |corr| over the top 50 PCs compresses away precisely the high-rank low-variance structure that e29 showed carries this signal, so it was the wrong instrument for this question even though it was the instrument I committed to. The decodability trajectory is reported as the substantive result and the alignment trajectory is reported as a failed summary statistic, not quietly swapped. Standing caveats reaffirmed: these are FITTED readouts on FROZEN features; the trajectory shows WHERE decodability is lost, not that restoring it would restore generation quality; S0 and S1 are mean-pooled summaries and a per-slot analysis could differ.

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `test_invalid`

The preregistered PRIMARY statistic was the wrong instrument and the run's real answer came from its secondary sweep, so read this record for the decodability numbers and ignore the alignment ones. Signal-variance alignment (variance-weighted mean |corr(PC_i,y)| over the top 50 PCs) is uniformly LOW and NON-monotone at every stage - S0 0.1602, S1 0.1602, L8 0.1143, L17 0.1680 (the series maximum, above S0), L25 0.1176, L34 0.1033 - against a preregistered bar of >0.30 declining monotonically; a variance-weighted average over leading components is structurally blind to the high-rank low-variance directions this signal lives in, so NEVER preregister it for this question again. Full-dimensional RidgeCV decodability in the SAME run (n=1000, 700/300, SEED 0) is monotone and decision-relevant: S0 projector 7.605, S1 7.608, L8 9.026, L17 9.723, L25 10.159, L34 emission position 10.270 bpm, against always-predict-median 18.492 - the frozen forward pass costs 2.665 bpm. Donor arms 19.222-19.441 and shuffled arms 19.181-19.732 at every k at every stage, both worse than a constant and neither improving with k, and the L34 anchor reproduces exp-e29 exactly (10.270/10.270, k=3 18.407/18.407). ACTIONABLE: the representation ENTERING the LM (7.605) is materially better than the record assumed, and every E24-E29 number measured at/after the emission position measured a degraded copy - re-site readout-side levers accordingly. Scope: fitted readouts on FROZEN features, mean-pooled over 96 slots; this bounds what each stage permits, not what training finds.

Transfers to: [[the-hr-peak-signal-at-the-projector-4aa472]], [[the-hr-peak-failure-is-a-readout-965f58]], [[hyp-e7-projector-bottleneck-20260814]], [[hyp-e8-lm-readout-transfer-20260814]]

## Notes
MY BRANCH-(b) PREDICTION FAILED, THE PREREGISTERED PRIMARY STATISTIC WAS THE WRONG INSTRUMENT, AND THE RANK SWEEP IN THE SAME RUN ANSWERED THE QUESTION ANYWAY. All three facts are recorded; none is swapped quietly for another.

=== 1. WHAT I PREDICTED AND WHAT HAPPENED ===
I committed to: S0 alignment >0.30, declining monotonically to S3 <0.30 (the LM progressively swamps a well-aligned signal). Observed alignment: S0 0.1602, S1 0.1602, L8 0.1143, L17 0.1680, L25 0.1176, L34 0.1033. S0 is already LOW and the series is NOT monotone -- L17 is the maximum of the whole trajectory. Neither branch (a) nor (b) is cleanly instantiated by this statistic.

=== 2. WHY THE STATISTIC WAS WRONG ===
Signal-variance alignment is a variance-weighted mean of |corr(PC_i, y)| over the top 50 PCs. e29 established that this signal lives in HIGH-RANK, LOW-VARIANCE directions -- exactly what a variance-weighted average over leading components discards. I preregistered an instrument that is blind to the structure I already knew was there. Recorded as a design error, not excused.

=== 3. THE ANSWER, FROM THE RANK SWEEP (decision-relevant, monotone, controlled) ===
Full-dimensional Ridge MAE by stage, same samples/split/donors, one pass:
  S0 projector      7.605
  S1 layer0 prefix  7.608
  S2 L8             9.026
  S2 L17            9.723
  S2 L25           10.159
  S3 L34           10.270      (always-median 18.492)
Monotone after injection. The frozen forward pass COSTS 2.665 bpm of decodable peak. Donor arms 19.2-19.4 and shuffled arms 19.2-19.7 at every k at every stage -- both worse than a constant, and neither improves with k while the real arm improves 8-11 bpm. Anchor reproduced e29 EXACTLY (10.270/10.270 full-dim, 18.407/18.407 at k=3) from an independently written script.

=== 4. THE ACTUAL DIAGNOSIS: BOTH MECHANISMS, NOT ONE ===
My hypothesis assumed (a) and (b) were mutually exclusive. They are not.
  (a) IS TRUE: the projector output is ALREADY off-manifold. At S0, alignment is 0.1602 and the rank sweep needs k>=100 to decode (k=3 gives 18.170 vs median 18.492 -- a rank-3 subspace of the PROJECTOR OUTPUT is already worthless). The signal leaves the projector spread across low-variance directions.
  (b) IS ALSO TRUE, but smaller: decodability then degrades a further 2.665 bpm monotonically through the stack.
So the fix classes are NOT alternatives to choose between. Upstream geometry (whitening / variance-matching / vocab-embedding init) addresses (a); gated cross-attention or Q-Former alignment addresses (b). (a) is the larger and cheaper target.

=== 5. THE MOST ACTIONABLE NUMBER IN THIS EXPERIMENT ===
THE PROJECTOR OUTPUT DECODES AT 7.605 bpm -- 10.887 bpm better than always-predict-median, and 2.665 bpm better than anything measurable at the emission position. Every prior experiment in this series (E24-E29) measured at or after the emission position and therefore measured a DEGRADED copy. The representation entering the LM is materially better than the problem record has assumed. This also reframes rb-e4 (~4 bpm at the readout) and every readout-side lever: they operate on the degraded end.

=== 6. CONSEQUENCES FOR THE QUEUE ===
* rb-e30 is DONE -- close it.
* The next arm should target (a): variance-matching / whitening the projector output to the token-embedding distribution, and/or initialising soft tokens from real vocabulary embeddings. Both are near-free and both are now motivated by a measurement rather than by analogy.
* Gated cross-attention / Q-Former remain live for (b) but address the smaller 2.665 bpm component; they should NOT be launched first.
* A per-slot (rather than mean-pooled) analysis of S0 is the obvious cheap follow-up, since S0/S1 here are mean-pooled summaries and the projector emits 96 slots.

=== 7. SCOPE, UNCHANGED AND STILL BINDING ===
Fitted readouts on FROZEN features. This bounds what each stage's representation permits; it does not show that restoring decodability would restore generation quality, and it does not prove cross-entropy training would find these solutions. S0 and S1 are mean-pooled over slots/positions. The 2.665 bpm forward-pass cost is a decodability difference, not a causal intervention result.

Artifacts: outputs/probes/e30_variance_localisation.json, logs/e30_localisation.log, logs/e30_smoke.log, logs/e30_smoke2.log. Script scratch/probe_e30_variance_localisation.py. ~40 min GPU, no training.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
