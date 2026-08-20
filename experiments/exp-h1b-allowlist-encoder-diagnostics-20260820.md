---
artifacts: []
closed: true
config_note: 'Measures H1b''s OWN three predicted diagnostics on the arms exp-050
  already trained. exp-050 ran exactly H1b''s design (CONTROL incumbent filters vs
  REPAIRED >=1-min-native allowlist, applied in A, B AND C, seed 42) but recorded
  only eiv_supabase_hr_raw_mae_bpm and is attached to a different hypothesis, so H1b''s
  three predictions were never scored. Two of them are cheap CPU probes on the frozen
  Phase-B encoders and both checkpoints survive: outputs/checkpoints/ab_ctrl/phase_b
  and outputs/checkpoints/ab_wl/phase_b.


  Script scratch/h1b_allowlist_encoder_diagnostics.py, readings H1-H3 committed PRE-DATA.
  Corpus ac7d2b1c. IDENTICAL rows for both arms: strict-clean pool n=4000 over 93
  subjects (coverage>0.9, HR min>0, mean>20 bpm), subject-grouped GroupKFold(5) RidgeCV,
  same seed. SINGLE-VARIABLE AUDIT RUN AND RECORDED: the two arms'' encoder_config
  blocks differ in NOTHING (verified programmatically, printed as ''NONE''), so geometry/width/patching
  are held fixed and only the training filter varies.


  SCOPE: eiv_gen_pred_bucket_entropy is an EMISSION diagnostic and is not derivable
  from frozen encoders. It is reported UNMEASURED, not passed.'
cost: null
crash_reason: ''
created_at: '2026-08-20T21:57:53+00:00'
diagnostics_measured:
- after: 0.2796
  before: 0.299
  diagnostic_id: eiv_peak_excursion_increment_r2
- after: 0.9881
  before: 0.9896
  diagnostic_id: eiv_heldout_hr_r2
do_not_retry: []
external_run: logs/h1b_allowlist_encoder_diagnostics.json
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: h1b-reopened-and-this-time-it-tests-the-98f8db
id: exp-h1b-allowlist-encoder-diagnostics-20260820
interpretation:
  at: '2026-08-20T22:02:51+00:00'
  failure_mode: null
  lesson: "H1b CALLED A NULL IN ADVANCE AND THE NULL HELD. Identical rows (n=4000,
    93 subjects, strict-clean), single-variable verified (the two arms' encoder_config
    blocks differ in nothing):\n  eiv_peak_excursion_increment_r2  ctrl +0.2990  wl
    +0.2796  delta -0.0194  vs band 0.0457 (42% of band)\n  eiv_heldout_hr_r2                ctrl
    +0.9896  wl +0.9881  delta -0.0015  vs 0.05 (3% of band)\n  shuffle control                  -0.0013
    / -0.0005 (dead, so the comparison is valid)\nBoth deltas are equivalences. The
    third predicted diagnostic (eiv_gen_pred_bucket_entropy) is an emission metric
    and is recorded UNMEASURED, not passed.\n\nTHE ALLOWLIST QUESTION IS NOW ANSWERED
    FROM THREE DIRECTIONS AND THEY AGREE: H1 (Phase-A only, refuted, -0.0708 on excursion),
    exp-050 (full A+B+C chain, emission, confounded by the new-corpus-encoder pathology
    it accidentally discovered), and this run (full chain, encoder readout, clean
    equivalence inside band). The dataset allowlist is NOT a lever on representation
    quality. H1's apparent HARM did not reproduce either -- -0.0194 here versus -0.0708
    there, and this one is inside the noise band while H1's was not.\n\nWHAT THIS
    RUN COST: zero GPU. Both checkpoints already existed from exp-050; only the probes
    were new. The generalisable move is the one the skill already names -- exp-050
    trained the arms and scored ONE diagnostic against a DIFFERENT hypothesis, leaving
    H1b's own three predictions unscored and the hypothesis stranded in `proposed`
    for twelve days. Trained artifacts are multi-question instruments: before funding
    a new arm, check whether an existing checkpoint pair already answers the question.\n\nTHE
    ENGINE BUG THIS EXPOSED, and it is the more valuable half. This experiment first
    closed as `refutes` because compute_prediction_match scored a predicted `direction:
    none` against EPS=1e-9 instead of against the diagnostic's REGISTERED noise floor.
    An equivalence claim was therefore satisfiable only by a bitwise-zero delta, so
    every honest no-harm / hygiene-rider prediction was punished and calibration()
    accrued fake misses. The floor (sigma 0.02285, from exp-e19b on 2026-08-15) predated
    this close by five days, so it was part of the committed rules and the engine
    merely failed to consult it -- a bug, not a retroactive rule change. Fixed at
    vibescience-mcp 05baf8b: equivalence is scored against k*sigma, is exempt from
    _apply_noise_floor (an equivalence win is small by construction, so demoting small
    wins would make the verdict unreachable), and blocking gates can still downgrade
    it. 16 new tests, full suite 231 passing, and a replay of all 97 closed experiments
    showing 96 byte-identical with this the only mover. Verdict corrected by documented
    surgery with backups and an audit note in the record.\n\nRULE FOR FUTURE PREREGISTRATIONS:
    a `direction: none` prediction is only meaningful if the primary diagnostic carries
    a registered noise floor. Without one it still falls back to EPS and is effectively
    unsatisfiable. Register the floor BEFORE preregistering an equivalence."
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: -0.0194
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: down
- delta: -0.0015
  diagnostic_id: eiv_heldout_hr_r2
  direction: down
