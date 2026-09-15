---
ambition_class: mechanism
created_at: '2026-08-24T09:11:28+00:00'
former_ids: []
gap_closure_estimate: 0.15
gates:
- blocking: true
  description: 'PRIMARY, BLOCKING. On the TRAINED baseline_sync encoder, at least
    one (channel x readout) cell reaches r_over_null >= +0.05 with z >= +2.0 against
    its own >=40-permutation within-subject null. MDE: the run is powered to detect
    r_over_null ~ 0.05 at 1,387-1,616 windows / 743-911 subjects; if this gate fails
    and |r_over_null| < 0.05 the verdict is a POWERED NULL at the new supply, which
    upgrades rule_out ...1dadc0 from supply-scoped to supply-independent and CLOSES
    the LUPI-token-mechanism question rather than leaving it open.'
  diagnostic_id: eiv_privileged_eda_token_r_over_null
  id: gate-eda-recoverable-over-null
  mde: 0.05
- blocking: true
  description: BLOCKING on any claim about the ENCODER (not on the recoverability
    claim itself). The trained arm must exceed the random-init arm of identical geometry
    on the primary cell. exp-046 established that patch geometry alone can carry a
    signal the learned weights do not, so without this gate a positive Q1 says 'a
    120-minute patch mean of 12 wrist channels correlates with EDA' — a statement
    about the data, not about training. If Q1 passes and this fails, that IS the finding
    and must be reported as such.
  diagnostic_id: eiv_privileged_eda_token_r_over_null
  id: gate-trained-beats-random-init
  mde: null
- blocking: true
  description: INSTRUMENT VALIDITY, BLOCKING. Null sd must be > 0.001 on every scored
    cell, and the count of subjects contributing >1 patch must be reported. A2's first
    null had sd exactly 0.000 because within-subject permutation was the identity
    at one session per subject; that degeneracy is silent and produces an infinite
    z. Abort rather than report if it recurs.
  diagnostic_id: null
  id: gate-null-not-degenerate
  mde: null
- blocking: true
  description: REPORTING DISCIPLINE, BLOCKING. Both RidgeCV and RandomForest results
    are reported with their OWN nulls on identical rows/folds, and the estimator is
    named in every quoted number. Mandated by the 2026-08-18 amendment to eiv_momentary_state_auc_over_null,
    where the arm ordering inverted between readouts on identical data. A2 ran linear
    only.
  diagnostic_id: null
  id: gate-two-readout-families
  mde: null
- blocking: false
  description: REPORTING DISCIPLINE, non-blocking. R2 against the always-predict-median
    trivial is quoted beside every r. A significant r with R2 <= 0 is a real correlation
    with no usable skill, and this project has already had one near-miss reading from
    a z quoted without an effect size.
  diagnostic_id: null
  id: gate-skill-beside-r
  mde: null
id: the-a2-lupi-null-rule-out-that-per-patch-eda-89857c
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_privileged_eda_token_r_over_null
  direction: up
  magnitude_note: 'PRIMARY. eda_tonic on the baseline_sync Phase-A encoder, subject-grouped
    OOF, over its OWN within-subject permutation null: predict r_over_null >= +0.05
    with z >= +2 on at least one of the two channels under at least one readout family.
    Deliberately BELOW the A2 MDE band (0.15-0.25) because the claim is ''the A2 null
    was underpowered'', not ''EDA is strongly recoverable'' — a 6.9x supply increase
    buys power, not a new mechanism. A result in [0, +0.05) with a tight null is a
    POWERED NULL at the new supply and is the more informative outcome, because it
    converts a supply-scoped rule-out into a supply-independent one.'
- diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: none
  magnitude_note: 'SECONDARY / no-harm guard. This probe reads a FROZEN existing checkpoint
    and trains nothing, so no canonical-channel readout can change. Recorded so the
    run is falsifiable as an instrument: if any non-HR channel R2 moves, the probe
    is touching weights it must not touch.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
- subject-heldout
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- wearables
- lupi
- control
- baseline
- epoch-resolution
---

