---
artifacts: []
closed: true
config_note: 'CPU-only, zero GPU. Reuses the frozen features E26 extracted (outputs/probes/e26_digit_ceiling_features.npz):
  SAME frozen hidden state H[:,0,:] at the SAME hr_peak emission position, SAME train/test
  split (n_train=700, n_test=300), SAME adapter medgemma_12ch_e23v2, SAME strict-clean
  population and E24c neutral context. Only the OUTPUT PARAMETERIZATION changes: E26
  decoded a multi-token digit string free-running via KV cache; E27 decodes ONE multinomial
  argmax over K quantile bins, dequantized by the fixed train-derived bin median.
  Bin edges and centers from TRAIN only, so the map is deployable. K and C selected
  by 5-fold CV on the TRAIN split only (grid K in {4,8,16,32} x C in {0.003,0.01,0.03,0.1},
  16 cells, all reported) -> selected K=4, C=0.1. Donor arm uses Hd[:,0,:] with identical
  K and C. Scripts scratch/probe_e27_bin_encoding.py (decision rule in docstring,
  written before the run) and scratch/probe_e27b_floor_decomposition.py (the error-budget
  decomposition demanded by the preregistered non-blocking floor guard). Committed
  to the POSITIVE branch before running.'
cost: null
crash_reason: ''
created_at: '2026-08-05T20:50:36+00:00'
diagnostics_measured:
- after: 12.529
  before: 14.263
  diagnostic_id: eiv_digit_readout_decoded_mae
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS. ARM BIN 12.529 bpm vs ARM DIGIT (E26 free-running) 14.263 bpm,
    paired on the same 300 test samples. Bootstrap CI95 (5000 resamples) of the difference
    [-3.280, -0.257], strictly excluding zero. Directional prediction confirmed: a
    single-token binned target does decode hr_peak better than the multi-token digit
    string from the identical frozen hidden state. Note the margin is small and the
    CI''s upper end is only -0.257, i.e. the win could be as little as a quarter of
    a bpm.'
  gate_id: gate-bin-beats-digit
  passed: true
- evidence: 'PASS, decisively. Donor-prefix bin decode 25.018 bpm vs real-prefix 12.529
    bpm; CI95 [-14.836, -10.091]. The donor arm is also far WORSE than always-predict-median
    (18.492), so the bin classifier did not learn a prefix-independent marginal bin
    distribution. Per-K bin accuracy confirms this independently: K=4 real 0.5800
    vs donor 0.2567 (+0.3233) and vs majority 0.2600 (+0.3200) -- clearing BOTH bars,
    unlike every non-leading digit position in E26.'
  gate_id: gate-bin-beats-donor
  passed: true
- evidence: PASS. ARM BIN 12.529 bpm vs always-predict-median 18.492 bpm, CI95 [-7.793,
    -4.097], excluding zero. The bin readout beats the trivial predictor by 5.964
    bpm.
  gate_id: gate-bin-beats-median
  passed: true
- evidence: 'REPORTED, AND IT OVERTURNS THE READING OF THE THREE BLOCKING PASSES ABOVE.
    K=4 quantization floor (assign each test sample its TRUE bin, predict that bin''s
    train median) = 5.694 bpm. ARM BIN achieved 12.529 -- 6.835 bpm ABOVE its own
    encoding limit. Error budget over the 12.799 bpm headroom from always-median (18.492)
    to floor (5.694): the readout REALISES 5.964 bpm (46.6%) and LOSES 6.835 bpm (53.4%)
    to its own errors at fixed encoding. The portion attributable to the encoding
    change itself is just 1.734 bpm = 13.6% of the headroom. By this gate''s own preregistered
    text -- ''if it lands far above the floor while still beating digits, the gain
    is real but the encoding is not yet the binding constraint'' -- ENCODING IS NOT
    THE BINDING CONSTRAINT. More than half the recoverable error is readout error
    that a finer or coarser target parameterization cannot touch.


    PER-K EVIDENCE THAT FINER BINS DO NOT HELP: floor falls monotonically (K=4 5.694
    -> K=8 2.730 -> K=16 1.594 -> K=32 0.862 bpm) while achieved accuracy collapses
    (0.5800 -> 0.3467 -> 0.1300 -> 0.0800) and the margin over majority shrinks (+0.3200
    -> +0.1967 -> +0.0333 -> +0.0200). CV MAE on the train split degrades monotonically
    with K (best cells: K=4 12.879, K=8 13.222, K=16 13.717, K=32 14.042), which is
    why CV selected the COARSEST grid point. The readout can resolve roughly quartile-level
    magnitude and no more -- consistent with E26''s finding that it carries magnitude
    but not digits.'
  gate_id: gate-quantization-floor-reported
  passed: true
