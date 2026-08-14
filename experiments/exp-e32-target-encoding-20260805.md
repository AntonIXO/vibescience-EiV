---
artifacts: []
closed: true
config_note: 'PURE CPU, zero GPU, ~15 s total across three probes. No model weights,
  no forward pass; MedGemma tokenizer attempted but the repo is gated (401), so the
  analysis is at CHARACTER level and that limitation is reported rather than papered
  over. Strict-clean population n=1546 for the target-distribution work (E32a/E32b)
  and the E26-E31 subsample n=1000 with the identical SEED 0 700/300 split for the
  confound audit (E32c), so the 7.605 comparison is admissible. Exact render path
  reproduced from datasets.py: round(v,1) then json.dumps. Scripts: scratch/probe_e32a_target_entropy.py,
  scratch/probe_e32b_place_alignment.py, scratch/probe_e32c_mean_confound.py. Built-in
  A/B control throughout: hr_raw (succeeds end-to-end, +4.27 bpm skill) vs hr_peak
  (collapses, -6.48 bpm) in the SAME JSON target under the SAME objective, architecture
  and prefix.'
crash_reason: ''
created_at: '2026-08-05T22:08:02+00:00'
diagnostics_measured:
- after: 7.605
  before: 18.44
  diagnostic_id: eiv_digit_readout_decoded_mae
external_run: ''
gate_results:
- evidence: 'FAILED -- MY PREDICTION WAS BACKWARDS. hr_peak''s rendered target carries
    MORE character entropy than hr_raw''s, not less: 11.3474 vs 8.4353 bits coverage-weighted
    total, a difference of +2.9121 bits in the WRONG direction for prior-domination.
    Per-position for hr_peak: pos0 0.8944, pos1 3.0514, pos2 3.3853, pos3 1.3319,
    pos4 3.3174 bits. For hr_raw: pos0 1.7992, pos1 3.3189, pos2 -0.0000, pos3 3.3172
    bits. Value distributions confirm the direction: hr_peak median 114.34, std 23.27,
    MAE-vs-median 18.703, only 3.3% of samples within 1 bpm of the median; hr_raw
    median 69.96, std 7.90, MAE-vs-median 6.512, 8.9% within 1 bpm. hr_raw is the
    MORE concentrated, MORE prior-predictable target, and hr_raw is the one that SUCCEEDS
    (+4.27 bpm skill) while hr_peak FAILS (-6.48 bpm) under the identical objective,
    architecture and prefix. A near-deterministic prior therefore cannot be what distinguishes
    them -- if anything the sign of the effect predicts the opposite outcome to the
    one observed. PRIOR-DOMINATION IS REFUTED as the discriminating mechanism.'
  gate_id: gate-peak-entropy-below-raw
  passed: false
- evidence: 'FAILED, WITH THE CONTROL CONFIRMING THE INSTRUMENT WORKS. Place-alignment
    does not help hr_peak -- it HURTS. Total per-slot mutual information with the
    underlying value: hr_peak position-aligned 5.2115 bits -> place-aligned 4.2699
    bits, delta -0.9416 bits. hr_raw 3.7626 -> 3.7626, delta +0.0000 bits exactly,
    which is the required behaviour for the control since hr_raw is already fixed-width
    and validates that the instrument is not manufacturing effects. DIFFERENTIAL GAIN
    (peak minus raw) -0.9416 bits, i.e. NEGATIVE where the hypothesis needed a large
    positive. Per-slot for hr_peak position-aligned: pos0 MI 0.7946, pos1 2.2072,
    pos2 1.4219, pos3 0.7003, pos4 0.0876. Place-aligned: hundreds 0.6848, tens 2.6301,
    ones 0.8911, tenths 0.0639. NOTE the finding that survives: hr_peak''s target
    carries MORE total per-slot MI than hr_raw''s (5.2115 vs 3.7626 bits) and still
    fails, which is another independent strike against every ''the target does not
    carry enough information'' framing. VARIABLE-WIDTH RENDERING IS REFUTED as the
    mechanism; fixed-width target proposals must stop. CAVEAT REPORTED, NOT HIDDEN:
    the MedGemma tokenizer repo is gated (401 Client Error, google/medgemma-4b-it),
    so this analysis is at CHARACTER level. It transfers directly only if Gemma tokenises
    digits individually, which was not verifiable here. The hr_raw control delta of
    exactly 0.0000 nonetheless shows the comparison is internally sound.'
  gate_id: gate-place-alignment-differential-gain
  passed: false
- evidence: 'PASSED -- MY OWN HEADLINE SURVIVES ITS AUDIT. On the identical E26-E31
    subsample and split (n=1000, 700/300, SEED 0), the true daily mean buys almost
    nothing for predicting the daily peak: always-predict-median 18.492; peak given
    TRUE daily mean 18.450 linear, 18.440 isotonic, 18.866 GBT. BEST mean-only 18.440,
    an improvement of only 0.052 bpm over the trivial baseline. Against that confound
    floor the projector output''s 7.605 is an INCREMENT OF +10.835 bpm and the emission
    position''s 10.270 is +8.170 bpm. The underlying reason is that corr(daily mean,
    daily peak) is only 0.2986 and the mean explains just 0.1028 of peak variance;
    the residual above-baseline excursion has std 21.563 bpm. So the decoded-MAE numbers
    this series is built on are NOT mean-recovery in disguise, and the capability
    hr_raw already demonstrates does not account for them. The standing increment
    rule had never been applied to the decoded-MAE series; applied now, the headline
    holds.'
  gate_id: gate-mean-confound-increment
  passed: true
