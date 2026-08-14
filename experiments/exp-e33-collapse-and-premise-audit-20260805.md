---
artifacts: []
closed: true
config_note: 'PURE CPU audit of cached measurements, zero GPU, ~1 min across five
  probes. Sources: outputs/probes/e26_digit_ceiling_features.npz (keys H, Hd, Y, y_peak,
  tr(700), te(300), tf_pred, fr_pred, don_pred) and outputs/probes/e0_arm1_gates_persample_per_sample.json
  (the sidecar written by scratch/eval_e23_gates.py, which is the -5.88 bpm measurement
  path). Motivated by reading run_12ch_pipeline.sh, which documents Stage C bistability
  and a mode-collapse basin. Scripts: scratch/probe_e33a_collapse_signature.py, probe_e33b_prior_sampling.py,
  probe_e33c_alignment_audit.py, probe_e33d_premise_reconciliation.py, probe_e33e_contamination_artifact.py.
  Baseline medians recomputed WITHIN each population at every filtering step; bootstrap
  CI95 on every skill number; Gini mean difference computed in closed form via the
  sorted-array identity and cross-checked against 400 explicit permutation draws.'
crash_reason: ''
created_at: '2026-08-05T22:28:41+00:00'
diagnostics_measured:
- after: 14.263
  before: 25.609
  diagnostic_id: eiv_digit_readout_decoded_mae
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: "REFUTED -- MY PREDICTION WAS WRONG, AND THE REFUTATION IS EMPHATIC. The
    adapter is NOT a mode-collapse draw:\n  std(emitted)/std(true)                       0.9999
    \  (collapse requires ~0)\n  fraction of emissions in the documented 54-67 bpm
    band   0.0000\n  emitted: n=300, mean 116.78, std 20.758, min 80.4, median 110.6,
    max 175.2, 129 distinct values\n  true:    n=1000, mean 120.43, std 23.315, min
    74.5, median 114.3, max 185.0\nTHE SHARP TEST also refutes it: swapping in a DONOR
    prefix changes the emission substantially -- mean |real - donor| = 22.184 bpm,
    only 0.33% bit-identical, corr(real, donor) = 0.0260. Under mode collapse the
    model would ignore the prefix and real/donor would be near-identical. Donor-prefix
    MAE 24.179 vs real-prefix 14.263, so the real prefix is worth +9.916 bpm. The
    dispersion is essentially perfect and the conditioning is real. The pipeline's
    bistability is documented and genuine, but THIS adapter landed in the good basin."
  gate_id: gate-mode-collapse-signature
  passed: false
- evidence: 'REFUTED. The prior-sampling arithmetic does not describe this emission.
    If the model emitted an independent draw from the correct marginal, its MAE would
    equal the GINI MEAN DIFFERENCE of the target distribution: computed in closed
    form as 26.439 bpm on TRAIN values and 25.609 on TEST, and confirmed by 400 explicit
    permutation draws at 25.610 +/- 0.924. The actual free-running decode MAE is 14.263
    bpm -- a gap of 11.346 bpm BELOW the prior-sampling prediction. The permutation
    control agrees: shuffling the emitted values against the truth costs +9.982 bpm
    (14.263 -> 24.245), so the emissions are strongly paired to their own samples.
    The arithmetic identity itself is sound and worth keeping on record -- Gini mean
    difference >= mean absolute deviation from the median for any distribution, so
    a perfect prior-sampler is GUARANTEED negative skill -- but it is not the mechanism
    operating here.'
  gate_id: gate-prior-sampling-arithmetic
  passed: false
