---
created_at: '2026-08-05T19:45:33+00:00'
former_ids:
- the-hr-peak-readout-is-limited-by-the-digit-string-target-encoding-not-by-the-in
gates:
- blocking: true
  description: PRIMARY. ARM BIN decoded MAE < ARM DIGIT decoded MAE (E26 free-running,
    14.263 bpm), with bootstrap CI95 (5000 resamples, paired on the same test samples)
    on the difference strictly excluding zero. Same frozen hidden state H[:,0,:],
    same emission position, same train/test split as E26. If the CI touches zero the
    result is suggestive only and the gate FAILS.
  diagnostic_id: null
  id: gate-bin-beats-digit
  mde: null
- blocking: true
  description: ARM BIN must also beat its own DONOR-PREFIX null with CI95 excluding
    zero. A bin classifier could otherwise score well by learning the marginal bin
    distribution with no per-sample conditioning at all -- which is precisely the
    failure mode that produced false passes in E25 and E26. Donor arm uses Hd[:,0,:]
    with identical K and C.
  diagnostic_id: null
  id: gate-bin-beats-donor
  mde: null
- blocking: true
  description: ARM BIN must beat always-predict-median (train median) with CI95 excluding
    zero. Non-negotiable floor for any hr_peak claim in this problem.
  diagnostic_id: null
  id: gate-bin-beats-median
  mde: null
- blocking: false
  description: 'HONESTY GUARD, non-blocking but decisive for interpretation. Report
    the QUANTIZATION FLOOR: assign each test sample to its TRUE bin and predict that
    bin''s train median. This is the best achievable MAE at the chosen K, so ARM BIN
    cannot beat it. If ARM BIN lands close to the floor, the readout is near-saturating
    the encoding and larger K is the follow-up; if it lands far above the floor while
    still beating digits, the gain is real but the encoding is not yet the binding
    constraint. A bin arm that beats digits ONLY because coarse targets are intrinsically
    easier must be caught here rather than sold as a win.'
  diagnostic_id: null
  id: gate-quantization-floor-reported
  mde: null
- blocking: false
  description: 'K and C must be selected by 5-fold CV on the TRAIN split ONLY, with
    the full grid reported. Test-set selection would inflate every arm above. Grid:
    K in {4,8,16,32}, C in {0.003,0.01,0.03,0.1}.'
  diagnostic_id: null
  id: gate-k-selected-on-train-only
  mde: null
id: the-hr-peak-readout-is-limited-by-the-0d4b48
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_digit_readout_decoded_mae
  direction: down
  magnitude_note: 'PRIMARY. Predicting ARM BIN (single-token binned target) decodes
    hr_peak at LOWER MAE than E26''s free-running multi-token digit decode of 14.263
    bpm, on the same frozen features, emission position and train/test split. Committed
    to the POSITIVE branch before running. Reference points on this test split: always-predict-median
    18.492 bpm, E26 digit free-running 14.263 bpm, E26 donor-prefix free-running 24.179
    bpm. A material improvement means CI95 of the paired (BIN - DIGIT) difference
    strictly below zero. Must be read alongside the quantization floor at the selected
    K -- a bin arm that merely exploits coarse targets is not evidence for the encoding
    hypothesis.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: confirmed
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The hr_peak readout is limited by the DIGIT-STRING TARGET ENCODING, not by the information available at the emission position. A single-token binned target -- one multinomial argmax over K quantile bins, dequantized by the fixed train-derived bin median -- will decode hr_peak with LOWER MAE than E26's free-running multi-token digit decode, on the same frozen hidden state, the same emission position and the same train/test split, with a bootstrap CI95 on the paired difference excluding zero, while also beating always-predict-median and its own donor-prefix null.

COMMITTED TO THE POSITIVE BRANCH BEFORE RUNNING. I expect this to PASS, and I am stating why so the prediction is falsifiable rather than hedged: E26 showed the readout carries magnitude but not digits, and a bin token asks for exactly magnitude and nothing else. The multi-token decode spends its error budget on independent per-position argmaxes that E26 proved are not individually recoverable.

CONSEQUENCE IF CONFIRMED: the digit encoding is a real and cheap bottleneck. A binned/ordinal hr_peak target becomes a datasets.py intervention that should be preregistered and re-ranked against rb-e4 -- and it may be strictly better value than rb-e4, which is now scoped at only ~4 bpm and still blocked on lm_head weight untying.

