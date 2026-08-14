---
artifacts: []
closed: true
config_note: ''
crash_reason: ''
created_at: '2026-08-05T16:54:54+00:00'
diagnostics_measured:
- after: 0.4162
  before: -0.0482
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry: []
external_run: ''
former_ids:
- hyp-eiv-patching-is-linear-not-average-20260805-exp-2026-08-05t16-54-54
gate_results:
- evidence: 'Real trained encoder, max-pooled across patches (arm D): eiv_peak_excursion_increment_r2
    = +0.4162 (R2->max 0.9061 vs within-probe control R2(max|mean) 0.4899). Incumbent
    avg-pooled (arm C) = -0.0482. Delta +0.4644. Both preregistered conditions met
    (>+0.0090, and >incumbent+0.01) by two orders of magnitude. CAVEAT: the numeric
    prediction is confirmed but the MECHANISM I predicted is refuted by controls --
    see close notes. Also gate-reproduces-recorded-control FAILED, so absolute comparison
    to the recorded +0.0030 is downgraded to indicative.'
  gate_id: gate-peak-excursion-increment-real-encoder
  passed: true
- evidence: 'FAILED, reported rather than quietly rescaled. n=4010 matched the recorded
    population EXACTLY, but the reimplemented control R2(max | true mean alone) came
    out 0.4899 vs the recorded 0.4371 -- gap +0.0528, marginally outside the preregistered
    +/-0.05 tolerance. Consequence honoured: absolute increments from this probe are
    NOT directly comparable to the recorded +0.0030, because the control they are
    differenced against is not identical. The WITHIN-PROBE comparison (arm C vs arm
    D: same control, same split, same estimator, same n) remains valid and is what
    the conclusion rests on. Likely cause: the recorded probe''s exact feature construction
    for the raw-avg arm (channel set, presence-channel handling) is not fully specified
    in the problem record, so exact replication was not possible from the record alone.'
  gate_id: gate-reproduces-recorded-control
  passed: false
git_ref: ''
hypothesis_id: hyp-eiv-patching-is-linear-not-average-20260805
id: exp-021-hyp-eiv-patching-is-linear-f6191d
key_numbers: {}
observed_effects:
- delta: 0.4644
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_peak_excursion_increment_r2: true
provenance: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[hyp-eiv-patching-is-linear-not-average-20260805]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before -0.0482 → after 0.4162  (Δ +0.4644, up ✓ predicted up)

## Gates
- `gate-peak-excursion-increment-real-encoder`: PASS — Real trained encoder, max-pooled across patches (arm D): eiv_peak_excursion_increment_r2 = +0.4162 (R2->max 0.9061 vs within-probe control R2(max|mean) 0.4899). Incumbent avg-pooled (arm C) = -0.0482. Delta +0.4644. Both preregistered conditions met (>+0.0090, and >incumbent+0.01) by two orders of magnitude. CAVEAT: the numeric prediction is confirmed but the MECHANISM I predicted is refuted by controls -- see close notes. Also gate-reproduces-recorded-control FAILED, so absolute comparison to the recorded +0.0030 is downgraded to indicative.
- `gate-reproduces-recorded-control`: **FAIL** — FAILED, reported rather than quietly rescaled. n=4010 matched the recorded population EXACTLY, but the reimplemented control R2(max | true mean alone) came out 0.4899 vs the recorded 0.4371 -- gap +0.0528, marginally outside the preregistered +/-0.05 tolerance. Consequence honoured: absolute increments from this probe are NOT directly comparable to the recorded +0.0030, because the control they are differenced against is not identical. The WITHIN-PROBE comparison (arm C vs arm D: same control, same split, same estimator, same n) remains valid and is what the conclusion rests on. Likely cause: the recorded probe's exact feature construction for the raw-avg arm (channel set, presence-channel handling) is not fully specified in the problem record, so exact replication was not possible from the record alone.

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Notes
READ THE VERDICT WITH THIS CAVEAT: the computed verdict keys off the numeric prediction, which was met (+0.4162 vs a >+0.0090 bar). But my STATED MECHANISM IS REFUTED BY MY OWN CONTROLS. Do not credit this as support for 'the trained linear patch projection preserves within-patch information'.