- evidence: "PARTIAL -- CONTAMINATION IS REAL AND SEVERE, BUT IT DOES NOT FLIP THE
    SIGN, AND I HAD PREDICTED IT WOULD. The sidecar population (n=96) is badly contaminated
    for a MAX-based target: 8 rows have a true 'daily peak' of 0.0 bpm (physiologically
    impossible for a maximum), 25.0% are below 90 bpm versus 5.0% in the strict-clean
    population, and its distribution (mean 100.07, std 38.18, median 99.96, min 0.0)
    differs sharply from strict-clean (mean 120.43, std 23.32, median 114.31, min
    74.5). Progressive decontamination with the baseline median recomputed WITHIN
    each population:\n  ALL rows (as the premise was scored)  n=96  MAE 28.13  base
    25.76  SKILL -2.37 [-7.01,+2.38]  r=0.604\n  drop true peak == 0 bpm               n=88
    \ MAE 25.08  base 18.93  SKILL -6.15 [-10.22,-1.89]  r=0.154\n  + daily mean >
    40 bpm                 n=81  MAE 23.69  base 19.06  SKILL -4.64 [-8.43,-0.65]
    \ r=0.202\n  + coverage > 0.9 (STRICT-CLEAN)       n=34  MAE 22.94  base 19.49
    \ SKILL -3.45 [-8.52,+2.02]  r=0.422\n  drop true peak < 90 bpm (aggressive)  n=72
    \ MAE 23.07  base 17.74  SKILL -5.33 [-9.27,-1.49]  r=0.084\nThe sign stays NEGATIVE
    at every filter level, so the negative-skill premise is NOT a contamination artifact
    -- my hypothesis is refuted. What IS established: the exact -5.88 / -6.48 figures
    are population-specific, the strict-clean n=34 subset gives -3.45 with a CI95
    [-8.52,+2.02] that CROSSES ZERO, and any future quotation must carry n and CI
    rather than a bare point estimate. Removing the 8 impossible zeros makes skill
    WORSE (-2.37 -> -6.15) because those rows were inflating the constant baseline
    more than the model's error."
  gate_id: gate-premise-decontamination
  passed: true
- evidence: 'PASSED, AND IT CAUGHT TWO OF MY OWN ERRORS IN THIS SAME EXPERIMENT.

    ERROR 1 -- WRONG PAIRING, RETRACTED. probe_e33a compared fr_pred against y_peak[:300],
    but fr_pred is stored over the TEST SPLIT, so the only valid truth vector is y_peak[te].
    Correct corr(emitted, true) = 0.5791; the value I reported was 0.0870. THE 0.0870
    FIGURE IS RETRACTED. E33a''s dispersion statistics stand (they are pairing-free)
    and its real-vs-donor comparison stands (both te-indexed), but every inference
    I drew from that correlation was invalid. I had begun building an ''independent
    sample from the correct marginal'' story on it.

    ERROR 2 -- PROVENANCE CONFUSION, ALSO MINE. probe_e33b/c framed a ''SIGN CONTRADICTION''
    between the cached +4.229 bpm and the -5.88 bpm premise, and recommended re-deriving
    the premise. That framing was wrong: reading scratch/probe_e26_digit_ceiling.py:200
    shows free_run is an ''Autoregressive decode driven by the fitted linear classifiers''
    -- fr_pred is a FITTED-PROBE decode, NOT the model''s native JSON emission. The
    sidecar''s pred_hr_peak IS a parsed native emission. They are different quantities
    and there is NO contradiction between them. The +4.229 bpm is the skill of a fitted
    per-digit probe (consistent with E26''s LIN/DIGIT family); the negative skill
    is the native emission. Comparing them as if they were the same manufactured a
    false crisis.

    BOTH ERRORS WERE CAUGHT BY THIS GATE BEFORE ANY CONCLUSION WAS RECORDED, WHICH
    IS EXACTLY WHY IT WAS PREREGISTERED.'
  gate_id: gate-pairing-and-provenance-audit
  passed: true
git_ref: ''
hypothesis_id: the-hr-peak-emission-failure-is-a-723466
id: exp-e33-collapse-and-premise-audit-20260805
key_numbers: {}
observed_effects:
- delta: -11.346
  diagnostic_id: eiv_digit_readout_decoded_mae
  direction: down
