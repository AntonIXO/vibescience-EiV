---
artifacts: []
closed: true
config_note: 'W5 three-stage extent contraction, CPU/GPU-light readout probe. Script
  scratch/w5_three_stage_contraction.py, falsifiers P1-P4 committed PRE-DATA at 50c3a85.
  Corpus ac7d2b1c [12547,1440,12]. Checkpoint outputs/checkpoints/ab_ctrl/phase_b
  (current-recipe chain on this corpus), PL=120 ST=60 n_patches=23 input_dim=24 (with_presence_v1).
  Readout held IDENTICAL across stages: subject-grouped GroupKFold(5) RidgeCV predicting
  the window''s PRESENT-ONLY mean HR in bpm; ratio = std(OOF pred)/std(truth) within
  cohort. Arms: naive_mean (W4b reproduction, no fit) / enc_trained (avg pool) / enc_random
  (random-init, matched geometry) / enc_masked (SAME trained tokens, mask-weighted
  re-pool, zero training) / production (channel_fusion + projector pre-pool, RP to
  48/ch) / shuffle control. BASELINES FROZEN PRE-RUN from W4b logs/w4b_variance_compression.json:
  case 0.0285, wesad 0.0825, lifesnaps 0.6992, pmdata 0.8567 -- these four are the
  BLOCKING harness-validity reproduction band (+-0.05); a failure forbids comparing
  anything else in the run. Logs: logs/w5_three_stage_contraction.json.'
cost: null
crash_reason: ''
created_at: '2026-08-20T21:34:16+00:00'
diagnostics_measured:
- after: 0.8159
  before: 0.0804
  diagnostic_id: eiv_extent_variance_compression_ratio
do_not_retry: []
external_run: logs/w5_three_stage_contraction.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1-three-stage-compression-measured
  passed: true
- evidence: ''
  gate_id: g2-random-init-control
  passed: true
- evidence: ''
  gate_id: g3-masked-repool-headroom
  passed: true
- evidence: ''
  gate_id: g4-shuffle-control
  passed: true
git_ref: ''
hypothesis_id: h-w5-contraction-survives-into-the-91ad0b
id: exp-w5-three-stage-contraction-20260820
interpretation:
  at: '2026-08-20T21:44:36+00:00'
  failure_mode: null
  lesson: 'The extent contraction is ARCHITECTURAL AND REVERSIBLE, and the reversal
    costs nothing. Short-cohort mean variance-compression ratio: naive 1440-mean 0.0804
    -> trained encoder 0.1822 -> mask-weighted re-pool of the SAME FROZEN TOKENS 0.8159,
    against a preregistered 0.60 bar, with the shuffle control at 0.0362 and the harness
    reproducing W4b to five decimals. Trained and random-init encoders sit 0.0140
    apart (bar 0.15), so the defect is a property of presence-blind pooling GEOMETRY,
    not of learned weights.


    THE TRANSFERABLE FINDING: the information was never destroyed at patch construction.
    It is present in the tokens and destroyed at the pooling step, by a single line
    (adaptive_avg_pool2d over all 23 patches regardless of presence). A post-hoc re-weighting
    with zero training and zero new parameters recovers it -- and w5b confirms the
    recovery is real rather than noise amplification: within-cohort r rises 0.497
    -> 0.727 on short cohorts and MAE falls 8.73 -> 5.23 bpm, with optihealth_supabase
    going r 0.630 -> 0.962 and MAE 7.46 -> 1.67 bpm.


    WHY THIS MATTERS BEYOND ITS OWN PROBLEM, and this is the part a future session
    should carry: every prior state/affect null measured on short-extent cohorts through
    this pooling was read through an instrument compressed to 7-44% of the cohort''s
    true between-subject spread. That does not automatically overturn those nulls
    -- most were measured on within-window transition rows or on lifesnaps (a LONG
    cohort at ratio 0.70-1.04, barely affected) -- but any null whose evaluation population
    was dominated by case / popane / wesad / neuro_stress must be re-read with this
    in mind before being cited as settled.


    WHAT IT DOES NOT LICENSE. Nothing here is trained end-to-end. The supabase 1.67
    bpm is a PROBE on frozen features, not an emission, and the vault''s emission
    seed band (6.29 bpm, exceeding its own 4.02 trivial baseline) still governs any
    deployment claim. The production stage already sits at 0.5157, i.e. the projector''s
    cross-attention partially compensates on its own, which RAISES the risk that an
    end-to-end mask-weighted arm disturbs a learned calibration -- so H-W6''s dense
    no-harm gate is the one to watch, not the short-cohort gain.


    METHOD NOTE WORTH REUSING: I ran an unpreregistered control (w5b) precisely because
    the headline looked too good -- three cohorts overshot ratio 1.0, and a std-ratio
    cannot distinguish recovery from magnitude-matched noise. Adding r and MAE settled
    it in minutes. When a ratio-type metric overshoots its ideal value, that is a
    signal to test tracking, not to celebrate.'
  transfers_to:
  - h-w6-mask-weighted-pooling-restores-short-9da5b6
