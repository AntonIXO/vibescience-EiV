---
artifacts:
- outputs/adapters/v36labels_c
- logs/v36_driver.log
- logs/emission_v36labels_supa_summary.json
- logs/emission_v36labels_dense_summary.json
- scratch/_arm_rescore.py
- scratch/_verify_h5_caption.py
- scratch/_label_coverage_audit.py
closed: true
config_note: 'v36labels = v33dispersion + exactly one key: caption_style hr_dispersion
  -> hr_dispersion_labels (verified one-variable pre-flight). Appends the continuous/ordinal
  labels the window actually carries (stress, stress VAS, valence, arousal, mood,
  fatigue, readiness, sleep quality, sleep hours, STAI, PANAS, workload), capped at
  6 clauses; categorical codes excluded. Chain A->B@30->band04 C, driver scratch/run_v36_labels.sh,
  unit eiv-v36. Pre-flight scratch/_verify_h5_caption.py on 1200 windows: hr_dispersion
  unchanged 1200/1200, unlabelled windows byte-identical to v33 639/639, 561/1200
  gain clauses, 0/1200 leak a scored HR target (unit-aware), max 331 chars.'
cost:
  gpu_hours: 1.55
  wall_hours: 0.0
crash_reason: ''
created_at: '2026-09-01T07:05:53+00:00'
diagnostics_measured:
- after: 1.862
  before: 3.03
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
- after: 0.516
  before: 0.287
  diagnostic_id: eiv_gen_peak_corr
- after: 4.017
  before: 2.851
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
- after: 9.711
  before: 8.599
  diagnostic_id: eiv_effective_rank
do_not_retry:
- 'Do NOT add supervised heads for the orphan label channels. The label-coverage audit
  measured supply by WINDOWS (rows are the wrong denominator -- a continuously-sampled
  dial puts 149,098 rows into 486 windows): the largest orphan reaches 685 windows
  against 5,331 already covered, while v22/v29 measured -1.50/-1.816 eff_rank for
  adding heads and v31/v32 measured deployment destruction for detaching them. The
  caption is the working channel for labels.'
- 'Do NOT read any driver''s own HONEST RESCORE block for v36 or later arms: scratch/_v35_rescore.py
  had the arm name HARDCODED to ''v35wide'', and the v36 driver was sed-generated
  from the v35 one, so it rescored v35wide and printed a table that read as v36''s
  result. Fixed by making the arm a CLI argument (renamed scratch/_arm_rescore.py
  --arm X --vs A,B). Any v36 number quoted from a driver log before 2026-09-01 10:05
  is v35''s.'
- Do NOT promote v36labels on the supabase result alone. Dense hr_raw regresses to
  4.017 vs the incumbent 2.851 (CI [+0.360,+2.055], demonstrated) with std_ratio drifting
  1.11 -> 1.32 while corr stays 0.844 -- the ranking-survives-calibration-dies signature.
  A deployment decision needs the dense draw priced too.
- Do NOT attribute v36's JSON fix to the --stop-on-brace decoding change. v36 was
  captured BEFORE that flag existed; it closed 128/128 natively from the caption change
  alone.
external_run: ''
former_ids: []
gate_results: []
git_ref: 398bc46
hypothesis_id: h4-caption-content-not-architecture-the-3306b2
id: exp-v36-labels-in-caption-20260901
interpretation:
  at: '2026-09-01T07:06:46+00:00'
  failure_mode: recipe_conditional
  lesson: 'LABELS BELONG IN THE CAPTION, NOT IN NEW HEADS. Stating a window''s own
    labels as text clauses produced the campaign''s best supabase numbers -- hr_raw
    1.862 (incumbent 3.030, CI[-1.496,-0.859] vs incumbent and CI[-0.525,-0.036] vs
    v33, both excluding zero) and hr_peak skill +1.353 (incumbent -4.422) -- with
    one config key and no new supervised head. This is the third consecutive confirmation
    that the text VIEW, not the loss architecture, is the lever on this pipeline.
    It also settles the goal question ''use all labels in all three phases'': the
    head route is closed by four arms, and the audit showed the orphan channels are
    small when counted by windows rather than rows, so the caption is the route.


    SECOND, LARGER FINDING, UNPLANNED: v36 closed its JSON on 128/128 dense rows against
    v33''s 81 and v34''s 88 -- the termination defect that blocked the entire caption
    branch simply vanished, and it was captured BEFORE the --stop-on-brace fix existed.
    Working hypothesis to test next: the label clauses lengthen and diversify the
    Phase-A text view, and the looping tail was the low-entropy ending a monotonous
    caption distribution invites. Not yet demonstrated.


    THE COST IS REAL AND MUST BE PRICED: dense hr_raw regresses to 4.017 vs 2.851
    (CI[+0.360,+2.055]) with std_ratio 1.11 -> 1.32 at corr 0.844 -- the ranking-survives/calibration-dies
    signature again. So v36 is a candidate, not a promotion: the incumbent stays until
    a second seed prices the dense side against the 3.19 bpm seed band.


    PROCESS LESSON WORTH MORE THAN THE ARM: the driver''s rescore block had the arm
    name HARDCODED, and the v36 driver was sed-generated from the v35 one, so it silently
    rescored v35wide and printed a table that read as v36''s result. A script that
    cannot name its own subject will eventually be copied onto a different subject
    and lie. It is now a CLI argument.'
  transfers_to:
  - the-hr-peak-native-emission-deficit-264f4f
  - full-utilisation-at-the-safe-dose-the-v29-chain-616efb
