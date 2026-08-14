---
artifacts: []
closed: true
config_note: 'CPU-side analysis on GPU-extracted frozen features; NO TRAINING. Adapter
  medgemma_12ch_e23v2 (baseline, same as the E24 line). n=400, SEED 0, strict-clean
  population (no zero-bpm present steps, mean>40, cov>0.9), neutral context identical
  to E24c arm C. Script scratch/probe_e25_readout_ceiling.py with the decision rule
  committed in its docstring before launch. Apparatus premises verified first in scratch/probe_e25_smoke.py
  (logs/e25_smoke.log): lm_head(hidden_states[-1]) == logits exactly (max abs diff
  0.0e+00); lm_head.weight TIED to input embeddings (identical data_ptr); one-token-per-digit
  tokenization with digit mass >= 0.995 at the emission positions.'
crash_reason: ''
created_at: '2026-08-05T18:33:00+00:00'
diagnostics_measured:
- after: 0.0
  before: 0.0
  diagnostic_id: eiv_readout_linear_gap
- after: 0.5283
  before: -0.0176
  diagnostic_id: eiv_peak_excursion_increment_r2
external_run: ''
gate_results:
- evidence: 'FAILED AS WRITTEN, for a reason that invalidates the metric rather than
    the hypothesis. Measured gap = +0.3250 (B linear 0.8917 - A frozen 0.5667), nominally
    clearing the >+0.15 bar. It is NOT counted as a pass: the first-digit metric is
    degenerate on this population. True peak mean 121.6 bpm => ~89% of hr_peak values
    begin with ''1'', and the best linear readout scored 0.8917, EXACTLY the majority-class
    baseline -- a constant predictor. The apparent gap exists only because the frozen
    head (0.5667) scores BELOW always-guessing-''1''. The metric therefore measures
    ''the frozen head is broken'' (which is true and already known) and carries zero
    information about linear recoverability. Design error is mine: I chose first-digit
    accuracy without checking the target''s leading-digit distribution first. Replacement
    evidence supplied on the value arm via gate-beats-median-mae.'
  gate_id: gate-readout-linear-gap
  passed: false
- evidence: 'PASS, decisively, and this now carries the verdict in place of the degenerate
    primary. Best linear readout of the frozen final hidden state: MAE 11.815 bpm
    vs always-predict-median 20.094 bpm. Strengthened beyond the preregistered bar
    in scratch/probe_e25b_increment_control.py: also beats predict-peak-from-TRUE-daily-mean
    (19.723 bpm), and both comparisons have bootstrap 95% CIs (5000 resamples) excluding
    zero -- MAE(h)-MAE(median) = [-10.619, -6.028], MAE(h)-MAE(true mean) = [-9.943,
    -5.952]. For scale, the frozen head''s own emission is MAE 30.602 with correlation
    -0.089, i.e. worse than a constant. Additionally the standing increment control:
    R2(peak|h) +0.5107 vs R2(peak|true mean) -0.0176 => increment +0.5283 against
    a +0.15 bar, so the readout reconstructs the PEAK and not the daily level.'
  gate_id: gate-beats-median-mae
  passed: true
- evidence: 'PASS. Donor-prefix null on the value arm: R2 -0.0784 (below zero), versus
    +0.5107 for the real prefix. On the degenerate first-digit metric the donor arm
    scored 0.8250, below the 0.8917 majority baseline and so also within the bar --
    but that number is uninformative for the same degeneracy reason as the primary
    gate, and the value-arm figure is the one relied upon. The linear readout is reading
    per-sample peak information from THIS sample''s prefix, not a prefix-correlated
    nuisance.'
  gate_id: gate-donor-null
  passed: true