key_numbers: {}
observed_effects:
- delta: 0.7355
  diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_extent_variance_compression_ratio: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[h-w5-contraction-survives-into-the-91ad0b]].
External run: logs/w5_three_stage_contraction.json

## Measurements
- [[eiv_extent_variance_compression_ratio]]: before 0.0804 → after 0.8159  (Δ +0.7355, up ✓ predicted up)

## Gates
- `g1-three-stage-compression-measured`: PASS
- `g2-random-init-control`: PASS
- `g3-masked-repool-headroom`: PASS
- `g4-shuffle-control`: PASS

**Verdict:** supports

## Interpretation
The extent contraction is ARCHITECTURAL AND REVERSIBLE, and the reversal costs nothing. Short-cohort mean variance-compression ratio: naive 1440-mean 0.0804 -> trained encoder 0.1822 -> mask-weighted re-pool of the SAME FROZEN TOKENS 0.8159, against a preregistered 0.60 bar, with the shuffle control at 0.0362 and the harness reproducing W4b to five decimals. Trained and random-init encoders sit 0.0140 apart (bar 0.15), so the defect is a property of presence-blind pooling GEOMETRY, not of learned weights.

THE TRANSFERABLE FINDING: the information was never destroyed at patch construction. It is present in the tokens and destroyed at the pooling step, by a single line (adaptive_avg_pool2d over all 23 patches regardless of presence). A post-hoc re-weighting with zero training and zero new parameters recovers it -- and w5b confirms the recovery is real rather than noise amplification: within-cohort r rises 0.497 -> 0.727 on short cohorts and MAE falls 8.73 -> 5.23 bpm, with optihealth_supabase going r 0.630 -> 0.962 and MAE 7.46 -> 1.67 bpm.

WHY THIS MATTERS BEYOND ITS OWN PROBLEM, and this is the part a future session should carry: every prior state/affect null measured on short-extent cohorts through this pooling was read through an instrument compressed to 7-44% of the cohort's true between-subject spread. That does not automatically overturn those nulls -- most were measured on within-window transition rows or on lifesnaps (a LONG cohort at ratio 0.70-1.04, barely affected) -- but any null whose evaluation population was dominated by case / popane / wesad / neuro_stress must be re-read with this in mind before being cited as settled.

WHAT IT DOES NOT LICENSE. Nothing here is trained end-to-end. The supabase 1.67 bpm is a PROBE on frozen features, not an emission, and the vault's emission seed band (6.29 bpm, exceeding its own 4.02 trivial baseline) still governs any deployment claim. The production stage already sits at 0.5157, i.e. the projector's cross-attention partially compensates on its own, which RAISES the risk that an end-to-end mask-weighted arm disturbs a learned calibration -- so H-W6's dense no-harm gate is the one to watch, not the short-cohort gain.

METHOD NOTE WORTH REUSING: I ran an unpreregistered control (w5b) precisely because the headline looked too good -- three cohorts overshot ratio 1.0, and a std-ratio cannot distinguish recovery from magnitude-matched noise. Adding r and MAE settled it in minutes. When a ratio-type metric overshoots its ideal value, that is a signal to test tracking, not to celebrate.

Transfers to: [[h-w6-mask-weighted-pooling-restores-short-9da5b6]]

## Notes
ALL FOUR PREREGISTERED GATES PASSED, harness validity first. The contraction is real, architectural, and REVERSIBLE by changing the pooling denominator alone.

