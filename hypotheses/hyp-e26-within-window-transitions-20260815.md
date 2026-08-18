---
created_at: '2026-08-15T07:54:17+00:00'
former_ids: []
gates:
- blocking: true
  description: 'PRIMARY. On the dominant state pair, HR features over [epoch-W, epoch)
    must reach absolute OOF AUC >= 0.60 at some W <= 120, AND exceed the clock-only
    control on the identical rows by >= +0.05. Both required: the absolute rules out
    a marginal effect and the clock margin rules out circadian bookkeeping, which
    is NOT held constant by the within-window design (the two epochs differ in time-of-day).'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g1_within_window_beats_clock
  mde: 0.6
- blocking: true
  description: 'SHUFFLE CONTROL. Permuting the A/B state assignment WITHIN each window
    must give AUC ~0.50 (|AUC-0.5| < 0.05). This is the decisive control for a paired
    design: it destroys the state contrast while preserving every subject, day, window
    and feature distribution. Anything above it is real state discrimination; a high
    shuffled score would mean the pairing leaks.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g2_shuffle_control_dead
  mde: 0.05
- blocking: false
  description: 'TRAIT-CONFOUND ELIMINATION, the structural point. Because both epochs
    come from the SAME subject on the SAME day, the per-user constant that beat every
    physiological arm in E16/E17/E18 (AUC 0.7381) is exactly 0.5 here by construction
    and cannot be cited as a competing baseline. Informational: record that the within-subject
    constant is inapplicable, which is the whole reason this design exists.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g3_trait_held_constant_by_design
  mde: null
id: hyp-e26-within-window-transitions-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: On within-window state transitions, HR features reach absolute AUC
    >= 0.60 at some W <= 120 with a clock margin >= +0.05, and the within-window shuffle
    control sits at ~0.50. Trait is held constant by construction here, so unlike
    E16/E17/E18 the per-user constant is 0.5 by design and cannot bury the physiological
    arm.
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- data-contract-violation
status: confirmed
supersedes: null
topic_tags:
- wearables
- epoch-resolution
- baseline
- control
- contrastive
---

## Statement
H-E26 (within-window state transitions). The 1274 windows that carry 2-3 DIFFERENT momentary states are the cleanest state-detection design in this corpus: two states, SAME subject, SAME day, minutes apart, so between-subject trait variance — the confound that buried E16/E17/E18 — is held constant by construction rather than by a control.

PREDICTION: peri-epoch HR/HRV features discriminate state A from state B within these windows at absolute AUC >= 0.60 for some W <= 120, exceeding a clock-only control by >= +0.05, with a within-window label-shuffle control at ~0.50.

ENGINEERING CONSEQUENCE EITHER WAY: TripletStateDataset mines on infer_state_label, one majority-collapsed label per window, so every one of these internal contrasts is currently DISCARDED before Phase B sees it. A positive result means the triplet miner is throwing away usable supply and should be changed to mine within-window pairs; a null means the transition windows carry nothing and the miner is fine as it is.

## Rationale
Every state null in this project died on trait dominance: the per-user constant scored AUC 0.7381 while the best physiological arm reached 0.5818 (E16/E17), and the N-of-1 rescue was underpowered (E23). Within-window transitions dissolve that problem structurally rather than statistically — two different states, one subject, one day, minutes apart, so the per-user constant is 0.5 by construction.

E25 established the supply: 1274 such windows now exist (18.06% of labelled windows), up ~20x from the pre-a7fc419 record. This is the first design in the project where the dominant confound is removed by the sampling itself.

It also has a direct engineering consequence. TripletStateDataset mines on infer_state_label (datasets.py:2149) — ONE majority-collapsed label per window — so all 1274 of these internal contrasts are discarded before Phase B ever sees them. Whatever this probe measures is signal the current triplet objective is structurally unable to use, which makes the result a direct test of whether changing the miner is worth GPU time.

## Plan
scratch/e26_within_window_transitions.py -> logs/e26_within_window_transitions.json. Pure CPU.

Enumerate every window carrying >=2 distinct momentary states via the production path (load_sample_metadata + state_series). For the dominant state pair, take the epochs labelled with each state and build peri-epoch HR/HRV features over [epoch-W, epoch) for W in {15,30,60,120} — the SAME feature builder as E16/E18/E24, so any difference is the DESIGN, not the features.

Task: classify state A vs state B, where both come from the same subject and the same day, minutes apart.

ARMS on identical rows, GroupKFold by SUBJECT (still subject-held-out despite the pairing), RF(300, min_samples_leaf=3):
  clock-only    hour sin/cos + minute-of-day   — the CONFOUND FLOOR, still needed because the two epochs differ in time-of-day
  hr            peri-epoch HR/HRV
  hr+clock      both
  SHUFFLE       A/B assignment permuted WITHIN each window, on the hr+clock features — the decisive paired control

Report absolute AUC, delta over clock, shuffle, and majority together (the standing three-numbers rule).

Also records which datasets contribute transition windows, because if a single cohort dominates, the result is about that cohort rather than the corpus.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1_within_window_beats_clock` (blocking): PRIMARY. On the dominant state pair, HR features over [epoch-W, epoch) must reach absolute OOF AUC >= 0.60 at some W <= 120, AND exceed the clock-only control on the identical rows by >= +0.05. Both required: the absolute rules out a marginal effect and the clock margin rules out circadian bookkeeping, which is NOT held constant by the within-window design (the two epochs differ in time-of-day).
Gate `g2_shuffle_control_dead` (blocking): SHUFFLE CONTROL. Permuting the A/B state assignment WITHIN each window must give AUC ~0.50 (|AUC-0.5| < 0.05). This is the decisive control for a paired design: it destroys the state contrast while preserving every subject, day, window and feature distribution. Anything above it is real state discrimination; a high shuffled score would mean the pairing leaks.
Gate `g3_trait_held_constant_by_design` (advisory): TRAIT-CONFOUND ELIMINATION, the structural point. Because both epochs come from the SAME subject on the SAME day, the per-user constant that beat every physiological arm in E16/E17/E18 (AUC 0.7381) is exactly 0.5 here by construction and cannot be cited as a competing baseline. Informational: record that the within-subject constant is inapplicable, which is the whole reason this design exists.

#wearables #epoch-resolution #baseline #control #contrastive #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
