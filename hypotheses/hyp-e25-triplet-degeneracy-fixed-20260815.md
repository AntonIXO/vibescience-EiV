---
created_at: '2026-08-15T07:46:02+00:00'
former_ids: []
gates:
- blocking: true
  description: 'PREMISE 1. The single-state share among labelled windows must be materially
    below the recorded 0.991 — concretely <= 0.95, i.e. at least 5% of labelled windows
    carry a state TRANSITION. MDE 0.95. NOTE FOR THE RECORD: this probe was executed
    BEFORE this hypothesis was registered (the numbers existed first), so this gate
    is a POST-HOC formalisation of the problem record''s own stated claim, not a blind
    prediction. Recorded as such rather than presented as prereg.'
  diagnostic_id: eiv_affect_multichannel_increment
  id: g1_single_state_share_below_claim
  mde: 0.95
- blocking: true
  description: PREMISE 2, the one that decides triplet validity. Every non-neutral
    state must span >1 dataset with top-dataset purity < 0.999, so that same-state
    positive pairs are not automatically same-dataset pairs. If purity is ~1.0 the
    triplet margin separates provenance rather than physiology and the Phase B objective
    remains vacuous regardless of label timing. Same post-hoc caveat as g1.
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  id: g2_states_span_multiple_datasets
  mde: null
id: hyp-e25-triplet-degeneracy-fixed-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: Single-state share among labelled windows falls well below the recorded
    0.991, and every non-neutral state spans multiple datasets at top-dataset purity
    far below 1.0 — together making cross-dataset same-state triplet mining possible
    for the first time.
problem_id: phase-b-triplet-orthogonalization-is-f6d3c5
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
status: refuted
supersedes: null
topic_tags:
- wearables
- baseline
- control
- contrastive
- dataset-whitelist
- collapse
---

## Statement
H-E25 (triplet degeneracy is a property of the OLD label supply). Problem phase-b-triplet-orthogonalization-is-f6d3c5 parks Phase B as 'close to vacuous' on two numbers: 99.1% of training samples carry a single state label, and every non-NONE label is a 100%-pure dataset identifier. Both were measured before commit a7fc419 placed ground truth on the epoch it was recorded in.

PREDICTION: on the current corpus, (a) the single-state share among labelled windows is materially below 0.991, and (b) non-neutral states span MULTIPLE datasets with top-dataset purity well below 1.0 — so same-state positive pairs are no longer automatically same-dataset pairs, and the triplet margin can separate physiology rather than provenance.

IF CONFIRMED: the parked problem's blocking premise is obsolete, Phase B triplet retraining on the new labels becomes a live and justified arm, and the objective built to separate states finally has labels capable of separating states.
IF REFUTED: the degeneracy survives the label migration, Phase B stays parked, and no triplet retrain should be funded.

## Rationale
The problem record parks Phase B as near-vacuous on two specific numbers, both measured before the per-epoch label migration. Commit a7fc419 changed exactly the thing those numbers describe: a window can now carry a SEQUENCE of momentary states rather than one majority-reduced label, and labels land on real timestamps across all contributing datasets.

Three times today a dormant hypothesis rested on a premise the corpus had moved under (E20 refuted outright, E21 weakened, E23's MDE false). Each cost CPU minutes to catch and two would have been GPU-hours misspent. Re-measuring this premise before proposing any Phase B retrain is the same discipline applied consistently.

It also matters because E24 has just supplied cohorts (WESAD AUC 0.8497, neuro_stress 0.6806) where state labels demonstrably carry a large effect — so if the triplet supply is now non-degenerate, Phase B has both a working objective and a cohort where the target is real.

## Plan
scratch/e25_triplet_degeneracy_premise.py. Pure CPU, no GPU, no model weights.

Load all 11318 samples through load_sample_metadata (the production contract), derive momentary states with state_series() and the day reduction with infer_state_label() — never a re-implemented scatter, per the CLAUDE.md rule.

Measure, for BOTH the new per-epoch series and the incumbent day reduction so the a7fc419 delta is explicit:
  1. distinct momentary states per window (tests the 99.1% single-state claim)
  2. per-state dataset purity: share held by the top dataset (tests the 100%-pure claim)
  3. number of datasets each state spans — the quantity that decides whether a mined same-state positive pair can be cross-dataset

HONESTY NOTE ON SEQUENCING: this probe was written and run BEFORE the hypothesis was registered, so its gates formalise the problem record's existing claims rather than committing a blind prediction. The gates are stated as the problem itself stated them (0.991 single-state; 100% purity), which is the only reading under which a post-hoc gate is meaningful — the thresholds come from the prior record, not from the observed result.

## Links
Tests problem [[phase-b-triplet-orthogonalization-is-f6d3c5]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1_single_state_share_below_claim` (blocking): PREMISE 1. The single-state share among labelled windows must be materially below the recorded 0.991 — concretely <= 0.95, i.e. at least 5% of labelled windows carry a state TRANSITION. MDE 0.95. NOTE FOR THE RECORD: this probe was executed BEFORE this hypothesis was registered (the numbers existed first), so this gate is a POST-HOC formalisation of the problem record's own stated claim, not a blind prediction. Recorded as such rather than presented as prereg.
Gate `g2_states_span_multiple_datasets` (blocking): PREMISE 2, the one that decides triplet validity. Every non-neutral state must span >1 dataset with top-dataset purity < 0.999, so that same-state positive pairs are not automatically same-dataset pairs. If purity is ~1.0 the triplet margin separates provenance rather than physiology and the Phase B objective remains vacuous regardless of label timing. Same post-hoc caveat as g1.

#wearables #baseline #control #contrastive #dataset-whitelist #collapse #optihealth-eiv #data-contract-violation #matched-baseline