git_ref: ''
hypothesis_id: the-hr-peak-failure-is-an-objective-side-defect-in-the-rendered-target-not-a-rep
id: exp-e32-target-encoding-20260805
observed_effects:
- delta: -10.835
  diagnostic_id: eiv_digit_readout_decoded_mae
  direction: down
parent_experiment_id: exp-e31-vocab-subspace-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_digit_readout_decoded_mae: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-hr-peak-failure-is-an-objective-side-defect-in-the-rendered-target-not-a-rep]].
Branches from [[exp-e31-vocab-subspace-20260805]].

## Measurements
- [[eiv_digit_readout_decoded_mae]]: before 18.44 → after 7.605  (Δ -10.835, down ✗ predicted none)

## Gates
- `gate-peak-entropy-below-raw`: **FAIL** — FAILED -- MY PREDICTION WAS BACKWARDS. hr_peak's rendered target carries MORE character entropy than hr_raw's, not less: 11.3474 vs 8.4353 bits coverage-weighted total, a difference of +2.9121 bits in the WRONG direction for prior-domination. Per-position for hr_peak: pos0 0.8944, pos1 3.0514, pos2 3.3853, pos3 1.3319, pos4 3.3174 bits. For hr_raw: pos0 1.7992, pos1 3.3189, pos2 -0.0000, pos3 3.3172 bits. Value distributions confirm the direction: hr_peak median 114.34, std 23.27, MAE-vs-median 18.703, only 3.3% of samples within 1 bpm of the median; hr_raw median 69.96, std 7.90, MAE-vs-median 6.512, 8.9% within 1 bpm. hr_raw is the MORE concentrated, MORE prior-predictable target, and hr_raw is the one that SUCCEEDS (+4.27 bpm skill) while hr_peak FAILS (-6.48 bpm) under the identical objective, architecture and prefix. A near-deterministic prior therefore cannot be what distinguishes them -- if anything the sign of the effect predicts the opposite outcome to the one observed. PRIOR-DOMINATION IS REFUTED as the discriminating mechanism.
- `gate-place-alignment-differential-gain`: **FAIL** — FAILED, WITH THE CONTROL CONFIRMING THE INSTRUMENT WORKS. Place-alignment does not help hr_peak -- it HURTS. Total per-slot mutual information with the underlying value: hr_peak position-aligned 5.2115 bits -> place-aligned 4.2699 bits, delta -0.9416 bits. hr_raw 3.7626 -> 3.7626, delta +0.0000 bits exactly, which is the required behaviour for the control since hr_raw is already fixed-width and validates that the instrument is not manufacturing effects. DIFFERENTIAL GAIN (peak minus raw) -0.9416 bits, i.e. NEGATIVE where the hypothesis needed a large positive. Per-slot for hr_peak position-aligned: pos0 MI 0.7946, pos1 2.2072, pos2 1.4219, pos3 0.7003, pos4 0.0876. Place-aligned: hundreds 0.6848, tens 2.6301, ones 0.8911, tenths 0.0639. NOTE the finding that survives: hr_peak's target carries MORE total per-slot MI than hr_raw's (5.2115 vs 3.7626 bits) and still fails, which is another independent strike against every 'the target does not carry enough information' framing. VARIABLE-WIDTH RENDERING IS REFUTED as the mechanism; fixed-width target proposals must stop. CAVEAT REPORTED, NOT HIDDEN: the MedGemma tokenizer repo is gated (401 Client Error, google/medgemma-4b-it), so this analysis is at CHARACTER level. It transfers directly only if Gemma tokenises digits individually, which was not verifiable here. The hr_raw control delta of exactly 0.0000 nonetheless shows the comparison is internally sound.
- `gate-mean-confound-increment`: PASS — PASSED -- MY OWN HEADLINE SURVIVES ITS AUDIT. On the identical E26-E31 subsample and split (n=1000, 700/300, SEED 0), the true daily mean buys almost nothing for predicting the daily peak: always-predict-median 18.492; peak given TRUE daily mean 18.450 linear, 18.440 isotonic, 18.866 GBT. BEST mean-only 18.440, an improvement of only 0.052 bpm over the trivial baseline. Against that confound floor the projector output's 7.605 is an INCREMENT OF +10.835 bpm and the emission position's 10.270 is +8.170 bpm. The underlying reason is that corr(daily mean, daily peak) is only 0.2986 and the mean explains just 0.1028 of peak variance; the residual above-baseline excursion has std 21.563 bpm. So the decoded-MAE numbers this series is built on are NOT mean-recovery in disguise, and the capability hr_raw already demonstrates does not account for them. The standing increment rule had never been applied to the decoded-MAE series; applied now, the headline holds.