CONSEQUENCE IF REFUTED: encoding is NOT the lever. The ~4 bpm free-running result is then an INFORMATION ceiling at the readout rather than a parameterization artifact, rb-e4's modest prize stands as the honest expectation, and the remaining candidates are the ones that change what enters the stack (prefix basis alignment -- E20 measured cos(prefix HR direction, LM native numeric direction) = -0.015 -- or zero-init gated cross-attention). A refutation must be recorded as a rule_out against target-encoding changes so no future session retries binning.

SCOPE LIMIT STATED UP FRONT: this measures a FITTED linear readout on frozen features. It bounds what the encoding permits; it does not prove cross-entropy training would find that solution. Any downstream intervention inherits that caveat, exactly as rb-e4 does.

## Rationale
E26 measured, in lm_head's real function class, that a free-running per-digit decode reaches MAE 14.263 bpm vs always-median 18.492 (donor null 24.179, CI95 of FR-minus-median [-6.008, -2.493]). But the donor-corrected per-position analysis (scratch/probe_e26b_donor_corrected.py) found ZERO non-leading digit positions clearing both their majority baseline and their own donor null: pos1 +0.0433/+0.0800, pos2 +0.0600/-0.0233, pos3 +0.0333/+0.0267, pos4 +0.0467/-0.0433 (vs_majority / vs_donor). A value-level win with no per-position win is the exact signature of a readout recovering MAGNITUDE but not DIGITS.

That is what you would expect if the target ENCODING is the binding constraint rather than the information. Emitting '118.6' requires 5 correct independent argmaxes over a 12-way vocabulary, each conditioned on the previous commitment; emitting a single bin token requires ONE. A binned target sits squarely inside lm_head's function class and is a src/training/datasets.py slot-assembly change, not an architecture rewrite -- strictly cheaper than rb-e4's 2600-step run plus the unresolved lm_head untying.

No existing rule_out covers target encoding. ro-window-shortening, ro-sensor-slot-granularity and ro-dual-avg-max-pooling all concern the representation/upstream; ro-llm-cannot-compute-max concerns the LM's arithmetic. This is new ground and it is testable for ZERO GPU on the features E26 already saved (outputs/probes/e26_digit_ceiling_features.npz), using the SAME frozen hidden state at the SAME emission position with the SAME train/test split, which makes ARM BIN directly comparable to E26's 14.263.

CRITICAL CONTROL: a binned readout must be scored against its own QUANTIZATION FLOOR -- assign each test sample to its TRUE bin and predict that bin's train median. That is the best any readout can do at a given K, and without it a bin arm can look good purely because coarse targets are easier. K and C are selected by 5-fold CV on the TRAIN split only.

Script: scratch/probe_e27_bin_encoding.py, decision rule in its docstring, written before the run.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_digit_readout_decoded_mae]] (down).
Gate `gate-bin-beats-digit` (blocking): PRIMARY. ARM BIN decoded MAE < ARM DIGIT decoded MAE (E26 free-running, 14.263 bpm), with bootstrap CI95 (5000 resamples, paired on the same test samples) on the difference strictly excluding zero. Same frozen hidden state H[:,0,:], same emission position, same train/test split as E26. If the CI touches zero the result is suggestive only and the gate FAILS.
Gate `gate-bin-beats-donor` (blocking): ARM BIN must also beat its own DONOR-PREFIX null with CI95 excluding zero. A bin classifier could otherwise score well by learning the marginal bin distribution with no per-sample conditioning at all -- which is precisely the failure mode that produced false passes in E25 and E26. Donor arm uses Hd[:,0,:] with identical K and C.
Gate `gate-bin-beats-median` (blocking): ARM BIN must beat always-predict-median (train median) with CI95 excluding zero. Non-negotiable floor for any hr_peak claim in this problem.
Gate `gate-quantization-floor-reported` (advisory): HONESTY GUARD, non-blocking but decisive for interpretation. Report the QUANTIZATION FLOOR: assign each test sample to its TRUE bin and predict that bin's train median. This is the best achievable MAE at the chosen K, so ARM BIN cannot beat it. If ARM BIN lands close to the floor, the readout is near-saturating the encoding and larger K is the follow-up; if it lands far above the floor while still beating digits, the gain is real but the encoding is not yet the binding constraint. A bin arm that beats digits ONLY because coarse targets are intrinsically easier must be caught here rather than sold as a win.
Gate `gate-k-selected-on-train-only` (advisory): K and C must be selected by 5-fold CV on the TRAIN split ONLY, with the full grid reported. Test-set selection would inflate every arm above. Grid: K in {4,8,16,32}, C in {0.003,0.01,0.03,0.1}.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