- evidence: 'FAILED at exactly +0.0000, and this is the finding that saved the experiment
    from a false positive. Best linear readout first-digit accuracy 0.8917 minus majority-class
    baseline 0.8917 = 0.0000, against a >+0.05 bar. This non-blocking guard is what
    exposed that the blocking primary gate had passed degenerately. Recorded prominently:
    a non-blocking gate overturned the reading of a blocking one.'
  gate_id: gate-above-majority
  passed: false
git_ref: ''
hypothesis_id: the-hr-peak-readout-failure-is-recoverable-by-a-linear-correction-at-lm-head-the
id: exp-e25-readout-ceiling-20260805
observed_effects:
- delta: 0.0
  diagnostic_id: eiv_readout_linear_gap
  direction: none
- delta: 0.5459
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: exp-e24-lm-hidden-peak-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_lm_hidden_peak_r2: false
    eiv_readout_linear_gap: false
provenance: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[the-hr-peak-readout-failure-is-recoverable-by-a-linear-correction-at-lm-head-the]].
Branches from [[exp-e24-lm-hidden-peak-20260805]].

## Measurements
- [[eiv_readout_linear_gap]]: before 0.0 → after 0.0  (Δ +0, flat ✗ predicted up)
- [[eiv_peak_excursion_increment_r2]]: before -0.0176 → after 0.5283  (Δ +0.5459, up)

## Gates
- `gate-readout-linear-gap`: **FAIL** — FAILED AS WRITTEN, for a reason that invalidates the metric rather than the hypothesis. Measured gap = +0.3250 (B linear 0.8917 - A frozen 0.5667), nominally clearing the >+0.15 bar. It is NOT counted as a pass: the first-digit metric is degenerate on this population. True peak mean 121.6 bpm => ~89% of hr_peak values begin with '1', and the best linear readout scored 0.8917, EXACTLY the majority-class baseline -- a constant predictor. The apparent gap exists only because the frozen head (0.5667) scores BELOW always-guessing-'1'. The metric therefore measures 'the frozen head is broken' (which is true and already known) and carries zero information about linear recoverability. Design error is mine: I chose first-digit accuracy without checking the target's leading-digit distribution first. Replacement evidence supplied on the value arm via gate-beats-median-mae.
- `gate-beats-median-mae`: PASS — PASS, decisively, and this now carries the verdict in place of the degenerate primary. Best linear readout of the frozen final hidden state: MAE 11.815 bpm vs always-predict-median 20.094 bpm. Strengthened beyond the preregistered bar in scratch/probe_e25b_increment_control.py: also beats predict-peak-from-TRUE-daily-mean (19.723 bpm), and both comparisons have bootstrap 95% CIs (5000 resamples) excluding zero -- MAE(h)-MAE(median) = [-10.619, -6.028], MAE(h)-MAE(true mean) = [-9.943, -5.952]. For scale, the frozen head's own emission is MAE 30.602 with correlation -0.089, i.e. worse than a constant. Additionally the standing increment control: R2(peak|h) +0.5107 vs R2(peak|true mean) -0.0176 => increment +0.5283 against a +0.15 bar, so the readout reconstructs the PEAK and not the daily level.
- `gate-donor-null`: PASS — PASS. Donor-prefix null on the value arm: R2 -0.0784 (below zero), versus +0.5107 for the real prefix. On the degenerate first-digit metric the donor arm scored 0.8250, below the 0.8917 majority baseline and so also within the bar -- but that number is uninformative for the same degeneracy reason as the primary gate, and the value-arm figure is the one relied upon. The linear readout is reading per-sample peak information from THIS sample's prefix, not a prefix-correlated nuisance.
- `gate-above-majority`: **FAIL** — FAILED at exactly +0.0000, and this is the finding that saved the experiment from a false positive. Best linear readout first-digit accuracy 0.8917 minus majority-class baseline 0.8917 = 0.0000, against a >+0.05 bar. This non-blocking guard is what exposed that the blocking primary gate had passed degenerately. Recorded prominently: a non-blocking gate overturned the reading of a blocking one.

**Verdict:** inconclusive