**Verdict:** refutes

## Notes
TWO PREDICTIONS REFUTED, ONE AUDIT PASSED. My prior-domination story was BACKWARDS, and the built-in hr_raw control is what proved it. ~15 s of CPU, no GPU, no model.

=== 1. PRIOR-DOMINATION IS REFUTED -- MY PREDICTION HAD THE SIGN WRONG ===
I predicted hr_peak's rendered target would be LOWER-entropy than hr_raw's, so cross-entropy would be dominated by a cheap prior. The opposite is true:
  total coverage-weighted char entropy   hr_peak 11.3474   hr_raw 8.4353   (+2.9121 WRONG WAY)
  MAE-vs-own-median                      hr_peak 18.703    hr_raw 6.512
  within 1 bpm of median                 hr_peak 3.3%      hr_raw 8.9%
  std                                    hr_peak 23.27     hr_raw 7.90
hr_raw is the MORE concentrated, MORE prior-predictable target -- and hr_raw is the one that WORKS (+4.27 bpm skill) while hr_peak FAILS (-6.48 bpm) under the identical objective, architecture and prefix. The mechanism predicts the opposite of the observed outcome. Dead.

=== 2. VARIABLE-WIDTH RENDERING IS REFUTED -- AND THE CONTROL VALIDATED THE INSTRUMENT ===
E32a surfaced a real structural asymmetry: hr_raw renders at FIXED width 4 with pos2 ALWAYS '.' (entropy exactly 0.0000), while hr_peak renders at width 4 OR 5 (length entropy 0.7032) with pos2 a '.' only 19.1% of the time. In '97.1' pos1 is the ones digit; in '148.3' it is the tens digit. Place value SLIDES across samples, which for independent per-position softmaxes looked like a textbook defect.
Measured, it is not the mechanism -- place-alignment HURTS:
  hr_peak total per-slot MI   position 5.2115 -> place 4.2699   delta -0.9416 bits
  hr_raw  total per-slot MI   position 3.7626 -> place 3.7626   delta +0.0000 bits  <- CONTROL, exactly zero as required
The control landing on exactly 0.0000 is what makes the negative result trustworthy rather than an instrument artifact. FIXED-WIDTH TARGET PROPOSALS MUST STOP.
And note the finding that SURVIVES both refutations: hr_peak's target carries MORE per-slot MI than hr_raw's (5.2115 vs 3.7626 bits) and still fails. Every 'the target does not carry enough information' framing is now dead.

=== 3. THE MEAN-CONFOUND AUDIT OF MY OWN HEADLINE -- PASSED ===
hr_raw demonstrably works, so if the projector's hr_peak decodability were largely mean-recovery in disguise, the 7.605 this series rests on would be measuring a capability the system already has. The standing increment rule had never been applied to the decoded-MAE numbers. Applied now:
  always-predict-median                18.492
  peak | TRUE daily mean, linear       18.450
  peak | TRUE daily mean, isotonic     18.440   <- CONFOUND FLOOR
  peak | TRUE daily mean, GBT          18.866
The true daily mean buys 0.052 bpm over the trivial baseline. So S0's 7.605 is a genuine INCREMENT OF +10.835 bpm and the emission position's 10.270 is +8.170. corr(mean, peak) is only 0.2986; the mean explains 0.1028 of peak variance; the residual excursion has std 21.563 bpm. The headline survives its own audit.

=== 4. WHERE THIS LEAVES THE PROBLEM ===
Representation side (E24-E31): signal present, decodable at 7.605 (+10.835 over the confound floor), inside the LM's subspace, sane scale, not rank-limited, non-linear readouts lose to linear.
Target side (E32): not prior-dominated, not place-value-scrambled, carries MORE information than the slot that succeeds.
Both sides are now clean, and the -5.88 bpm emitted skill is unexplained by either.

What remains unexamined is the thing sitting between them: the TRAINING DYNAMICS. Every number in E24-E32 is either a fitted probe on frozen features or a property of the target distribution. Nothing has measured what the gradient actually does -- whether the sensor pathway receives usable gradient at the hr_peak positions, and how that compares to the hr_raw positions that demonstrably learn. hr_raw vs hr_peak remains the built-in A/B, and it is now the ONLY surviving handle: two slots, one system, opposite outcomes, with every static explanation eliminated.

=== 5. SCOPE / LIMITATION REPORTED, NOT HIDDEN ===
The MedGemma tokenizer repo is GATED (401 Client Error on google/medgemma-4b-it), so E32a/E32b are CHARACTER-level, not token-level. The loss is over tokens. These conclusions transfer directly only if Gemma tokenises digits individually, which I could NOT verify here. The hr_raw control delta of exactly 0.0000 shows the comparison is internally sound, but a token-level redo behind HF auth would strengthen it. E32c is unaffected -- it is pure value-space arithmetic.

Artifacts: outputs/probes/e32a_target_entropy.json, outputs/probes/e32b_place_alignment.json, outputs/probes/e32c_mean_confound.json. Scripts: scratch/probe_e32a_target_entropy.py, scratch/probe_e32b_place_alignment.py, scratch/probe_e32c_mean_confound.py.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