## Statement
The A2 LUPI null (rule_out that-per-patch-eda-tonic-level-scr-rate-is-1dadc0) was SUPPLY-LIMITED and not a fact about wrist physiology, and the 2026-08-24 corpus refresh (commit 1ef63da, 'recover every unused EDA source') supplies enough EDA to move the measurement. MEASURED ON THE LANDED BUNDLE BEFORE THIS PREDICTION: privileged_events.parquet now holds 1,125,502 rows over 1,714 windows / 934 subjects / 14 datasets, against A2's 250 windows / 250 subjects / 3 datasets — 6.9x the windows and 3.7x the subjects. eda_scr_rate 1,616 windows / 911 subjects; eda_tonic 1,387 / 743. Median epochs per window 380 (scr) and 229 (tonic).

THE PREDICTION IS A PROBE-STAGE ONE AND IS DELIBERATELY MODEST. Q1: at this supply, per-patch EDA becomes recoverable from the trained encoder's tokens ABOVE the A2 MDE of r~0.15-0.25 — i.e. the A2 null was underpowered rather than correct. Q2 (the real reason this matters): even if Q1 reads positive, the LUPI HEAD as currently configured still cannot exploit it, because `phase_a_12ch_analog_priv_control.dataset_filter` is a WHITELIST (datasets.py:2745-2748) that predates the refresh and DROPS 1,286 of the 1,714 EDA windows (75.0%), including dapper's 891 — the single largest EDA source in the corpus and 52% of the new supply. So a re-run of the A2 pair on the new bundle would see only 428 EDA windows, not 1,714, and would re-measure a starved head while appearing to test a 6.9x-larger corpus.

WHY THIS IS SCORED ON THE PROBE FIRST AND NOT A RETRAIN: a retrain pair is ~4.8 GPU-hours and the A2 arms are the wrong instrument to spend it on until the filter defect above is fixed. The probe is CPU/GPU-cheap, reads the ALREADY-TRAINED baseline_sync encoder (trained on this exact bundle, same geometry PL120/ST60, d_model 192, verified in phase_config.json), and answers whether there is anything for a head to learn before any arm is funded.

## Rationale
Three things changed under the A2 result, and all three are verified on disk rather than assumed:

1. SUPPLY. 250 -> 1,714 windows (6.9x), 250 -> 934 subjects (3.7x), 3 -> 14 datasets. A2's own honest sentence was 'below r~0.2 detectable at n=250 subjects x 1 session' — an explicitly supply-scoped claim, which is why re-testing is legitimate rather than re-litigating a refuted lever.

2. THE NULL BECAME AVAILABLE. A2 could not run a within-subject permutation: one session per subject made it the IDENTITY, its null had sd 0.000 and was discarded, so A2 fell back to a between-subject donor-mean null. 473 of 934 EDA subjects now carry >1 window (dapper median 2 / max 6, big_ideas median 8, lifesnaps max 32), so the structure-preserving null the design actually wants now exists.

3. THE TARGET STANDARDISATION CHANGED, and this is the subtle one. privileged_stats.json now ships by_subject fits (903 subjects for scr_rate, 767 for tonic) and privileged_patch_targets PREFERS the narrowest fit (datasets.py:342-348). A2 was measured against by_dataset targets. A by_subject target has the between-subject level REMOVED by construction — which both makes the task harder (the easy dataset/subject-indicator shortcut is gone) and makes A2's donor-mean null test a quantity the target no longer contains. Same code path, different measurement.

The readout amendment on eiv_momentary_state_auc_over_null (2026-08-18, exp-e34) is the reason two readout families are gated rather than one: on that diagnostic the arm ordering INVERTED between RF and linear on identical rows and folds. A2 ran LINEAR ONLY, so its null is a statement about (representation x linear readout) and cannot carry the unqualified reading it was given.

## Plan
CPU/GPU-cheap probe, no training, single script scratch/a3_lupi_supply_eval.py, writing logs/a3_lupi_supply_eval.json stamped with n_windows/n_subjects/written_at per the permutation-null skill's provenance rule.