ARTIFACTS: scratch/probe_e1_dual_patching.py, scratch/probe_e1_controls.py, outputs/probes/e1_dual_patching_gate.json, outputs/probes/e1_dual_patching_controls.json
CONFIG: CPU-only RidgeCV. n=4010 clean fully-observed days (coverage>0.9, true mean>20bpm), 70/30 split seed 0 (train 2807 / test 1203), RidgeCV 24 alphas logspace(-3,4), train-fit standardisation. Encoder outputs/checkpoints/12ch_v1/phase_b/sensor_modules/temporal_encoder.pt (input_dim=24, hidden 64, patch_len 8, stride 4, d_model 192). NO GPU, no training run spent.

--- THE FOUR CONTROLS (run because this was the best-looking number in the report) ---
1. SHUFFLE: arm D on permuted y_max = R2 -0.0165. Not overfitting despite 9216 features / 2807 train rows. The effect is real.
2. DIMENSION-MATCHED: arm D is 2x arm C's width. Avg-only padded to the same 9216 dims (concat with a random projection of itself) scores R2->max 0.4901 vs arm D 0.9061 -- width explains 0.1% of the gain. Not a capacity artifact.
3. RANDOM-ENCODER -- DECISIVE AGAINST MY MECHANISM: an UNTRAINED random-init encoder, identical architecture, same max-pool, scores R2->max 0.9139, i.e. SLIGHTLY BETTER than the trained 0.9061 (R2->exc 0.8571). Training contributes NOTHING. The gain belongs to the MAX OPERATOR, not to learned weights.
4. TRIVIALITY: corr(explicit raw max-over-patches on channel 0, true y_max) = +1.000000 EXACTLY. Max is associative, so max-of-within-patch-maxima IS the daily max. Arm D is close to tautological in the probe setting.

--- WHAT ACTUALLY STANDS ---
(a) The incumbent path is peak-blind, CONFIRMING the recorded conclusion: real trained encoder + avg-pool across patches gives increment -0.0482, i.e. NEGATIVE. It carries nothing about peaks above knowing the level.
(b) The recorded MECHANISM is wrong about WHERE the loss happens. It is NOT destroyed at patch construction: arm D recovers the peak from the SAME encoder outputs by changing only the pooling operator. The loss occurs at the DOWNSTREAM AVERAGING readers -- cross_modal.py:260 adaptive_avg_pool2d and correlation_head.py:107 x.mean(dim=2).
(c) CODE FACT independent of the probe: src/models/patching.py forward() is unfold(size=8, step=4) then nn.Linear(8 -> 192). There is NO within-patch mean anywhere in TemporalPatching. The problem record's 'AVERAGES 8 timesteps = 2 hours' is factually incorrect as a description of the code, and it has been steering intervention design.

--- CONSEQUENCE FOR RUNBOOK rb-e1-dual-avg-max-patching-20260805 ---
The intervention as written (add max-pooled patches UPSTREAM at patch construction) targets a stage that was never lossy. The correctly-aimed and equally cheap version is to change DOWNSTREAM pooling: add a max branch alongside adaptive_avg_pool2d in the projector and alongside x.mean(dim=2) in the correlation head. This does NOT contradict ro-sensor-slot-granularity-20260805, which swept slot COUNT while holding avg-pooling fixed; this changes the pooling OPERATOR.

--- CEILING WARNING, DO NOT OVER-READ ---
R2=0.9061 is a scale-free ridge with direct access to per-patch features. Probe R2 proves PRESENCE, never USABILITY. E0 (same day) showed hr_peak currently has NEGATIVE skill against an always-predict-median constant in BOTH coverage regimes (-5.88 bpm FULL, -6.48 bpm clean SPARSE). Recovering peak information into the representation is necessary, not sufficient. The bucket rule keys on hr_peak>=120, so a numeric gain may still not move the categorical slot at all. Also note gate-reproduces-recorded-control FAILED (0.4899 vs recorded 0.4371), so cross-probe absolute comparisons to the recorded +0.0030 are indicative only; the within-probe arm C vs arm D contrast is what carries the conclusion.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
