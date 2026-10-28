---
artifacts:
- logs/a_hidden_tower_v41_minimal.json
- logs/a_hidden_tower_v41.json
- logs/a_tower_v41.json
- logs/emission_v41rebased_medianK.json
- logs/emission_v41rebased_constrained.json
- scratch/a_hidden_tower.py
- scratch/run_track_a.sh
closed: true
config_note: 'scratch/run_track_a.sh on outputs/adapters/v41rebased_c, all rungs on
  the 128 row ids read from logs/emission_v41rebased_heldout.json itself. A1 = scratch/a_hidden_tower.py
  (RidgeCV on the last hidden state at the token that predicts hr_raw''s first digit,
  GroupKFold by subject, donor-prefix and zeroed-prefix nulls). A2/A3 = the same adapter
  re-scored with --decode medianK (K=8, T=1) and --decode constrained. TWO INSTRUMENT
  DEFECTS FOUND AND RECORDED RATHER THAN HIDDEN: (1) with the row''s OWN json_slots
  target teacher-forced, heart_rate_bucket and hr_pct precede hr_raw in the context,
  and the donor-prefix null reads 0.646 against the real prefix''s 0.558 -- the probe
  was reading the scaffold, so a --context minimal arm (''{\"hr_raw\":\"@@'' and nothing
  else) was added and is the instrument the fork is read from; (2) the first constrained-decoding
  allow-list admitted only a bare ''\"'' token, which this vocabulary does not use
  to close a JSON value, so 126/128 rows ran a digit loop to max_new_tokens -- fixed
  to admit any token containing a quote plus an 8-character release, and re-run on
  the D-a adapter.'
cost:
  gpu_hours: 0.6
  wall_hours: 0.7
crash_reason: ''
created_at: '2026-09-02T22:17:02+00:00'
diagnostics_measured:
- after: 2.331
  before: 2.843
  diagnostic_id: eiv_lm_hidden_hr_raw_ridge_mae_bpm
- after: 3.032
  before: 2.843
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry:
- 'Do not read a hidden-state probe that teacher-forces the row''s OWN json_slots
  target. heart_rate_bucket and hr_pct precede hr_raw, so the probe reads the true
  earlier slots: real 0.558 against a donor null of 0.646 and a zeroed null of 0.604.
  Any localisation probe on this target needs --context minimal (or the numfirst slot
  order), and its donor null is what tells you which you got.'
- Do not expect medianK / median-of-samples to recover the LLM's numeric gap on hr_raw
  for this recipe. It was 2.843 -> 3.032 (CI95 [-0.491, +0.110]) with hr_peak 2.017
  WORSE. The shipped scorer is ALREADY greedy (do_sample=False, emission_diversity_audit.py:275),
  so 2.843 is the argmax; exp-037's pmf-median win was on hr_peak on a 2026-08 adapter
  and does not transfer.
- Do not build a constrained-decoding allow-list from a bare '"' token id. Gemma's
  vocabulary does not close a JSON value with it, so the constraint becomes a trap
  and every row runs a digit loop to max_new_tokens (126/128 unparseable). Admit any
  token whose decoded piece CONTAINS a quote, and release the constraint after ~8
  characters of value.
- Do not launch Track D-a on the strength of the row-target hidden number. Its preregistered
  g0 fails on the corrected instrument (2.331 >= 2.3), and a Phase C started automatically
  off the stale file at 01:16 was killed on that gate's authority.
external_run: logs/a_tower_v41.json
former_ids: []
gate_results:
- evidence: 'All three rungs on the IDENTICAL 128 row ids, taken from logs/emission_v41rebased_heldout.json''s
    own per-row `idx` rather than re-drawn: attention head over the 96 soft tokens
    0.812 -> RidgeCV on the frozen residual stream 2.331 -> emitted 2.843. Trivial
    always-predict-median is 6.876 on the capture''s arithmetic and 7.447 inside the
    probe''s own GroupKFold folds (the probe''s median is refit per fold on the same
    rows); both are reported rather than the flattering one.'
  gate_id: g0
  passed: true
- evidence: 'Minimal-context probe: real prefix 2.331 vs donor-prefix null 7.645 (margin
    5.31) and zeroed-prefix null 7.738 (margin 5.41), both far beyond the 3 bpm bar;
    shuffled-target control at the trivial level. THE ROW-TARGET ARM OF THE SAME PROBE
    FAILS THIS GATE (real 0.558, donor 0.646, zeroed 0.604 -- margins under 0.1) and
    is why the gate exists: with the row''s own JSON teacher-forced, the true heart_rate_bucket
    and hr_pct precede hr_raw and the probe reads them instead of the prefix.'
  gate_id: g1
  passed: true