- evidence: 'PASS. K and C were selected by 5-fold CV on the TRAIN split only; the
    test split was never touched during selection. Full 16-cell grid reported in outputs/probes/e27_bin_encoding.json.
    Selected K=4, C=0.1 at CV MAE 12.879. Grid was monotone in K at every C, so the
    selection is not a knife-edge: K=4 {12.879..13.131}, K=8 {13.222..13.670}, K=16
    {13.717..14.426}, K=32 {14.042..15.120}.'
  gate_id: gate-k-selected-on-train-only
  passed: true
git_ref: ''
hypothesis_id: the-hr-peak-readout-is-limited-by-the-0d4b48
id: exp-e27-bin-encoding-20260805
interpretation:
  at: '2026-08-18T21:08:42+00:00'
  failure_mode: null
  lesson: 'All five gates pass and the framing still does not survive: a single-token
    K=4 quantile-bin target does decode hr_peak better than E26''s multi-token digit
    string from the IDENTICAL frozen hidden state (12.529 vs 14.263 bpm, CI95 [-3.280,-0.257];
    vs always-median 18.492, CI95 [-7.793,-4.097]; donor 25.018, CI95 [-14.836,-10.091];
    K=4 bin accuracy 0.5800 vs majority 0.2600 and donor 0.2567, clearing both bars
    that every non-leading digit position in E26 failed) — but the preregistered floor
    guard ELIMINATES target encoding as the binding constraint. Of the 12.799 bpm
    headroom between always-median (18.492) and the K=4 quantization floor (5.694),
    the readout realises 5.964 bpm (46.6%) and loses 6.835 (53.4%) at fixed encoding,
    with only 1.734 bpm (13.6%) attributable to the encoding change itself. Do not
    ''just use more bins'': floors fall (K=4 5.694 -> K=8 2.730 -> K=16 1.594 -> K=32
    0.862) while achieved accuracy collapses (0.5800 -> 0.3467 -> 0.1300 -> 0.0800)
    and train-split CV MAE degrades monotonically (12.879 -> 13.222 -> 13.717 -> 14.042),
    which is why CV picked the coarsest grid point — the readout resolves quartile-level
    magnitude and no more. Note that exp-e28 later withdrew this experiment''s framing
    entirely: plain LIN_SCALAR Ridge (untested here) reaches 10.270 bpm, beating this
    bin arm CI95 [-3.310,-1.185], so the axis that mattered was scalar-vs-token output,
    not digits-vs-bins; sell a binned-target change as ~1.7 bpm over the digit target,
    never as 5.964.'
  transfers_to:
  - measured-in-lm-head-s-actual-function-b096c6
  - the-hr-peak-readout-cap-is-an-a61025
  - e11-a-linear-scalar-regression-head-127ffe
key_numbers: {}
observed_effects:
- delta: -1.734
  diagnostic_id: eiv_digit_readout_decoded_mae
  direction: down
parent_experiment_id: exp-e26-digit-ceiling-20260805
prediction_match:
  overall: true
  per_diagnostic:
    eiv_digit_readout_decoded_mae: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-hr-peak-readout-is-limited-by-the-0d4b48]].
Branches from [[exp-e26-digit-ceiling-20260805]].

## Measurements
- [[eiv_digit_readout_decoded_mae]]: before 14.263 → after 12.529  (Δ -1.734, down ✓ predicted down)

