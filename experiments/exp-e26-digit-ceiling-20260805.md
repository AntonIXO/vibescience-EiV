---
artifacts: []
closed: true
config_note: 'SELF-CORRECTION of exp-e25. CPU-side fits on GPU-extracted frozen features;
  NO TRAINING. Adapter medgemma_12ch_e23v2, 4-bit. n=1000 (E25 used 400; n_train=280
  vs d_model=2560 was 9x underdetermined), SEED 0, strict-clean population, E24c neutral
  context. Key methodological changes vs E25: (1) readout measured in lm_head''s REAL
  function class -- per-position multinomial classifier over {''0''..''9'',''.'',END}
  + argmax, NOT scalar RidgeCV; (2) hidden states captured at EVERY emission position,
  not just the leading digit; (3) FREE-RUNNING decode with the classifier''s own digit
  fed back via KV cache, alongside the oracle-prefix teacher-forced arm which is labelled
  OPTIMISTIC; (4) logistic C chosen by CV on the TRAIN split only. Apparatus smoke-tested
  at n=60 (all arms parse 1.000). Script scratch/probe_e26_digit_ceiling.py with the
  decision rule in its docstring, committed before launch. Primary prediction committed
  to the NULL branch.'
crash_reason: ''
created_at: '2026-08-05T19:08:43+00:00'
diagnostics_measured:
- after: 14.263
  before: 18.492
  diagnostic_id: eiv_digit_readout_decoded_mae
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS, and it survives the adversarial control. Free-running decoded MAE
    14.263 bpm vs always-predict-median 18.492 bpm; bootstrap CI95 (5000 resamples)
    of the difference [-6.008, -2.493], excluding zero. Strengthened beyond the preregistered
    bar in scratch/probe_e26b_donor_corrected.py: the donor-prefix free-running decode
    is 24.179 bpm, WORSE than the median predictor (CI vs median [+3.685, +7.681]),
    and CI95 of FR-minus-donor is [-12.320, -7.519]. So the ~4.2 bpm improvement is
    per-sample information from THIS sample''s prefix, not a prefix-independent digit
    prior. This REFUTES my preregistered null prediction: a linear readout in lm_head''s
    actual function class does beat the trivial predictor at the value level.'
  gate_id: gate-fr-beats-median
  passed: true
- evidence: 'RECORDED AS FAILED. As written (majority baseline only) it passed via
    position 2 at +0.0600 over majority -- but position 2''s DONOR accuracy is 0.2867,
    EXCEEDING its real-prefix accuracy of 0.2633 (vs_donor -0.0233). A position where
    a donor prefix outperforms the true prefix cannot be carrying per-sample information,
    so that pass is an artifact of comparing against the wrong bar -- the same class
    of error as E25''s degenerate primary gate. Donor-corrected in scratch/probe_e26b_donor_corrected.py:
    ZERO non-leading positions clear BOTH bars (>+0.05 over majority AND >0 over their
    own donor). Per position (vs_majority / vs_donor): pos1 +0.0433/+0.0800, pos2
    +0.0600/-0.0233, pos3 +0.0333/+0.0267, pos4 +0.0467/-0.0433. The two positions
    with a real donor margin (1 and 3) fall short of the +0.05 majority bar; the two
    clearing the majority bar (2 and 4) have donors that beat them. Per-position digit
    accuracy is therefore NOT established, and the tenths digit in particular (pos
    4: +0.0467 majority but -0.0433 donor) shows no evidence of carrying signal. LESSON
    REPEATED FROM E25: an accuracy gate needs BOTH a majority baseline and a donor
    null; majority alone is insufficient.'
  gate_id: gate-non-leading-digit
  passed: false
- evidence: 'HONESTY GATE FIRED, with the opposite sign to the one anticipated. I
    preregistered that teacher-forcing would be OPTIMISTIC and that a TF pass with
    an FR fail must be read as refutation. The observed result is the reverse: TF
    60.873 bpm vs FR 14.263 bpm, i.e. the oracle-prefix arm is catastrophically WORSE,
    and its CI vs median is [+24.143, +62.995]. Mechanism: under teacher forcing each
    position is predicted independently from a hidden state conditioned on the TRUE
    preceding digits, so an error at the leading digit yields an unrecoverable magnitude
    error (e.g. emitting ''9'' then continuing ''9.7'' for a true 119.7). Free-running
    decode is self-consistent -- once it commits to a leading digit, subsequent states
    are conditioned on that commitment. CONSEQUENCE FOR METHOD: teacher-forced digit
    accuracy is NOT an upper bound on free-running decoded error for multi-token numerics,
    and must not be used as one. The free-running arm is the only admissible measurement.'
  gate_id: gate-tf-vs-fr-honesty
  passed: true
