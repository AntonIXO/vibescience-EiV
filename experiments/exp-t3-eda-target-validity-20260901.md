---
artifacts:
- logs/t3_eda_target_validity.json
- logs/t3_eda_target_validity.log
- scratch/t3_eda_target_validity.py
closed: true
config_note: 'CPU-only, nothing trained, no checkpoint read. Runner scratch/t3_eda_target_validity.py
  -> logs/t3_eda_target_validity.json. Corpus: data/canonical, merge_manifest git_sha
  f1b15e9e9aba8cb1d991f8882815a1df4972b7ec, window_timesteps 1440, num_channels 12.
  Privileged source privileged_events.parquet (1,158,980 rows: 429,847 eda_tonic /
  729,133 eda_scr_rate), stats privileged_stats.json (by_subject / by_dataset / pooled).
  State labels read via src.training.datasets.state_series over load_sample_metadata,
  never re-scattered. 200 within-stratum permutations per conditional cell, 60 for
  the I(EDA;dataset) confound; 8 quantile bins for the plug-in estimator (edges recomputed
  inside each stratum); k-NN estimator is sklearn mutual_info_classif n_neighbors=3,
  seed 0. Lab corpora for the gate: case, wesad, popane_s4, popane_s5, popane_s6 (popane_s3
  excluded by declaration -- it registers SCR rate only, its amplifier being offset-coupled).
  Runs concurrently with GPU unit eiv-v39c; no resource contention with the science.'
cost:
  gpu_hours: 0.0
  wall_hours: 1.1
crash_reason: ''
created_at: '2026-09-01T19:59:55+00:00'
diagnostics_measured:
- after: 0.0446
  before: 0.0
  diagnostic_id: eiv_eda_state_cmi_over_null
do_not_retry:
- Do NOT choose eda_scr_rate over eda_tonic as the LUPI target on the strength of
  NEXT.md's reading of Yang 2022 ('pick the less predictive target if two are available
  -- SCR rate over tonic level'). That prescription was written before this measurement
  and the measurement reverses it. Yang's hazard is a teacher too predictive OF THE
  DOWNSTREAM LABEL, and neither channel is near that here; the operative hazard in
  this corpus is corpus identity, and on that axis eda_tonic dominates on BOTH counts
  at once -- more state signal (over_null 0.0446/0.0630 vs 0.0176/0.0183) and far
  less dataset information relative to it (1.1-3.1x vs 9.1-37.0x). Use eda_tonic.
- Do NOT run a LUPI head against the POOLED or BY_DATASET privileged fit. The conditional
  state signal is flat across the three fits while I(EDA;dataset) falls 0.3162 ->
  0.0925 -> 0.0503, so a pooled-fit target is overwhelmingly a corpus indicator --
  the same shortcut already measured in this project at bal-acc 0.9945 against 0.1250
  chance. by_subject or nothing.
- Do NOT read popane_s4's z = +0.00 as a null. At 10.0 rows per subject every stratum
  falls under cond_mi's 20-row floor, so the statistic is undefined; its within-subject
  Spearman is +0.776 (n=36), the highest measured. A per-corpus claim there needs
  a coarser stratum or a lower floor with the plug-in bias re-controlled.
- Do NOT quote this experiment as evidence that a LUPI arm will help deployment. It
  is a data-level measurement; the standing rule (five rule_outs retracted 2026-08-08)
  is that such a measurement cannot close a claim about the trained pipeline. It removes
  the objection that the target is a pure fingerprint. Generalised distillation remains
  UNTESTED.
- 'Do NOT re-run this as a supply experiment, which exp-055''s do_not_retry already
  forbids. Supply is not the constraint: 49,860 joined cells over 9 datasets. The
  next step is a trained arm, not more rows.'