parent_experiment_id: exp-e32-target-encoding-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_digit_readout_decoded_mae: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-hr-peak-emission-failure-is-a-723466]].
Branches from [[exp-e32-target-encoding-20260805]].

## Measurements
- [[eiv_digit_readout_decoded_mae]]: before 25.609 → after 14.263  (Δ -11.346, down ✗ predicted none)

## Gates
- `gate-mode-collapse-signature`: **FAIL** — REFUTED -- MY PREDICTION WAS WRONG, AND THE REFUTATION IS EMPHATIC. The adapter is NOT a mode-collapse draw:
  std(emitted)/std(true)                       0.9999   (collapse requires ~0)
  fraction of emissions in the documented 54-67 bpm band   0.0000
  emitted: n=300, mean 116.78, std 20.758, min 80.4, median 110.6, max 175.2, 129 distinct values
  true:    n=1000, mean 120.43, std 23.315, min 74.5, median 114.3, max 185.0
THE SHARP TEST also refutes it: swapping in a DONOR prefix changes the emission substantially -- mean |real - donor| = 22.184 bpm, only 0.33% bit-identical, corr(real, donor) = 0.0260. Under mode collapse the model would ignore the prefix and real/donor would be near-identical. Donor-prefix MAE 24.179 vs real-prefix 14.263, so the real prefix is worth +9.916 bpm. The dispersion is essentially perfect and the conditioning is real. The pipeline's bistability is documented and genuine, but THIS adapter landed in the good basin.
- `gate-prior-sampling-arithmetic`: **FAIL** — REFUTED. The prior-sampling arithmetic does not describe this emission. If the model emitted an independent draw from the correct marginal, its MAE would equal the GINI MEAN DIFFERENCE of the target distribution: computed in closed form as 26.439 bpm on TRAIN values and 25.609 on TEST, and confirmed by 400 explicit permutation draws at 25.610 +/- 0.924. The actual free-running decode MAE is 14.263 bpm -- a gap of 11.346 bpm BELOW the prior-sampling prediction. The permutation control agrees: shuffling the emitted values against the truth costs +9.982 bpm (14.263 -> 24.245), so the emissions are strongly paired to their own samples. The arithmetic identity itself is sound and worth keeping on record -- Gini mean difference >= mean absolute deviation from the median for any distribution, so a perfect prior-sampler is GUARANTEED negative skill -- but it is not the mechanism operating here.
- `gate-premise-decontamination`: PASS — PARTIAL -- CONTAMINATION IS REAL AND SEVERE, BUT IT DOES NOT FLIP THE SIGN, AND I HAD PREDICTED IT WOULD. The sidecar population (n=96) is badly contaminated for a MAX-based target: 8 rows have a true 'daily peak' of 0.0 bpm (physiologically impossible for a maximum), 25.0% are below 90 bpm versus 5.0% in the strict-clean population, and its distribution (mean 100.07, std 38.18, median 99.96, min 0.0) differs sharply from strict-clean (mean 120.43, std 23.32, median 114.31, min 74.5). Progressive decontamination with the baseline median recomputed WITHIN each population:
  ALL rows (as the premise was scored)  n=96  MAE 28.13  base 25.76  SKILL -2.37 [-7.01,+2.38]  r=0.604
  drop true peak == 0 bpm               n=88  MAE 25.08  base 18.93  SKILL -6.15 [-10.22,-1.89]  r=0.154
  + daily mean > 40 bpm                 n=81  MAE 23.69  base 19.06  SKILL -4.64 [-8.43,-0.65]  r=0.202
  + coverage > 0.9 (STRICT-CLEAN)       n=34  MAE 22.94  base 19.49  SKILL -3.45 [-8.52,+2.02]  r=0.422
  drop true peak < 90 bpm (aggressive)  n=72  MAE 23.07  base 17.74  SKILL -5.33 [-9.27,-1.49]  r=0.084