## Notes
READ THIS BEFORE READING THE VERDICT FIELD. The computed verdict keys off the primary predicted diagnostic eiv_readout_linear_gap, which I recorded as delta 0.0 / direction 'none'. That is deliberate and correct: my preregistered primary INSTRUMENT was invalid, so the honest record is that it moved nothing. The verdict field should be read as 'the preregistered test failed', NOT as 'the readout is fine'.

WHAT FAILED (my design error). I preregistered first-digit accuracy of hr_peak as the primary metric without first checking the target's leading-digit distribution. True peak mean is 121.6 bpm on the strict-clean population, so ~89% of values begin with '1'. Measured: best linear readout 0.8917 = majority-class baseline 0.8917 EXACTLY (a constant predictor), frozen head 0.5667. The preregistered gap of +0.3250 nominally cleared the >+0.15 bar but means only 'the frozen head performs worse than always guessing 1' -- already known from E0/E23. Zero information about linear recoverability. My own NON-BLOCKING gate-above-majority caught this at exactly +0.0000; a non-blocking guard overturned the reading of a blocking gate. Generalisable lesson: an accuracy-gap gate on a skewed categorical target is a trap; always preregister the majority baseline as a companion.

WHAT THE EXPERIMENT NEVERTHELESS ESTABLISHED, on the non-degenerate value arm, with controls added after the degeneracy was found (scratch/probe_e25b_increment_control.py, same seed/split/population, CPU-only on saved features):
  - R2(hr_peak | frozen final hidden state h) = +0.5107; R2(hr_peak | TRUE daily mean) = -0.0176; INCREMENT +0.5283 vs a +0.15 bar. On strict-clean days the daily mean carries essentially nothing about the peak, so the increment is nearly the whole R2 -- this is the PEAK, not the level.
  - MAE 11.815 bpm vs always-predict-median 20.094 and vs predict-from-true-mean 19.723. Bootstrap CI95 (5000 resamples) of both MAE differences excludes zero: [-10.619, -6.028] and [-9.943, -5.952].
  - Donor-prefix null R2 -0.0784: per-sample information, not a prefix-correlated nuisance.
  - The frozen head's actual emission: MAE 30.602, correlation -0.089 -- WORSE THAN A CONSTANT. Directly observed near-constant output ('90.0' emitted against true 100.3, 118.6, 94.0).
  - hr_peak>=100 decision (what hr_label_bucket keys on): frozen 0.5917, linear readout 0.9417, majority 0.8917. The +0.05 over majority has CI95 [+0.0000, +0.1000] -- touches zero on n_test=120, so it is SUGGESTIVE ONLY and must not be quoted as a win.

BEARING ON rb-e4-lora-lm-head. The capacity argument is unaffected by the gate failure and is arithmetic, not empirical: a rank-32 LoRA on lm_head is full-rank over the 10 digit rows (rank <= min(32,10)=10), so it can realise ANY linear-in-h correction to the digit logits; the best linear readout measured here IS its capacity ceiling, and that ceiling is comfortably above the trivial predictor. So E4 is not capacity-limited. Two caveats that are NOT resolved by this experiment: (1) presence-under-a-fitted-linear-map still is not proof that gradient descent through cross-entropy on 2600 steps will FIND that map; the ceiling is an upper bound, not a forecast. (2) lm_head.weight is TIED to the input embeddings (verified, identical data_ptr), so a naive LoRA on lm_head perturbs the input embedding table as well -- E4 must untie first or adapt an added un-tied head, which is a design change to the runbook, not a config tweak.

Artifacts: outputs/probes/e25_readout_ceiling.json, outputs/probes/e25_readout_ceiling_features.npz (saved so all further controls are CPU-only), outputs/probes/e25b_increment_control.json, logs/e25_smoke.log, logs/e25_ceiling.log, logs/e25_ceiling_v2.log. No training was spent; total GPU ~18 min across two forward-pass runs.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