HARNESS VALIDITY (blocking, read first). The naive arm reproduced W4b to five decimals: case |diff| 0.000028, wesad 0.000007, lifesnaps 0.0018, pmdata 0.0014, all inside the +-0.05 band frozen pre-run. That is what licenses every other comparison here.

THE FOUR NUMBERS, short-cohort means (per-cohort tables in logs/w5_three_stage_contraction.json):
  naive 1440-mean      0.0804
  enc_trained (avg)    0.1822   <- g1 bar was <0.5; contraction SURVIVES into the representation
  enc_random (avg)     0.1682   <- g2 |diff| 0.0140 vs trained, bar <=0.15
  enc_masked           0.8159   <- g3 bar >=0.60, from FROZEN tokens with ZERO training
  production stage     0.5157
  shuffle control      0.0362   <- g4, readout manufactures no spread

WHAT EACH GATE BOUGHT.
g1: the encoder's patch geometry roughly DOUBLES the naive ratio (0.0804 -> 0.1822), so it is not a plain 1440-mean, but short cohorts still arrive at the projector holding 7-44% of their true between-subject spread. The prediction that the encoder stage would land near n_real_patches/23 was directionally right: measured patch_frac is 0.087 for case/popane and the trained ratio 0.068-0.206.
g2: trained and random-init are 0.0140 apart. The defect is ARCHITECTURAL, not learned. This is the control that makes the prescribed fix correctly targeted -- retraining alone would not remove it, and changing the denominator is the right object. It also means every prior null measured on short-extent data through this pooling was read through a 5-12x compressed instrument.
g3: THE DECISIVE RESULT. A pure post-hoc re-pool of the SAME frozen tokens, weighting each patch by its measured fraction, recovers 0.0804 -> 0.8159. The information is IN the tokens and is destroyed at the pooling step. No retraining, no new parameters.
g4: shuffle 0.0362 -- not a width artifact.

UNPREREGISTERED CONTROL RUN BECAUSE THE RESULT LOOKED TOO GOOD (scratch/w5b_recovery_is_real.py -> logs/w5b_recovery_is_real.json). Three cohorts overshot ratio 1.0 (case 1.069, wesad 0.993, wearable_exam 1.488), and a std-ratio alone cannot separate 'recovered the signal' from 'amplified noise to the right magnitude'. Measured on identical rows/folds, within-cohort Pearson r and MAE:
  SHORT mean r      avg-pool 0.497 -> masked 0.727
  SHORT mean MAE    avg-pool 8.73  -> masked 5.23 bpm  (-3.50)
  case  r 0.416 -> 0.824, MAE 8.23 -> 3.63
  wesad r 0.714 -> 0.866, MAE 6.36 -> 3.33
  popane_s3 r 0.785 -> 0.957, MAE 7.28 -> 3.42
  optihealth_supabase (DEPLOYMENT) r 0.630 -> 0.962, MAE 7.46 -> 1.67 bpm
The recovered spread genuinely tracks the subject. The overshoot is over-dispersion, and |log ratio| is the fairer summary for those cohorts (case 0.066, wesad 0.007 -- i.e. nearly exact; neuro_stress 1.140 is the genuine laggard at r 0.416).

THE SUPABASE NUMBER IS NOT A DEPLOYMENT CLAIM. 7.46 -> 1.67 bpm is a PROBE on frozen features, not an emission. The vault's own rule stands: emission MAE on supabase has a 6.29 bpm seed band that exceeds its 4.02 trivial baseline. This says the information reaches the projector intact; it says nothing about what the LM emits. That is exactly what H-W6 is for.

WHAT THIS DOES NOT SHOW. Nothing here is trained end-to-end, so no claim about hr_peak, state, or emitted text follows. It is a readout probe on frozen artifacts. The production-stage figure (0.5157) sits between enc_trained and enc_masked, so the projector's cross-attention already partially compensates -- H-W6's dense no-harm gate matters more because of that, not less.

CONSEQUENCE FOR THE QUEUE: H-W6 is FUNDED (g3 passed, its precondition). W7 and W8 preconditions on W5 are satisfied. The 'information destroyed at patch construction' branch is CLOSED -- it was destroyed at pooling.

#extent-heterogeneity #pooling #padding #wearables #denominator #state-supervision #deployment-distribution #optihealth-eiv #data-contract-violation #matched-baseline