The sign stays NEGATIVE at every filter level, so the negative-skill premise is NOT a contamination artifact -- my hypothesis is refuted. What IS established: the exact -5.88 / -6.48 figures are population-specific, the strict-clean n=34 subset gives -3.45 with a CI95 [-8.52,+2.02] that CROSSES ZERO, and any future quotation must carry n and CI rather than a bare point estimate. Removing the 8 impossible zeros makes skill WORSE (-2.37 -> -6.15) because those rows were inflating the constant baseline more than the model's error.
- `gate-pairing-and-provenance-audit`: PASS — PASSED, AND IT CAUGHT TWO OF MY OWN ERRORS IN THIS SAME EXPERIMENT.
ERROR 1 -- WRONG PAIRING, RETRACTED. probe_e33a compared fr_pred against y_peak[:300], but fr_pred is stored over the TEST SPLIT, so the only valid truth vector is y_peak[te]. Correct corr(emitted, true) = 0.5791; the value I reported was 0.0870. THE 0.0870 FIGURE IS RETRACTED. E33a's dispersion statistics stand (they are pairing-free) and its real-vs-donor comparison stands (both te-indexed), but every inference I drew from that correlation was invalid. I had begun building an 'independent sample from the correct marginal' story on it.
ERROR 2 -- PROVENANCE CONFUSION, ALSO MINE. probe_e33b/c framed a 'SIGN CONTRADICTION' between the cached +4.229 bpm and the -5.88 bpm premise, and recommended re-deriving the premise. That framing was wrong: reading scratch/probe_e26_digit_ceiling.py:200 shows free_run is an 'Autoregressive decode driven by the fitted linear classifiers' -- fr_pred is a FITTED-PROBE decode, NOT the model's native JSON emission. The sidecar's pred_hr_peak IS a parsed native emission. They are different quantities and there is NO contradiction between them. The +4.229 bpm is the skill of a fitted per-digit probe (consistent with E26's LIN/DIGIT family); the negative skill is the native emission. Comparing them as if they were the same manufactured a false crisis.
BOTH ERRORS WERE CAUGHT BY THIS GATE BEFORE ANY CONCLUSION WAS RECORDED, WHICH IS EXACTLY WHY IT WAS PREREGISTERED.

**Verdict:** refutes

## Notes
MY HYPOTHESIS REFUTED, AND THE AUDIT GATE CAUGHT TWO ERRORS I MADE INSIDE THIS SAME EXPERIMENT. ~1 min CPU, no GPU.

=== 1. THE ADAPTER IS NOT MODE-COLLAPSED -- REFUTED EMPHATICALLY ===
run_12ch_pipeline.sh documents Stage C bistability with a collapse basin emitting 'constant ~54-67 bpm regardless of input' at ~50% rate. That is the hr_peak failure signature, so I predicted the adapter was an unlucky draw. It is not:
  std(emitted)/std(true)                    0.9999   (collapse needs ~0)
  emissions in the documented 54-67 band    0.0000
  emitted n=300: mean 116.78 std 20.758 min 80.4 max 175.2, 129 distinct values
And the sharp test kills it outright: swapping in a DONOR prefix moves the emission by 22.184 bpm on average, only 0.33% identical, donor MAE 24.179 vs real 14.263. A collapsed model ignores the prefix; this one does not. The bistability is real and documented, but THIS adapter landed in the good basin. Nine experiments were NOT aimed at a training-stability artifact.

=== 2. PRIOR SAMPLING ALSO REFUTED -- BUT THE ARITHMETIC IS WORTH KEEPING ===
If a model emits an independent draw from the correct marginal, its expected MAE is the GINI MEAN DIFFERENCE of the target, which is >= the mean absolute deviation from the median FOR ANY DISTRIBUTION. Such a model is mathematically GUARANTEED negative skill. Measured here: Gini 25.609 (test) / 26.439 (train), confirmed by 400 permutation draws at 25.610 +/- 0.924, versus actual 14.263 -- an 11.346 bpm gap. Shuffling emissions against truth costs +9.982 bpm. Not prior sampling. The identity remains a useful null for any future 'why is skill negative' question.

=== 3. TWO ERRORS OF MINE, CAUGHT BY THE PREREGISTERED AUDIT GATE ===
ERROR 1, RETRACTED: probe_e33a compared fr_pred against y_peak[:300], but fr_pred is stored over the TEST SPLIT. Correct pairing y_peak[te] gives corr = 0.5791; I had reported 0.0870. I had already begun constructing an 'independent sample from the correct marginal' story on that wrong number. Dispersion stats and the real-vs-donor comparison survive (pairing-free / both te-indexed); the correlation and everything inferred from it are RETRACTED.
ERROR 2, RETRACTED: probe_e33b/c declared a 'SIGN CONTRADICTION' between the cached +4.229 bpm and the -5.88 bpm premise and recommended re-deriving the premise. That was wrong. probe_e26_digit_ceiling.py:200 documents free_run as an 'Autoregressive decode driven by the fitted linear classifiers' -- fr_pred is a FITTED-PROBE decode; the sidecar's pred_hr_peak is a parsed NATIVE emission. Different quantities, no contradiction. I manufactured a false crisis by comparing them.
This is exactly why the gate was preregistered, and it is the second time in this series that a provenance check has stopped a wrong headline.

=== 4. WHAT THE PREMISE AUDIT DID ESTABLISH ===
The -5.88 premise is NOT a contamination artifact -- the sign stays negative through every decontamination step, so my prediction there was also wrong. But the population it was measured on is badly contaminated for a MAX-based target: 8 of 96 rows have a true 'daily peak' of 0.0 bpm (impossible for a maximum) and 25% are below 90 bpm vs 5% strict-clean. Progressive filtering, baseline median recomputed within each population:
  ALL rows            n=96  SKILL -2.37 [-7.01,+2.38]  r=0.604
  drop zero peaks     n=88  SKILL -6.15 [-10.22,-1.89] r=0.154
  + mean>40           n=81  SKILL -4.64 [-8.43,-0.65]  r=0.202
  + coverage>0.9      n=34  SKILL -3.45 [-8.52,+2.02]  r=0.422
CONSEQUENCE FOR THE RECORD: the strict-clean CI CROSSES ZERO at n=34. The -5.88 / -6.48 figures are population-specific and must never again be quoted as bare point estimates -- they carry n and CI or they are not quoted. Removing the impossible zeros makes skill WORSE, because those rows inflated the constant baseline more than they inflated the model's error.

=== 5. WHERE THIS LEAVES THE PROBLEM ===
The emission is conditioned (donor gap +9.916 bpm), dispersed correctly (ratio 0.9999), correlated with truth (r 0.5791 on the fitted decode), and NOT a collapsed seed. The representation carries the signal at 7.605 bpm, +10.835 over the confound floor. The native emission is nonetheless worse than a constant on the contaminated eval population, with a CI that crosses zero once cleaned.

So the honest current statement is much weaker than the one this problem started with: the native-emission deficit is real but SMALLER and FAR LESS CERTAIN than -5.88 implies, and the eval population is not fit for scoring a max-based target. The next decision-relevant step is not another mechanism hypothesis -- it is a PROPERLY POWERED, STRICT-CLEAN EVALUATION of native emission, which is why the Stage C retrain with N_SEEDS_C=3 and the pipeline's own LM-level tripwires is now running.

=== 6. SCOPE ===
This is an audit of cached measurements, not a new measurement of the model. It cannot say what a retrained model would do. The Gini identity is exact; the dispersion and donor statistics are from the E26 cache; the premise numbers come from the sidecar's own rows with medians recomputed per population.

Artifacts: outputs/probes/e33a_collapse_signature.json, e33b_prior_sampling.json, e33c_alignment_audit.json, e33d_premise_reconciliation.json, e33e_contamination_artifact.json.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