key_numbers:
  dense_hr_peak_mae: 15.751
  dense_hr_raw_ci_hi_vs_v28: 2.055
  dense_hr_raw_ci_lo_vs_v28: 0.36
  dense_hr_raw_corr: 0.844
  dense_hr_raw_mae: 4.017
  dense_hr_raw_std_ratio: 1.32
  dense_parse_native: 128.0
  dense_parse_native_v33: 81.0
  eff_rank: 9.711
  supa_hr_peak_corr: 0.516
  supa_hr_peak_mae: 16.057
  supa_hr_peak_skill: 1.353
  supa_hr_raw_ci_hi_vs_v33: -0.036
  supa_hr_raw_ci_lo_vs_v33: -0.525
  supa_hr_raw_corr: 0.893
  supa_hr_raw_mae: 1.862
  supa_hr_raw_skill: 2.093
observed_effects:
- delta: -1.168
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
- delta: 0.229
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: 1.166
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
- delta: 1.112
  diagnostic_id: eiv_effective_rank
  direction: up
parent_experiment_id: exp-v34-nopeak-decomposition-20260901
prediction_match:
  overall: false
  per_diagnostic:
    eiv_heldout_hr_r2: false
    eiv_peak_excursion_increment_r2: false
provenance: {}
recipe_fingerprint:
  git_ref: 398bc46
verdict: inconclusive
---

## Summary
Tests hypothesis [[h4-caption-content-not-architecture-the-3306b2]].
Branches from [[exp-v34-nopeak-decomposition-20260901]].
At commit `398bc46`.