external_run: ''
former_ids: []
gate_results:
- evidence: "PASS in 6 cells, requirement was >=1. by_subject fit, conditioned on
    SUBJECT, per lab corpus, z on BOTH estimator families:\n  eda_tonic    wesad      n=1457
    \ subj=15   z_binned +28.24  z_knn +40.37\n  eda_tonic    popane_s6  n=3868  subj=133
    \ z_binned  +9.20  z_knn +13.13\n  eda_tonic    popane_s5  n=4960  subj=194  z_binned
    \ +5.10  z_knn  +7.98\n  eda_tonic    case       n=1230  subj=30   z_binned  +3.25
    \ z_knn  +4.67\n  eda_scr_rate wesad      n=1454  subj=15   z_binned +22.77  z_knn
    +18.30\n  eda_scr_rate case       n=1230  subj=30   z_binned  +8.03  z_knn  +7.44\nThe
    both-estimators requirement was the operative one and it is met everywhere: the
    two families agree in sign and in rank wherever both are estimable, so exp-055's
    recorded sign flip between readout families does not recur. Effect size against
    the 0.005 nats mde: the corpus-wide by_subject over-null is 0.0446 nats (binned)
    / 0.0630 (k-NN) for eda_tonic and 0.0176 / 0.0183 for eda_scr_rate -- all above
    the mde, the smallest by 3.5x. NOT PASSING, and recorded as undefined rather than
    null: popane_s4 reads z=+0.00 on both estimators because at 10.0 rows/subject
    every stratum is below cond_mi's 20-row floor and is skipped; its within-subject
    Spearman is +0.776 (n=36), the highest in the table."
  gate_id: g1-conditional-signal-clears-null
  passed: true
- evidence: "PASS -- I(EDA;dataset) is reported in the same table as every conditional
    figure, and it changes the prescription rather than merely decorating it.\n  by_subject
    fit:   I(EDA;dataset)   I(state|subject)_over_null   ratio\n  eda_tonic binned
    \     0.0503              0.0446                 1.1x\n  eda_tonic knn         0.1955
    \             0.0630                 3.1x\n  eda_scr   binned      0.1597              0.0176
    \                9.1x\n  eda_scr   knn         0.6784              0.0183                37.0x\nAnd
    across fits, for eda_tonic: I(EDA;dataset) 0.3162 (pooled) -> 0.0925 (by_dataset)
    -> 0.0503 (by_subject), a 6.3x collapse, while the conditional signal is essentially
    flat (0.0173 / 0.0173 / 0.0222 binned). So the narrowest fit does not add state
    information, it removes corpus identity. The Yang 2022 failure mode -- a target
    satisfiable by learning which corpus this is -- is REAL for eda_scr_rate (9-37x)
    and largely absent for eda_tonic under by_subject (1.1-3.1x). My committed expectation
    was that the confound would be large relative to the conditional term for BOTH
    channels; that was right for scr_rate and wrong for tonic, in the direction that
    favours the lever."
  gate_id: g2-confound-must-be-reported-beside-it
  passed: true
- evidence: 'PASS by a wide margin; the bar was >=40 joined cells with >=2 state classes
    in >=1 lab corpus. Measured: 49,860 joined (window, epoch) cells carrying BOTH
    an EDA observation and a state label -- eda_tonic 23,259 over 534 subjects, eda_scr_rate
    26,601 over 548, across 9 datasets. Lab corpora individually: case 1,230 / wesad
    1,457 / popane_s4 823 / popane_s5 4,960 / popane_s6 3,868 for tonic, all with
    >=2 state classes. Corpus-wide class balance neutral 11,655 / stress 9,947 / flow
    1,657. This retires CLAUDE.md''s ''only 103 windows have EDA together with an
    affect label'', which predates several corpus rebuilds and is low by more than
    two orders of magnitude. The ''inconclusive for want of overlap'' branch this
    gate protected against does not apply.'
  gate_id: g3-supply-must-be-real
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: eda-carries-state-information-beyond-corpus-identity
id: exp-t3-eda-target-validity-20260901
interpretation:
  at: '2026-09-01T20:47:40+00:00'
  failure_mode: null
  lesson: 'Privileged EDA is a real state signal and not a corpus fingerprint — but
    ONLY as `eda_tonic` and ONLY on the `by_subject` fit, which is the opposite of
    the channel NEXT.md prescribed. The standardisation is the mechanism, not a robustness
    knob: across pooled → by_dataset → by_subject the conditional state signal is
    flat (0.0173 → 0.0173 → 0.0222 nats over null) while I(EDA;dataset) collapses
    6.3× (0.3162 → 0.0925 → 0.0503), so the narrow fit subtracts corpus identity rather
    than adding physiology. eda_scr_rate carries 9–37× more dataset information than
    state and is the target Yang 2022''s non-monotone hazard actually applies to.
    A GPU arm is now justified — generalised distillation on the patch axis, eda_tonic,
    by_subject, weight ≤ 0.1 — but nothing here says it will help: this is data-level,
    and the standing rule is that data-level measurements cannot close claims about
    the trained pipeline.'
  transfers_to:
  - the-a2-lupi-null-rule-out-that-per-patch-eda-89857c