- evidence: PASS, decisively. Donor-prefix free-running decode 24.179 bpm, which is
    WORSE than always-predict-median 18.492 (CI95 of donor-minus-median [+3.685, +7.681],
    excluding zero and positive). The per-position classifiers did not learn a prefix-independent
    digit prior that would have inflated the real arm. This is what makes the gate-fr-beats-median
    pass trustworthy at the value level, and simultaneously what exposes the per-position
    gate failure.
  gate_id: gate-donor-null-fr
  passed: true
git_ref: ''
hypothesis_id: measured-in-lm-head-s-actual-function-b096c6
id: exp-e26-digit-ceiling-20260805
key_numbers: {}
observed_effects:
- delta: -4.229
  diagnostic_id: eiv_digit_readout_decoded_mae
  direction: down
parent_experiment_id: exp-e25-readout-ceiling-20260805
prediction_match:
  overall: false
  per_diagnostic:
    eiv_digit_readout_decoded_mae: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[measured-in-lm-head-s-actual-function-b096c6]].
Branches from [[exp-e25-readout-ceiling-20260805]].

## Measurements
- [[eiv_digit_readout_decoded_mae]]: before 18.492 → after 14.263  (Δ -4.229, down ✗ predicted none)

## Gates
- `gate-fr-beats-median`: PASS — PASS, and it survives the adversarial control. Free-running decoded MAE 14.263 bpm vs always-predict-median 18.492 bpm; bootstrap CI95 (5000 resamples) of the difference [-6.008, -2.493], excluding zero. Strengthened beyond the preregistered bar in scratch/probe_e26b_donor_corrected.py: the donor-prefix free-running decode is 24.179 bpm, WORSE than the median predictor (CI vs median [+3.685, +7.681]), and CI95 of FR-minus-donor is [-12.320, -7.519]. So the ~4.2 bpm improvement is per-sample information from THIS sample's prefix, not a prefix-independent digit prior. This REFUTES my preregistered null prediction: a linear readout in lm_head's actual function class does beat the trivial predictor at the value level.
- `gate-non-leading-digit`: **FAIL** — RECORDED AS FAILED. As written (majority baseline only) it passed via position 2 at +0.0600 over majority -- but position 2's DONOR accuracy is 0.2867, EXCEEDING its real-prefix accuracy of 0.2633 (vs_donor -0.0233). A position where a donor prefix outperforms the true prefix cannot be carrying per-sample information, so that pass is an artifact of comparing against the wrong bar -- the same class of error as E25's degenerate primary gate. Donor-corrected in scratch/probe_e26b_donor_corrected.py: ZERO non-leading positions clear BOTH bars (>+0.05 over majority AND >0 over their own donor). Per position (vs_majority / vs_donor): pos1 +0.0433/+0.0800, pos2 +0.0600/-0.0233, pos3 +0.0333/+0.0267, pos4 +0.0467/-0.0433. The two positions with a real donor margin (1 and 3) fall short of the +0.05 majority bar; the two clearing the majority bar (2 and 4) have donors that beat them. Per-position digit accuracy is therefore NOT established, and the tenths digit in particular (pos 4: +0.0467 majority but -0.0433 donor) shows no evidence of carrying signal. LESSON REPEATED FROM E25: an accuracy gate needs BOTH a majority baseline and a donor null; majority alone is insufficient.
- `gate-tf-vs-fr-honesty`: PASS — HONESTY GATE FIRED, with the opposite sign to the one anticipated. I preregistered that teacher-forcing would be OPTIMISTIC and that a TF pass with an FR fail must be read as refutation. The observed result is the reverse: TF 60.873 bpm vs FR 14.263 bpm, i.e. the oracle-prefix arm is catastrophically WORSE, and its CI vs median is [+24.143, +62.995]. Mechanism: under teacher forcing each position is predicted independently from a hidden state conditioned on the TRUE preceding digits, so an error at the leading digit yields an unrecoverable magnitude error (e.g. emitting '9' then continuing '9.7' for a true 119.7). Free-running decode is self-consistent -- once it commits to a leading digit, subsequent states are conditioned on that commitment. CONSEQUENCE FOR METHOD: teacher-forced digit accuracy is NOT an upper bound on free-running decoded error for multi-token numerics, and must not be used as one. The free-running arm is the only admissible measurement.
- `gate-donor-null-fr`: PASS — PASS, decisively. Donor-prefix free-running decode 24.179 bpm, which is WORSE than always-predict-median 18.492 (CI95 of donor-minus-median [+3.685, +7.681], excluding zero and positive). The per-position classifiers did not learn a prefix-independent digit prior that would have inflated the real arm. This is what makes the gate-fr-beats-median pass trustworthy at the value level, and simultaneously what exposes the per-position gate failure.