## Measurements
- [[eiv_supabase_seed_band_mae_bpm]]: before 3.03 → after 1.862  (Δ -1.168, down)
- [[eiv_gen_peak_corr]]: before 0.287 → after 0.516  (Δ +0.229, up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 2.851 → after 4.017  (Δ +1.166, up)
- [[eiv_effective_rank]]: before 8.599 → after 9.711  (Δ +1.112, up)

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `recipe_conditional`

LABELS BELONG IN THE CAPTION, NOT IN NEW HEADS. Stating a window's own labels as text clauses produced the campaign's best supabase numbers -- hr_raw 1.862 (incumbent 3.030, CI[-1.496,-0.859] vs incumbent and CI[-0.525,-0.036] vs v33, both excluding zero) and hr_peak skill +1.353 (incumbent -4.422) -- with one config key and no new supervised head. This is the third consecutive confirmation that the text VIEW, not the loss architecture, is the lever on this pipeline. It also settles the goal question 'use all labels in all three phases': the head route is closed by four arms, and the audit showed the orphan channels are small when counted by windows rather than rows, so the caption is the route.

SECOND, LARGER FINDING, UNPLANNED: v36 closed its JSON on 128/128 dense rows against v33's 81 and v34's 88 -- the termination defect that blocked the entire caption branch simply vanished, and it was captured BEFORE the --stop-on-brace fix existed. Working hypothesis to test next: the label clauses lengthen and diversify the Phase-A text view, and the looping tail was the low-entropy ending a monotonous caption distribution invites. Not yet demonstrated.

THE COST IS REAL AND MUST BE PRICED: dense hr_raw regresses to 4.017 vs 2.851 (CI[+0.360,+2.055]) with std_ratio 1.11 -> 1.32 at corr 0.844 -- the ranking-survives/calibration-dies signature again. So v36 is a candidate, not a promotion: the incumbent stays until a second seed prices the dense side against the 3.19 bpm seed band.

PROCESS LESSON WORTH MORE THAN THE ARM: the driver's rescore block had the arm name HARDCODED, and the v36 driver was sed-generated from the v35 one, so it silently rescored v35wide and printed a table that read as v36's result. A script that cannot name its own subject will eventually be copied onto a different subject and lie. It is now a CLI argument.

Transfers to: [[the-hr-peak-native-emission-deficit-264f4f]], [[full-utilisation-at-the-safe-dose-the-v29-chain-616efb]]

## Key numbers
- `dense_hr_peak_mae`: 15.751
- `dense_hr_raw_ci_hi_vs_v28`: 2.055
- `dense_hr_raw_ci_lo_vs_v28`: 0.36
- `dense_hr_raw_corr`: 0.844
- `dense_hr_raw_mae`: 4.017
- `dense_hr_raw_std_ratio`: 1.32
- `dense_parse_native`: 128
- `dense_parse_native_v33`: 81
- `eff_rank`: 9.711
- `supa_hr_peak_corr`: 0.516
- `supa_hr_peak_mae`: 16.057
- `supa_hr_peak_skill`: 1.353
- `supa_hr_raw_ci_hi_vs_v33`: -0.036
- `supa_hr_raw_ci_lo_vs_v33`: -0.525
- `supa_hr_raw_corr`: 0.893
- `supa_hr_raw_mae`: 1.862
- `supa_hr_raw_skill`: 2.093

## Artifacts
- `outputs/adapters/v36labels_c`
- `logs/v36_driver.log`
- `logs/emission_v36labels_supa_summary.json`
- `logs/emission_v36labels_dense_summary.json`
- `scratch/_arm_rescore.py`
- `scratch/_verify_h5_caption.py`
- `scratch/_label_coverage_audit.py`

## Do not retry
- Do NOT add supervised heads for the orphan label channels. The label-coverage audit measured supply by WINDOWS (rows are the wrong denominator -- a continuously-sampled dial puts 149,098 rows into 486 windows): the largest orphan reaches 685 windows against 5,331 already covered, while v22/v29 measured -1.50/-1.816 eff_rank for adding heads and v31/v32 measured deployment destruction for detaching them. The caption is the working channel for labels.
- Do NOT read any driver's own HONEST RESCORE block for v36 or later arms: scratch/_v35_rescore.py had the arm name HARDCODED to 'v35wide', and the v36 driver was sed-generated from the v35 one, so it rescored v35wide and printed a table that read as v36's result. Fixed by making the arm a CLI argument (renamed scratch/_arm_rescore.py --arm X --vs A,B). Any v36 number quoted from a driver log before 2026-09-01 10:05 is v35's.
- Do NOT promote v36labels on the supabase result alone. Dense hr_raw regresses to 4.017 vs the incumbent 2.851 (CI [+0.360,+2.055], demonstrated) with std_ratio drifting 1.11 -> 1.32 while corr stays 0.844 -- the ranking-survives-calibration-dies signature. A deployment decision needs the dense draw priced too.
- Do NOT attribute v36's JSON fix to the --stop-on-brace decoding change. v36 was captured BEFORE that flag existed; it closed 128/128 natively from the caption change alone.

## Notes
LABELS TRAVEL THROUGH THE CAPTION, AND THIS IS THE BEST SUPABASE ARM OF THE CAMPAIGN.

The standing goal was to use every label in all three phases. The audit
(scratch/_label_coverage_audit.py) showed only 16.9% of label ROWS reach any
head -- but measured by WINDOWS the orphan channels are small (largest 685 vs
5,331 already covered), so adding heads would buy little and the head route is
closed by four arms anyway. This arm routes the labels through the channel that
is measured to work: the text view.

SUPABASE, 200 shared rows, baseline recomputed on those rows:
  hr_raw  1.862 (skill +2.093, corr 0.893, std_ratio 0.94)
    vs v33 2.142: dMAE -0.280 CI[-0.525,-0.036] -> BETTER (CI excludes 0)
    vs v28 3.030: dMAE -1.168 CI[-1.496,-0.859] -> BETTER, 6x the 0.195 sigma
  hr_peak 16.057 (skill +1.353, corr 0.516) vs v33 16.715 / v28 21.832
    vs v28: dMAE -5.774 CI[-7.425,-4.123] -> BETTER
    vs v33: dMAE -0.658 CI[-2.332,+1.059] -> not demonstrated
Both headline supabase numbers are the best recorded: hr_raw 1.862 against the
incumbent's 3.030, and hr_peak skill +1.353 against the incumbent's -4.422.

THE UNEXPECTED RESULT: v36 closed its JSON on 128/128 dense rows, against 81
for v33 and 88 for v34 (incumbent 127). The termination defect that has blocked
promotion of the whole caption branch simply does not occur in this arm, and it
was captured BEFORE --stop-on-brace existed, so the caption change alone fixed
it. Plausible mechanism, not yet tested: the label clauses lengthen and vary
the Phase-A text view, and the degenerate tail the model was looping
('temp':'normal','state':'flow') is exactly the low-entropy ending that a more
varied caption distribution discourages. That is a hypothesis, not a finding.

WHERE IT PAYS: dense hr_raw 4.017 vs incumbent 2.851, CI[+0.360,+2.055] --
a demonstrated regression, with std_ratio drifting 1.11 -> 1.32 while corr
holds at 0.844. Same ranking-survives/calibration-dies signature as v30/v31/v32,
in milder form. Dense hr_peak 15.751 is nominally best but neither delta is
demonstrated.

SO: v36 wins the deployment draw decisively and loses the dense draw
demonstrably. It is a genuine candidate rather than a clean promotion, and the
incumbent stays v28affect01 until the dense side is priced against a second
seed (the seed band on MAE is 3.19 bpm, far wider than the 1.166 dense gap).

PROCESS DEFECT FOUND AND FIXED: the driver's own rescore block reported
v35wide, because scratch/_v35_rescore.py hardcoded the arm name and the v36
driver was sed-generated from the v35 one. Every v36 number in this record
comes from the fixed scratch/_arm_rescore.py --arm v36labels.

#cross-modal #sensor-text #contrastive #infonce #baseline #epoch-resolution #dataset-whitelist #wearables