key_numbers:
  confound_ratio_scr_binned: 9.1
  confound_ratio_scr_knn: 37.0
  confound_ratio_tonic_binned: 1.1
  confound_ratio_tonic_knn: 3.1
  datasets: 9.0
  joined_rows_total: 49860.0
  lab_cells_passing_both_estimators: 6.0
  mi_eda_dataset_tonic_by_dataset: 0.0925
  mi_eda_dataset_tonic_by_subject: 0.0503
  mi_eda_dataset_tonic_pooled: 0.3162
  scr_by_subject_over_null_binned: 0.0176
  scr_by_subject_over_null_knn: 0.0183
  scr_by_subject_z_binned: 24.3
  scr_by_subject_z_knn: 13.2
  scr_rate_n: 26601.0
  subjects_tonic: 534.0
  tonic_by_subject_over_null_binned: 0.0446
  tonic_by_subject_over_null_knn: 0.063
  tonic_by_subject_z_binned: 55.7
  tonic_by_subject_z_knn: 57.9
  tonic_n: 23259.0
observed_effects:
- delta: 0.0446
  diagnostic_id: eiv_eda_state_cmi_over_null
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_eda_state_cmi_over_null: true
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: supports
---

## Summary
Tests hypothesis [[eda-carries-state-information-beyond-corpus-identity]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.

## Measurements
- [[eiv_eda_state_cmi_over_null]]: before 0.0 → after 0.0446  (Δ +0.0446, up ✓ predicted up)

## Gates
- `g1-conditional-signal-clears-null`: PASS — PASS in 6 cells, requirement was >=1. by_subject fit, conditioned on SUBJECT, per lab corpus, z on BOTH estimator families:
  eda_tonic    wesad      n=1457  subj=15   z_binned +28.24  z_knn +40.37
  eda_tonic    popane_s6  n=3868  subj=133  z_binned  +9.20  z_knn +13.13
  eda_tonic    popane_s5  n=4960  subj=194  z_binned  +5.10  z_knn  +7.98
  eda_tonic    case       n=1230  subj=30   z_binned  +3.25  z_knn  +4.67
  eda_scr_rate wesad      n=1454  subj=15   z_binned +22.77  z_knn +18.30
  eda_scr_rate case       n=1230  subj=30   z_binned  +8.03  z_knn  +7.44
The both-estimators requirement was the operative one and it is met everywhere: the two families agree in sign and in rank wherever both are estimable, so exp-055's recorded sign flip between readout families does not recur. Effect size against the 0.005 nats mde: the corpus-wide by_subject over-null is 0.0446 nats (binned) / 0.0630 (k-NN) for eda_tonic and 0.0176 / 0.0183 for eda_scr_rate -- all above the mde, the smallest by 3.5x. NOT PASSING, and recorded as undefined rather than null: popane_s4 reads z=+0.00 on both estimators because at 10.0 rows/subject every stratum is below cond_mi's 20-row floor and is skipped; its within-subject Spearman is +0.776 (n=36), the highest in the table.
- `g2-confound-must-be-reported-beside-it`: PASS — PASS -- I(EDA;dataset) is reported in the same table as every conditional figure, and it changes the prescription rather than merely decorating it.
  by_subject fit:   I(EDA;dataset)   I(state|subject)_over_null   ratio
  eda_tonic binned      0.0503              0.0446                 1.1x
  eda_tonic knn         0.1955              0.0630                 3.1x
  eda_scr   binned      0.1597              0.0176                 9.1x
  eda_scr   knn         0.6784              0.0183                37.0x
And across fits, for eda_tonic: I(EDA;dataset) 0.3162 (pooled) -> 0.0925 (by_dataset) -> 0.0503 (by_subject), a 6.3x collapse, while the conditional signal is essentially flat (0.0173 / 0.0173 / 0.0222 binned). So the narrowest fit does not add state information, it removes corpus identity. The Yang 2022 failure mode -- a target satisfiable by learning which corpus this is -- is REAL for eda_scr_rate (9-37x) and largely absent for eda_tonic under by_subject (1.1-3.1x). My committed expectation was that the confound would be large relative to the conditional term for BOTH channels; that was right for scr_rate and wrong for tonic, in the direction that favours the lever.
- `g3-supply-must-be-real`: PASS — PASS by a wide margin; the bar was >=40 joined cells with >=2 state classes in >=1 lab corpus. Measured: 49,860 joined (window, epoch) cells carrying BOTH an EDA observation and a state label -- eda_tonic 23,259 over 534 subjects, eda_scr_rate 26,601 over 548, across 9 datasets. Lab corpora individually: case 1,230 / wesad 1,457 / popane_s4 823 / popane_s5 4,960 / popane_s6 3,868 for tonic, all with >=2 state classes. Corpus-wide class balance neutral 11,655 / stress 9,947 / flow 1,657. This retires CLAUDE.md's 'only 103 windows have EDA together with an affect label', which predates several corpus rebuilds and is low by more than two orders of magnitude. The 'inconclusive for want of overlap' branch this gate protected against does not apply.

**Verdict:** supports

## Interpretation
Privileged EDA is a real state signal and not a corpus fingerprint — but ONLY as `eda_tonic` and ONLY on the `by_subject` fit, which is the opposite of the channel NEXT.md prescribed. The standardisation is the mechanism, not a robustness knob: across pooled → by_dataset → by_subject the conditional state signal is flat (0.0173 → 0.0173 → 0.0222 nats over null) while I(EDA;dataset) collapses 6.3× (0.3162 → 0.0925 → 0.0503), so the narrow fit subtracts corpus identity rather than adding physiology. eda_scr_rate carries 9–37× more dataset information than state and is the target Yang 2022's non-monotone hazard actually applies to. A GPU arm is now justified — generalised distillation on the patch axis, eda_tonic, by_subject, weight ≤ 0.1 — but nothing here says it will help: this is data-level, and the standing rule is that data-level measurements cannot close claims about the trained pipeline.

Transfers to: [[the-a2-lupi-null-rule-out-that-per-patch-eda-89857c]]

## Key numbers
- `confound_ratio_scr_binned`: 9.1
- `confound_ratio_scr_knn`: 37
- `confound_ratio_tonic_binned`: 1.1
- `confound_ratio_tonic_knn`: 3.1
- `datasets`: 9
- `joined_rows_total`: 49860
- `lab_cells_passing_both_estimators`: 6
- `mi_eda_dataset_tonic_by_dataset`: 0.0925
- `mi_eda_dataset_tonic_by_subject`: 0.0503
- `mi_eda_dataset_tonic_pooled`: 0.3162
- `scr_by_subject_over_null_binned`: 0.0176
- `scr_by_subject_over_null_knn`: 0.0183
- `scr_by_subject_z_binned`: 24.3
- `scr_by_subject_z_knn`: 13.2
- `scr_rate_n`: 26601
- `subjects_tonic`: 534
- `tonic_by_subject_over_null_binned`: 0.0446
- `tonic_by_subject_over_null_knn`: 0.063
- `tonic_by_subject_z_binned`: 55.7
- `tonic_by_subject_z_knn`: 57.9
- `tonic_n`: 23259

## Artifacts
- `logs/t3_eda_target_validity.json`
- `logs/t3_eda_target_validity.log`
- `scratch/t3_eda_target_validity.py`

## Do not retry
- Do NOT choose eda_scr_rate over eda_tonic as the LUPI target on the strength of NEXT.md's reading of Yang 2022 ('pick the less predictive target if two are available -- SCR rate over tonic level'). That prescription was written before this measurement and the measurement reverses it. Yang's hazard is a teacher too predictive OF THE DOWNSTREAM LABEL, and neither channel is near that here; the operative hazard in this corpus is corpus identity, and on that axis eda_tonic dominates on BOTH counts at once -- more state signal (over_null 0.0446/0.0630 vs 0.0176/0.0183) and far less dataset information relative to it (1.1-3.1x vs 9.1-37.0x). Use eda_tonic.
- Do NOT run a LUPI head against the POOLED or BY_DATASET privileged fit. The conditional state signal is flat across the three fits while I(EDA;dataset) falls 0.3162 -> 0.0925 -> 0.0503, so a pooled-fit target is overwhelmingly a corpus indicator -- the same shortcut already measured in this project at bal-acc 0.9945 against 0.1250 chance. by_subject or nothing.
- Do NOT read popane_s4's z = +0.00 as a null. At 10.0 rows per subject every stratum falls under cond_mi's 20-row floor, so the statistic is undefined; its within-subject Spearman is +0.776 (n=36), the highest measured. A per-corpus claim there needs a coarser stratum or a lower floor with the plug-in bias re-controlled.
- Do NOT quote this experiment as evidence that a LUPI arm will help deployment. It is a data-level measurement; the standing rule (five rule_outs retracted 2026-08-08) is that such a measurement cannot close a claim about the trained pipeline. It removes the objection that the target is a pure fingerprint. Generalised distillation remains UNTESTED.
- Do NOT re-run this as a supply experiment, which exp-055's do_not_retry already forbids. Supply is not the constraint: 49,860 joined cells over 9 datasets. The next step is a trained arm, not more rows.

## Notes
The prediction was that EDA carries state information surviving conditioning on corpus AND subject, in at least one lab corpus, on BOTH estimator families. It does, in six cells.

=== SUPPLY (g3) RETIRES AN ARCHIVED FIGURE ===
49,860 joined (window, epoch) cells carry BOTH an EDA observation and a state label: eda_tonic 23,259 over 534 subjects, eda_scr_rate 26,601 over 548, across 9 datasets, class balance neutral 11,655 / stress 9,947 / flow 1,657. CLAUDE.md's "only 103 windows have EDA together with an affect label" predates several corpus rebuilds and is low by more than two orders of magnitude.

=== THE GATE (g1) ===
by_subject fit, conditioned on SUBJECT: eda_tonic clears z>=3 on both estimators in wesad (+28.24/+40.37), popane_s6 (+9.20/+13.13), popane_s5 (+5.10/+7.98) and case (+3.25/+4.67); eda_scr_rate in wesad (+22.77/+18.30) and case (+8.03/+7.44). The two estimator families agree in sign and rank wherever both are estimable -- the sign flip exp-055's do_not_retry warned about does not recur.

=== popane_s4's z = +0.00 IS UNDEFINED, NOT NULL ===
823 rows over 82 subjects = 10.0/subject, below cond_mi's 20-row-per-stratum floor, so every stratum is skipped. Its within-subject Spearman is +0.776 over 36 subjects, the highest in the table. Do not cite it as evidence of absence.

=== WHERE MY COMMITTED EXPECTATION WAS WRONG, AND IT MATTERS ===
I predicted the confound I(EDA;dataset) would be LARGE relative to the conditional signal. For eda_scr_rate that is emphatically right: 9.1x (binned) and 37.0x (k-NN). For eda_tonic under the by_subject fit it is NOT: 1.1x and 3.1x. The channel this project was about to deprioritise is the one that is mostly physiology, and the channel NEXT.md prescribed is the one that is mostly corpus identity.

=== THE STANDARDISATION IS THE MECHANISM, EXACTLY AS PREREGISTERED ===
For eda_tonic the conditional signal is flat across fits (over_null 0.0173 / 0.0173 / 0.0222 binned) while the confound collapses 6.3x: I(EDA;dataset) 0.3162 pooled -> 0.0925 by_dataset -> 0.0503 by_subject. The narrowest fit does not add state information; it subtracts corpus identity. `privileged_patch_targets` preferring by_subject is therefore load-bearing, not a robustness nicety.

=== WHAT THIS LICENSES, AND WHAT IT DOES NOT ===
It licenses a GPU arm: generalised distillation (Lopez-Paz 2015, arXiv:1511.03643) on the patch axis, target eda_tonic on the by_subject fit, weight <= 0.1 (exp-v22/v29 measured heads costing -1.5 to -1.8 eff_rank at 0.3), scored on the clean transition pool. It does NOT support the claim that such an arm will help: this is a data-level measurement and the project's standing rule -- the one that cost five retracted rule_outs on 2026-08-08 -- is that a data-level measurement cannot close a claim about the trained pipeline. LUPI remains UNTESTED, not supported.

#optihealth-eiv