**Verdict:** refutes

## Notes
MY PREREGISTERED PREDICTION WAS WRONG, AND THE CORRECTION IT WAS MAKING WAS STILL WORTH RUNNING. I committed to the NULL branch: that a linear readout in lm_head's real function class would NOT beat always-predict-median. It does, by 4.229 bpm with a CI excluding zero and a donor null on the wrong side of the median predictor. The verdict is computed from that primary prediction, so it reads as a refutation of MY stated expectation -- read the gates, not the verdict label, for the substantive finding.

WHAT E26 SETTLED (the reason it was run): E25 measured rb-e4's ceiling with RidgeCV mapping h -> a SCALAR, which lm_head cannot implement. E26 re-measured in the correct class -- per-position multinomial classifier over {'0'..'9','.',END}, argmax, free-running with each predicted digit fed back through the model via KV cache. The function-class objection was legitimate and the correction was necessary; the empirical answer simply came out the other way from my prediction. E4 is NOT ruled out on capacity grounds.

WHAT PASSES: free-running decoded MAE 14.263 vs always-median 18.492, CI95 [-6.008, -2.493]. Donor-prefix free-running 24.179 -- WORSE than the median predictor (CI vs median [+3.685, +7.681]) -- and CI95 of FR-minus-donor [-12.320, -7.519]. The gain is per-sample, not a digit prior.

WHAT FAILS, AND IT MATTERS: gate-non-leading-digit is recorded FAILED after a donor correction (scratch/probe_e26b_donor_corrected.py). As written it passed via position 2 (+0.0600 over majority), but position 2's donor accuracy 0.2867 EXCEEDS its real-prefix 0.2633. Zero non-leading positions clear BOTH bars. Positions 1 and 3 have a real donor margin (+0.0800, +0.0267) but miss the +0.05 majority bar; positions 2 and 4 clear the majority bar but their donors beat them. This is the SECOND time in two experiments that an accuracy gate scored against a majority baseline alone produced a false pass -- the same error class as E25's degenerate primary. Standing rule now: every accuracy gate needs BOTH a majority baseline AND a donor null.

SO WHAT IS THE READOUT ACTUALLY DOING? The value-level win is real but the per-position digit evidence is absent, which is only consistent with the readout recovering MAGNITUDE (roughly: is this ~90, ~110, ~130) rather than precise digits. Position 0 (hundreds/leading) gains +0.0167 over majority with a +0.0367 donor margin, and position 3 similarly -- these are the magnitude-bearing positions. The tenths digit (pos 4) shows nothing: +0.0467 over majority but -0.0433 against its own donor. The honest summary is that a linear correction at lm_head buys a coarse magnitude improvement worth ~4 bpm, NOT accurate peak values.

METHODOLOGICAL FINDING, OPPOSITE IN SIGN TO WHAT I PREREGISTERED: teacher-forced decode is 60.873 bpm, catastrophically WORSE than free-running 14.263 (CI vs median [+24.143, +62.995]). I had labelled TF the OPTIMISTIC oracle-prefix arm and preregistered that a TF-pass-with-FR-fail must be read as refutation. The reverse occurred. Mechanism: under teacher forcing each position is conditioned on the TRUE preceding digits, so a leading-digit error produces an unrecoverable magnitude error ('9' then '9.7' for a true 119.7); free-running decode is self-consistent because later positions are conditioned on the model's own commitment. CONSEQUENCE: teacher-forced digit accuracy is NOT an upper bound on free-running decoded error for multi-token numerics and must never be used as one. This invalidates a common shortcut and should be applied to any future numeric-emission probe in this project.

BEARING ON rb-e4: the capacity objection raised against E25 is resolved in E4's favour -- a rank>=10 LoRA on lm_head can realise a correction worth ~4.2 bpm over the trivial predictor, verified per-sample. But the expected prize is now quantified and it is MODEST: ~4 bpm of magnitude correction, with no evidence of digit-level accuracy, against a task whose current state is -5.88 bpm skill. rb-e4 should be unblocked but re-scoped: it is a partial-credit arm, not a fix. The separate blocking design issue stands unresolved -- lm_head.weight is TIED to the input embeddings, so a naive LoRA also perturbs the embedding table for all 262145 tokens and must be untied first.

Artifacts: outputs/probes/e26_digit_ceiling.json, outputs/probes/e26_digit_ceiling_features.npz, outputs/probes/e26b_donor_corrected.json, logs/e26_ceiling.log. Scripts scratch/probe_e26_digit_ceiling.py, scratch/probe_e26b_donor_corrected.py. n=1000 -> n_train=700/n_test=300. GPU ~28 min, no training spent.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