- evidence: 'medianK (K=8, T=1) on the unchanged adapter: 2.843 -> 3.032, paired delta
    -0.189 bpm with CI95 [-0.491, +0.110] crossing zero, better on 46.9% of rows (a
    coin flip), and hr_peak 2.017 bpm WORSE. scale 1.015, in band, so the failure
    is not a dispersion collapse. Non-blocking gate. The constrained arm could not
    be scored on this adapter: the first allow-list admitted only a bare quote token,
    which this vocabulary does not use to close a JSON value, so 126/128 rows ran
    a digit loop (''"hr_pct":"55.27788888...'') to max_new_tokens; the constraint
    was fixed (any token containing a quote, plus an 8-character release) and re-runs
    on the next adapter scored.'
  gate_id: g2
  passed: false
- evidence: 'The fork was read from the hidden-state number ONLY, and from the instrument
    whose nulls hold: 2.331 >= 2.3 -> FORWARD PASS. Reading it from the discarded
    row-target arm would have said 0.558 -> EMISSION, which is exactly the mistake
    the gate forbids -- and which the D-a driver made automatically at 01:16 off the
    stale file before the corrected number existed. That Phase C was killed at 3 minutes
    on this gate''s authority.'
  gate_id: g3
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: a-the-llm-keeps-hr-raw-in-its-residual-stream-and-loses-it-at-emission
id: exp-a-hr-tower-localisation-20260903
interpretation:
  at: '2026-09-18T11:14:24+00:00'
  failure_mode: null
  lesson: 'READ THIS BEFORE THE VERDICT STRING. The computed verdict is `supports`
    because the primary diagnostic moved in the predicted DIRECTION (hidden 2.331
    < emitted 2.843), but the hypothesis''s actual claim -- "MORE THAN HALF is lost
    at EMISSION", hidden between 1.2 and 1.7 -- is REFUTED. On the 128 split-honest
    rows the tower reads tokens 0.812 -> hidden 2.331 -> emitted 2.843, so 1.519 of
    the 2.031 bpm the LLM discards (75%) is already gone inside the frozen forward
    pass and only 0.512 is lost turning the residual stream into digits. "hidden <
    emitted" was never in question; the magnitude was, and it landed outside the committed
    band. Treat this experiment as: the LLM path loses HR at the INTERFACE, not at
    the decoder. Two consequences that cost or save GPU immediately: (1) Track D-a
    is not powered -- a perfect emission fix wins at most ~0.5 bpm against a measured
    seed floor of +/-0.416, and its own preregistered g0 (hidden <= 1.6) fails at
    2.331, which is why its Phase C was killed three minutes in; (2) the levers with
    room are the ones that change what reaches the residual stream (Track E''s distillation,
    Track G''s full fine-tune) or that stop routing numbers through the LM at all
    (Track N''s narrator, where the head''s 0.812 is the shipped number). METHOD LESSON,
    and it is the reason this ran twice: the first probe teacher-forced each row''s
    own json_slots target and read 0.558 bpm -- but its donor-prefix null read 0.646
    and its zeroed-prefix null 0.604, i.e. it was decoding the true heart_rate_bucket
    and hr_pct that sit before hr_raw in that target, not the sensor prefix. Only
    after zeroing the conditioning (''{"hr_raw":"@@'' and nothing else) do the nulls
    behave (7.645 / 7.738 against a 7.447 trivial) and the number mean anything. A
    localisation probe without a donor null is not a measurement.


    PROVENANCE CORRECTION 2026-09-18: the documented scratch/run_v41_rebased.sh writes
    split=train to YAML, but src/cli_train.py at saved commit ad78bc2082d0 forwards
    split only for C; Phase A allowed_subject_keys was not forwarded (saved allowed_subject_count=null)
    and B had no allowlist. This static evidence blocks the WHOLE-PIPELINE subject-held-out
    claim for the documented path. Stored captures/numerical comparisons are preserved;
    no historical rerun or unknown launch path is asserted. A new campaign will rebuild
    a verified train-only common upstream tower. Numerical readout-error differences
    between fitted heads are not an information-theoretic decomposition.'
  transfers_to:
  - d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss
  - n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path
  - e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling
key_numbers:
  donor_null_mae_bpm: 7.645
  emitted_greedy_mae_bpm: 2.843
  hidden_ridge_minimal_mae_bpm: 2.331
  hidden_ridge_row_target_INVALID: 0.558
  lost_at_emission_bpm: 0.512
  lost_in_forward_pass_bpm: 1.519
  medianK_mae_bpm: 3.032
  medianK_paired_delta: -0.189
  row_target_donor_null_INVALID: 0.646
  tokens_attn_head_mae_bpm: 0.812
  trivial_capture_mae_bpm: 6.876
  trivial_probe_folds_mae_bpm: 7.447
  zeroed_null_mae_bpm: 7.738
observed_effects:
- delta: -0.512
  diagnostic_id: eiv_lm_hidden_hr_raw_ridge_mae_bpm
  direction: down
- delta: 0.189
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_dense128_hr_raw_mae_bpm: false
    eiv_gen_between_sample_std: false
    eiv_lm_hidden_hr_raw_ridge_mae_bpm: true
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: supports
---

## Summary
Tests hypothesis [[a-the-llm-keeps-hr-raw-in-its-residual-stream-and-loses-it-at-emission]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.
External run: logs/a_tower_v41.json

## Measurements
- [[eiv_lm_hidden_hr_raw_ridge_mae_bpm]]: before 2.843 → after 2.331  (Δ -0.512, down ✓ predicted down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 2.843 → after 3.032  (Δ +0.189, up ✗ predicted down)

## Gates
- `g0`: PASS — All three rungs on the IDENTICAL 128 row ids, taken from logs/emission_v41rebased_heldout.json's own per-row `idx` rather than re-drawn: attention head over the 96 soft tokens 0.812 -> RidgeCV on the frozen residual stream 2.331 -> emitted 2.843. Trivial always-predict-median is 6.876 on the capture's arithmetic and 7.447 inside the probe's own GroupKFold folds (the probe's median is refit per fold on the same rows); both are reported rather than the flattering one.
- `g1`: PASS — Minimal-context probe: real prefix 2.331 vs donor-prefix null 7.645 (margin 5.31) and zeroed-prefix null 7.738 (margin 5.41), both far beyond the 3 bpm bar; shuffled-target control at the trivial level. THE ROW-TARGET ARM OF THE SAME PROBE FAILS THIS GATE (real 0.558, donor 0.646, zeroed 0.604 -- margins under 0.1) and is why the gate exists: with the row's own JSON teacher-forced, the true heart_rate_bucket and hr_pct precede hr_raw and the probe reads them instead of the prefix.
- `g2`: **FAIL** — medianK (K=8, T=1) on the unchanged adapter: 2.843 -> 3.032, paired delta -0.189 bpm with CI95 [-0.491, +0.110] crossing zero, better on 46.9% of rows (a coin flip), and hr_peak 2.017 bpm WORSE. scale 1.015, in band, so the failure is not a dispersion collapse. Non-blocking gate. The constrained arm could not be scored on this adapter: the first allow-list admitted only a bare quote token, which this vocabulary does not use to close a JSON value, so 126/128 rows ran a digit loop ('"hr_pct":"55.27788888...') to max_new_tokens; the constraint was fixed (any token containing a quote, plus an 8-character release) and re-runs on the next adapter scored.
- `g3`: PASS — The fork was read from the hidden-state number ONLY, and from the instrument whose nulls hold: 2.331 >= 2.3 -> FORWARD PASS. Reading it from the discarded row-target arm would have said 0.558 -> EMISSION, which is exactly the mistake the gate forbids -- and which the D-a driver made automatically at 01:16 off the stale file before the corrected number existed. That Phase C was killed at 3 minutes on this gate's authority.

**Verdict:** supports

## Interpretation
READ THIS BEFORE THE VERDICT STRING. The computed verdict is `supports` because the primary diagnostic moved in the predicted DIRECTION (hidden 2.331 < emitted 2.843), but the hypothesis's actual claim -- "MORE THAN HALF is lost at EMISSION", hidden between 1.2 and 1.7 -- is REFUTED. On the 128 split-honest rows the tower reads tokens 0.812 -> hidden 2.331 -> emitted 2.843, so 1.519 of the 2.031 bpm the LLM discards (75%) is already gone inside the frozen forward pass and only 0.512 is lost turning the residual stream into digits. "hidden < emitted" was never in question; the magnitude was, and it landed outside the committed band. Treat this experiment as: the LLM path loses HR at the INTERFACE, not at the decoder. Two consequences that cost or save GPU immediately: (1) Track D-a is not powered -- a perfect emission fix wins at most ~0.5 bpm against a measured seed floor of +/-0.416, and its own preregistered g0 (hidden <= 1.6) fails at 2.331, which is why its Phase C was killed three minutes in; (2) the levers with room are the ones that change what reaches the residual stream (Track E's distillation, Track G's full fine-tune) or that stop routing numbers through the LM at all (Track N's narrator, where the head's 0.812 is the shipped number). METHOD LESSON, and it is the reason this ran twice: the first probe teacher-forced each row's own json_slots target and read 0.558 bpm -- but its donor-prefix null read 0.646 and its zeroed-prefix null 0.604, i.e. it was decoding the true heart_rate_bucket and hr_pct that sit before hr_raw in that target, not the sensor prefix. Only after zeroing the conditioning ('{"hr_raw":"@@' and nothing else) do the nulls behave (7.645 / 7.738 against a 7.447 trivial) and the number mean anything. A localisation probe without a donor null is not a measurement.

PROVENANCE CORRECTION 2026-09-18: the documented scratch/run_v41_rebased.sh writes split=train to YAML, but src/cli_train.py at saved commit ad78bc2082d0 forwards split only for C; Phase A allowed_subject_keys was not forwarded (saved allowed_subject_count=null) and B had no allowlist. This static evidence blocks the WHOLE-PIPELINE subject-held-out claim for the documented path. Stored captures/numerical comparisons are preserved; no historical rerun or unknown launch path is asserted. A new campaign will rebuild a verified train-only common upstream tower. Numerical readout-error differences between fitted heads are not an information-theoretic decomposition.

Transfers to: [[d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss]], [[n-narrator-over-head-numbers-is-numerically-faithful-and-beats-the-soft-token-path]], [[e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling]]

## Key numbers
- `donor_null_mae_bpm`: 7.645
- `emitted_greedy_mae_bpm`: 2.843
- `hidden_ridge_minimal_mae_bpm`: 2.331
- `hidden_ridge_row_target_INVALID`: 0.558
- `lost_at_emission_bpm`: 0.512
- `lost_in_forward_pass_bpm`: 1.519
- `medianK_mae_bpm`: 3.032
- `medianK_paired_delta`: -0.189
- `row_target_donor_null_INVALID`: 0.646
- `tokens_attn_head_mae_bpm`: 0.812
- `trivial_capture_mae_bpm`: 6.876
- `trivial_probe_folds_mae_bpm`: 7.447
- `zeroed_null_mae_bpm`: 7.738

## Artifacts
- `logs/a_hidden_tower_v41_minimal.json`
- `logs/a_hidden_tower_v41.json`
- `logs/a_tower_v41.json`
- `logs/emission_v41rebased_medianK.json`
- `logs/emission_v41rebased_constrained.json`
- `scratch/a_hidden_tower.py`
- `scratch/run_track_a.sh`

## Do not retry
- Do not read a hidden-state probe that teacher-forces the row's OWN json_slots target. heart_rate_bucket and hr_pct precede hr_raw, so the probe reads the true earlier slots: real 0.558 against a donor null of 0.646 and a zeroed null of 0.604. Any localisation probe on this target needs --context minimal (or the numfirst slot order), and its donor null is what tells you which you got.
- Do not expect medianK / median-of-samples to recover the LLM's numeric gap on hr_raw for this recipe. It was 2.843 -> 3.032 (CI95 [-0.491, +0.110]) with hr_peak 2.017 WORSE. The shipped scorer is ALREADY greedy (do_sample=False, emission_diversity_audit.py:275), so 2.843 is the argmax; exp-037's pmf-median win was on hr_peak on a 2026-08 adapter and does not transfer.
- Do not build a constrained-decoding allow-list from a bare '"' token id. Gemma's vocabulary does not close a JSON value with it, so the constraint becomes a trap and every row runs a digit loop to max_new_tokens (126/128 unparseable). Admit any token whose decoded piece CONTAINS a quote, and release the constraint after ~8 characters of value.
- Do not launch Track D-a on the strength of the row-target hidden number. Its preregistered g0 fails on the corrected instrument (2.331 >= 2.3), and a Phase C started automatically off the stale file at 01:16 was killed on that gate's authority.

## Notes
THE TOWER, all three rungs on the same 128 split-honest rows: attention head over the 96 soft tokens 0.812 bpm -> RidgeCV on MedGemma's frozen final hidden state at the hr_raw emission token 2.331 -> what the model actually emits 2.843. Trivial 6.876 (capture) / 7.447 (probe folds).

SO THE LOSS IS 75% FORWARD PASS, 25% EMISSION: 1.519 of the 2.031 bpm the LLM discards is already gone by the time the residual stream reaches the token that must say the number, and only 0.512 more is lost turning that state into digits. The committed prediction was the opposite (1.2-1.7 bpm hidden, i.e. the stream keeps it and emission loses it), so the primary effect is refuted in magnitude and the fork it implied is refuted in direction.

WHY THIS IS NOT E36 CONTRADICTED. E36 read 1.481 bpm on 2026-08-06, but on a different adapter, a different bundle and its own strict-clean draw, and with the row's own target teacher-forced -- the same configuration that reads 0.558 here and fails the donor null. E36's number is not comparable and should not be quoted as the middle rung.

CONSEQUENCE FOR THE PROGRAMME. D-a (a better emission format) has at most ~0.5 bpm to win, against a measured seed floor of +/-0.416 -- it is not powered to show anything, and its own g0 forbids it. The interface, not the decoder, is where the LLM path loses HR, which points at Tracks E/G and at the narrator architecture rather than at another Phase C target.

#optihealth #readout #medgemma #target-style #optihealth-eiv #matched-baseline