## Gates
- `gate-bin-beats-digit`: PASS — PASS. ARM BIN 12.529 bpm vs ARM DIGIT (E26 free-running) 14.263 bpm, paired on the same 300 test samples. Bootstrap CI95 (5000 resamples) of the difference [-3.280, -0.257], strictly excluding zero. Directional prediction confirmed: a single-token binned target does decode hr_peak better than the multi-token digit string from the identical frozen hidden state. Note the margin is small and the CI's upper end is only -0.257, i.e. the win could be as little as a quarter of a bpm.
- `gate-bin-beats-donor`: PASS — PASS, decisively. Donor-prefix bin decode 25.018 bpm vs real-prefix 12.529 bpm; CI95 [-14.836, -10.091]. The donor arm is also far WORSE than always-predict-median (18.492), so the bin classifier did not learn a prefix-independent marginal bin distribution. Per-K bin accuracy confirms this independently: K=4 real 0.5800 vs donor 0.2567 (+0.3233) and vs majority 0.2600 (+0.3200) -- clearing BOTH bars, unlike every non-leading digit position in E26.
- `gate-bin-beats-median`: PASS — PASS. ARM BIN 12.529 bpm vs always-predict-median 18.492 bpm, CI95 [-7.793, -4.097], excluding zero. The bin readout beats the trivial predictor by 5.964 bpm.
- `gate-quantization-floor-reported`: PASS — REPORTED, AND IT OVERTURNS THE READING OF THE THREE BLOCKING PASSES ABOVE. K=4 quantization floor (assign each test sample its TRUE bin, predict that bin's train median) = 5.694 bpm. ARM BIN achieved 12.529 -- 6.835 bpm ABOVE its own encoding limit. Error budget over the 12.799 bpm headroom from always-median (18.492) to floor (5.694): the readout REALISES 5.964 bpm (46.6%) and LOSES 6.835 bpm (53.4%) to its own errors at fixed encoding. The portion attributable to the encoding change itself is just 1.734 bpm = 13.6% of the headroom. By this gate's own preregistered text -- 'if it lands far above the floor while still beating digits, the gain is real but the encoding is not yet the binding constraint' -- ENCODING IS NOT THE BINDING CONSTRAINT. More than half the recoverable error is readout error that a finer or coarser target parameterization cannot touch.

PER-K EVIDENCE THAT FINER BINS DO NOT HELP: floor falls monotonically (K=4 5.694 -> K=8 2.730 -> K=16 1.594 -> K=32 0.862 bpm) while achieved accuracy collapses (0.5800 -> 0.3467 -> 0.1300 -> 0.0800) and the margin over majority shrinks (+0.3200 -> +0.1967 -> +0.0333 -> +0.0200). CV MAE on the train split degrades monotonically with K (best cells: K=4 12.879, K=8 13.222, K=16 13.717, K=32 14.042), which is why CV selected the COARSEST grid point. The readout can resolve roughly quartile-level magnitude and no more -- consistent with E26's finding that it carries magnitude but not digits.
- `gate-k-selected-on-train-only`: PASS — PASS. K and C were selected by 5-fold CV on the TRAIN split only; the test split was never touched during selection. Full 16-cell grid reported in outputs/probes/e27_bin_encoding.json. Selected K=4, C=0.1 at CV MAE 12.879. Grid was monotone in K at every C, so the selection is not a knife-edge: K=4 {12.879..13.131}, K=8 {13.222..13.670}, K=16 {13.717..14.426}, K=32 {14.042..15.120}.

**Verdict:** supports

## Interpretation
All five gates pass and the framing still does not survive: a single-token K=4 quantile-bin target does decode hr_peak better than E26's multi-token digit string from the IDENTICAL frozen hidden state (12.529 vs 14.263 bpm, CI95 [-3.280,-0.257]; vs always-median 18.492, CI95 [-7.793,-4.097]; donor 25.018, CI95 [-14.836,-10.091]; K=4 bin accuracy 0.5800 vs majority 0.2600 and donor 0.2567, clearing both bars that every non-leading digit position in E26 failed) — but the preregistered floor guard ELIMINATES target encoding as the binding constraint. Of the 12.799 bpm headroom between always-median (18.492) and the K=4 quantization floor (5.694), the readout realises 5.964 bpm (46.6%) and loses 6.835 (53.4%) at fixed encoding, with only 1.734 bpm (13.6%) attributable to the encoding change itself. Do not 'just use more bins': floors fall (K=4 5.694 -> K=8 2.730 -> K=16 1.594 -> K=32 0.862) while achieved accuracy collapses (0.5800 -> 0.3467 -> 0.1300 -> 0.0800) and train-split CV MAE degrades monotonically (12.879 -> 13.222 -> 13.717 -> 14.042), which is why CV picked the coarsest grid point — the readout resolves quartile-level magnitude and no more. Note that exp-e28 later withdrew this experiment's framing entirely: plain LIN_SCALAR Ridge (untested here) reaches 10.270 bpm, beating this bin arm CI95 [-3.310,-1.185], so the axis that mattered was scalar-vs-token output, not digits-vs-bins; sell a binned-target change as ~1.7 bpm over the digit target, never as 5.964.

Transfers to: [[measured-in-lm-head-s-actual-function-b096c6]], [[the-hr-peak-readout-cap-is-an-a61025]], [[e11-a-linear-scalar-regression-head-127ffe]]

## Notes
ALL FIVE GATES PASS AND THE HEADLINE FRAMING STILL DOES NOT SURVIVE. The verdict will read 'supports' because the primary directional prediction (bin MAE < digit MAE) was correct. Do not stop there -- the non-blocking floor guard I preregistered is what actually decides how this should be used, and it says the encoding is NOT the binding constraint.

THE NUMBERS. Same frozen hidden state, same emission position, same split as E26; only the output parameterization changed.
  always-predict-median        18.492 bpm
  ARM DIGIT (E26 free-running) 14.263 bpm
  ARM BIN   (K=4, 1 argmax)    12.529 bpm
  QUANTIZATION FLOOR (K=4)      5.694 bpm
  ARM BIN-D (donor null)       25.018 bpm
CI95: BIN-DIGIT [-3.280, -0.257]; BIN-median [-7.793, -4.097]; BIN-donor [-14.836, -10.091].

THE DECOMPOSITION THAT REFRAMES IT (scratch/probe_e27b_floor_decomposition.py). Headroom from always-median to the K=4 floor is 12.799 bpm. The readout realises 5.964 (46.6%) and loses 6.835 (53.4%) to its own error at FIXED encoding. The share attributable to the encoding change is 1.734 bpm = 13.6%. So switching from a digit string to a bin token buys about an eighth of what is available, while more than half the recoverable error sits in the readout and is untouched by any target parameterization. My hypothesis said the digit encoding was the LIMITING factor; it is a real but MINOR factor.

WHY FINER BINS ARE NOT THE ANSWER (the obvious follow-up, pre-empted). As K rises the floor drops but achieved accuracy collapses faster: K=4 floor 5.694 acc 0.5800; K=8 2.730 / 0.3467; K=16 1.594 / 0.1300; K=32 0.862 / 0.0800. Margin over majority shrinks +0.3200 -> +0.1967 -> +0.0333 -> +0.0200. Train-split CV MAE degrades monotonically in K, which is why CV picked the COARSEST point in the grid. The readout resolves roughly quartile-level magnitude and no more. Anyone tempted to 'just use more bins' should read this row of the table first.

WHAT E27 DID ESTABLISH THAT E26 COULD NOT. The bin arm clears BOTH bars that every non-leading digit position failed: K=4 accuracy 0.5800 vs majority 0.2600 (+0.3200) AND vs donor 0.2567 (+0.3233). E26's per-position table had zero non-leading positions clearing both. So the magnitude signal is genuinely per-sample and genuinely decodable -- it was the multi-token digit parameterization that was hiding it, exactly as predicted. The prediction was right about MECHANISM and wrong about MAGNITUDE.

CONSEQUENCE FOR THE PROBLEM. Combined with E24 (peak present at the emitting token, R2 0.6850, donor null -0.0014) and E26, the picture is now: the information arrives, a linear readout can extract COARSE MAGNITUDE from it (quartile-level, ~6 bpm of the ~12.8 available), and neither the digit encoding nor bin granularity is what caps it. The cap is the linear readout itself. That is an argument for a NON-LINEAR head (a small MLP regression head on the final hidden state, which is also outside lm_head's function class and outside rb-e4's reach) rather than for another target-encoding change or another LoRA target list.

CONSEQUENCE FOR rb-e4. Unchanged and still modest. E27 does not raise rb-e4's ~4 bpm expected prize; if anything it shows a cheaper single-token route to a similar-sized gain (5.964 bpm over median) without touching lm_head or its embedding tying. rb-e4 remains queued at priority 15 with the untying issue unresolved.

RECORD-KEEPING. A binned-target intervention should NOT be sold as a fix on this evidence. If a future session wants to implement one in src/training/datasets.py, the honest expected value is ~1.7 bpm over the current digit target, not the 5.964 bpm figure -- that larger number is measured against always-median, which is a different comparison. State which baseline is in play every time.

Artifacts: outputs/probes/e27_bin_encoding.json, outputs/probes/e27b_floor_decomposition.json, logs/e27_bin.log, logs/e27b_floor.log. Scripts scratch/probe_e27_bin_encoding.py, scratch/probe_e27b_floor_decomposition.py. Zero GPU -- entirely on E26's cached features.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