ROWS: every (window, patch) cell carrying a privileged target, via privileged_patch_targets(m, 1440, 120, 60, stats) — the repo's own accessor, never a re-derived scatter. Report the realised cell/window/subject/dataset counts in the artifact.

FEATURES: baseline_sync Phase-A encoder tokens, channel-mean of that patch's token ([B,C,N,D] -> D=192). This is the checkpoint trained on THIS bundle; geometry verified PL120/ST60/T1440/input_dim 24.

ARMS: (a) trained baseline_sync encoder; (b) RANDOM-INIT encoder of identical geometry — the untrained control exp-046 established is necessary, since patch geometry alone carried momentary state there. Any 'the encoder learned EDA' claim requires trained > random.

READOUTS: RidgeCV (linear) AND RandomForest (tree), each with its OWN null recomputed on the same rows and folds, per the 2026-08-18 amendment.

NULL: >=40 within-subject permutations of the target across that subject's patches. Holds subject, dataset and the target marginal exactly; destroys only patch alignment. Assert null sd > 0 and ABORT if it is 0.000 — the exact degeneracy that invalidated A2's first null.

SKILL: R2 vs always-predict-median reported beside every r, per pricing-metrics-against-trivial-baselines.

If and only if the primary gate passes does a retrain arm get proposed, and it must first FIX the whitelist defect (add dapper/popane_s3/big_ideas_glycemic/dreamt/k_emocon/brain_wearable_monitoring, drop the case:40 oversample that exists only because CASE used to be the sole source).

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_privileged_eda_token_r_over_null]] (up).
Predicts [[eiv_nonhr_channel_grouped_r2]] (none).
Gate `gate-eda-recoverable-over-null` (blocking): PRIMARY, BLOCKING. On the TRAINED baseline_sync encoder, at least one (channel x readout) cell reaches r_over_null >= +0.05 with z >= +2.0 against its own >=40-permutation within-subject null. MDE: the run is powered to detect r_over_null ~ 0.05 at 1,387-1,616 windows / 743-911 subjects; if this gate fails and |r_over_null| < 0.05 the verdict is a POWERED NULL at the new supply, which upgrades rule_out ...1dadc0 from supply-scoped to supply-independent and CLOSES the LUPI-token-mechanism question rather than leaving it open.
Gate `gate-trained-beats-random-init` (blocking): BLOCKING on any claim about the ENCODER (not on the recoverability claim itself). The trained arm must exceed the random-init arm of identical geometry on the primary cell. exp-046 established that patch geometry alone can carry a signal the learned weights do not, so without this gate a positive Q1 says 'a 120-minute patch mean of 12 wrist channels correlates with EDA' — a statement about the data, not about training. If Q1 passes and this fails, that IS the finding and must be reported as such.
Gate `gate-null-not-degenerate` (blocking): INSTRUMENT VALIDITY, BLOCKING. Null sd must be > 0.001 on every scored cell, and the count of subjects contributing >1 patch must be reported. A2's first null had sd exactly 0.000 because within-subject permutation was the identity at one session per subject; that degeneracy is silent and produces an infinite z. Abort rather than report if it recurs.
Gate `gate-two-readout-families` (blocking): REPORTING DISCIPLINE, BLOCKING. Both RidgeCV and RandomForest results are reported with their OWN nulls on identical rows/folds, and the estimator is named in every quoted number. Mandated by the 2026-08-18 amendment to eiv_momentary_state_auc_over_null, where the arm ordering inverted between readouts on identical data. A2 ran linear only.
Gate `gate-skill-beside-r` (advisory): REPORTING DISCIPLINE, non-blocking. R2 against the always-predict-median trivial is quoted beside every r. A significant r with R2 <= 0 is a real correlation with no usable skill, and this project has already had one near-miss reading from a z quoted without an effect size.

#wearables #lupi #control #baseline #epoch-resolution #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