parent_experiment_id: exp-050-allowlist-a-b-full-chain-a44bb0
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_pred_bucket_entropy: false
    eiv_heldout_hr_r2: true
    eiv_peak_excursion_increment_r2: true
provenance: {}
recipe_fingerprint: {}
verdict: supports
---

## Summary
Tests hypothesis [[h1b-reopened-and-this-time-it-tests-the-98f8db]].
Branches from [[exp-050-allowlist-a-b-full-chain-a44bb0]].
External run: logs/h1b_allowlist_encoder_diagnostics.json

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.299 → after 0.2796  (Δ -0.0194, down ✗ predicted none)
- [[eiv_heldout_hr_r2]]: before 0.9896 → after 0.9881  (Δ -0.0015, down ✗ predicted none)

**Verdict:** supports

## Interpretation
H1b CALLED A NULL IN ADVANCE AND THE NULL HELD. Identical rows (n=4000, 93 subjects, strict-clean), single-variable verified (the two arms' encoder_config blocks differ in nothing):
  eiv_peak_excursion_increment_r2  ctrl +0.2990  wl +0.2796  delta -0.0194  vs band 0.0457 (42% of band)
  eiv_heldout_hr_r2                ctrl +0.9896  wl +0.9881  delta -0.0015  vs 0.05 (3% of band)
  shuffle control                  -0.0013 / -0.0005 (dead, so the comparison is valid)
Both deltas are equivalences. The third predicted diagnostic (eiv_gen_pred_bucket_entropy) is an emission metric and is recorded UNMEASURED, not passed.

THE ALLOWLIST QUESTION IS NOW ANSWERED FROM THREE DIRECTIONS AND THEY AGREE: H1 (Phase-A only, refuted, -0.0708 on excursion), exp-050 (full A+B+C chain, emission, confounded by the new-corpus-encoder pathology it accidentally discovered), and this run (full chain, encoder readout, clean equivalence inside band). The dataset allowlist is NOT a lever on representation quality. H1's apparent HARM did not reproduce either -- -0.0194 here versus -0.0708 there, and this one is inside the noise band while H1's was not.

WHAT THIS RUN COST: zero GPU. Both checkpoints already existed from exp-050; only the probes were new. The generalisable move is the one the skill already names -- exp-050 trained the arms and scored ONE diagnostic against a DIFFERENT hypothesis, leaving H1b's own three predictions unscored and the hypothesis stranded in `proposed` for twelve days. Trained artifacts are multi-question instruments: before funding a new arm, check whether an existing checkpoint pair already answers the question.

THE ENGINE BUG THIS EXPOSED, and it is the more valuable half. This experiment first closed as `refutes` because compute_prediction_match scored a predicted `direction: none` against EPS=1e-9 instead of against the diagnostic's REGISTERED noise floor. An equivalence claim was therefore satisfiable only by a bitwise-zero delta, so every honest no-harm / hygiene-rider prediction was punished and calibration() accrued fake misses. The floor (sigma 0.02285, from exp-e19b on 2026-08-15) predated this close by five days, so it was part of the committed rules and the engine merely failed to consult it -- a bug, not a retroactive rule change. Fixed at vibescience-mcp 05baf8b: equivalence is scored against k*sigma, is exempt from _apply_noise_floor (an equivalence win is small by construction, so demoting small wins would make the verdict unreachable), and blocking gates can still downgrade it. 16 new tests, full suite 231 passing, and a replay of all 97 closed experiments showing 96 byte-identical with this the only mover. Verdict corrected by documented surgery with backups and an audit note in the record.

RULE FOR FUTURE PREREGISTRATIONS: a `direction: none` prediction is only meaningful if the primary diagnostic carries a registered noise floor. Without one it still falls back to EPS and is effectively unsatisfiable. Register the floor BEFORE preregistering an equivalence.

## Notes
H1b PREDICTED `none` ON ALL THREE DIAGNOSTICS AND GOT `none` ON BOTH MEASURABLE ONES. This is a preregistered null that was correctly called in advance, which is the strongest form this hypothesis could take -- its own statement said the point was that 'a MEASURED null is evidence, while an INFERRED null from data-level proxies is not'.

MEASURED, identical rows (n=4000, 93 subjects, strict-clean), single-variable verified (the two arms' encoder_config blocks differ in NOTHING):
  eiv_peak_excursion_increment_r2   ctrl +0.2990  wl +0.2796   delta -0.0194  vs recipe band 0.0457
  eiv_heldout_hr_r2                 ctrl +0.9896  wl +0.9881   delta -0.0015  vs 0.05
  shuffle control                   ctrl -0.0013  wl -0.0005   (dead, comparison valid)
Both deltas sit INSIDE their bands, so `none` is confirmed on both axes. The excursion delta is 42% of its band and the HR delta is 3% of its band -- neither is distinguishable from zero.

THIRD DIAGNOSTIC NOT MEASURED: eiv_gen_pred_bucket_entropy is an emission metric and cannot be derived from frozen encoders. Recorded as unmeasured, not passed. Anyone wanting it must run generation on both adapters.

WHAT THIS ADDS OVER THE PARENT exp-050. The parent trained these exact two arms and measured only deployment emission MAE (3.044 -> 20.28), then correctly concluded the allowlist question had been asked on a broken substrate because EVERY fresh new-corpus chain was deployment-broken. That left H1b's own encoder-side predictions unscored and the hypothesis stranded in `proposed`. This run scores them on the frozen artifacts at zero GPU cost. The two results are consistent and complementary: the allowlist moves NOTHING at the encoder representation level, while the emission-level difference the parent saw belongs to the new-corpus-encoder pathology it identified, not to the filter.

SCOPE, so this is not over-cited: this measures the allowlist's effect on ENCODER READOUT only. H1b's headline sentence is about GENERATED output, and the honest position is that the generation half remains governed by the parent's finding -- both arms were deployment-broken, so a generation comparison between them is 'which arm is less broken', not a clean test. The predecessor H1 was refuted on encoder metrics with a much larger negative (-0.0708 on excursion); this arm's -0.0194 is a third of that and inside the band, so the sharper A+B+C design did NOT reproduce H1's apparent harm either.

BOTTOM LINE FOR THE ALLOWLIST QUESTION: three independent measurements now agree that the dataset allowlist is not a lever on representation quality -- H1 (Phase-A only, refuted), exp-050 (full chain, emission, confounded by the corpus pathology), and this run (full chain, encoder readout, clean null inside band).

#dataset-whitelist #infonce #contrastive #baseline #control #epoch-resolution #wearables
