---
artifacts: []
closed: true
config_note: 'Runner: scratch/e25_triplet_degeneracy_premise.py -> logs/e25_triplet_degeneracy_premise.json.
  Pure CPU, ~1 min, no GPU, no model weights. Corpus git_sha e849eb7, all 11318 samples.


  All state values read through the PRODUCTION label path — load_sample_metadata,
  state_series, infer_state_label — never a re-implemented scatter, per the CLAUDE.md
  contract.


  Measures both the NEW per-epoch momentary series and the INCUMBENT day-reduced label
  side by side, so the delta attributable to a7fc419 is explicit rather than inferred.


  SEQUENCING DISCLOSED: the probe ran BEFORE the hypothesis was registered. Its gate
  thresholds are taken from the PROBLEM RECORD''s own prior claims (0.991 single-state,
  100% purity), not chosen after seeing the result. Recorded as a post-hoc formalisation
  rather than presented as a blind preregistration.'
cost: null
crash_reason: ''
created_at: '2026-08-15T07:46:10+00:00'
diagnostics_measured:
- after: 0.8194
  before: 0.991
  diagnostic_id: eiv_affect_multichannel_increment
- after: 0.5455
  before: 1.0
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
do_not_retry: []
external_run: logs/e25_triplet_degeneracy_premise.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_single_state_share_below_claim
  passed: true
- evidence: ''
  gate_id: g2_states_span_multiple_datasets
  passed: true
git_ref: ''
hypothesis_id: hyp-e25-triplet-degeneracy-fixed-20260815
id: exp-e25-triplet-degeneracy-premise-20260815
interpretation:
  at: '2026-08-18T21:08:41+00:00'
  failure_mode: claim_false
  lesson: 'Read the headline, not the verdict: ''refutes'' here refers to the PARKED
    PROBLEM''s two blocking premises being false, and both preregistered gates PASSED
    (the mechanical refutation comes from the tracked diagnostic moving down, 0.991
    -> 0.8194). Claim 1 ''99.1% of windows carry a single state'' is false — of 7054
    labelled windows (62.33% of 11318 samples) 5780 carry one state, 1175 two and
    99 three, i.e. 1274 transition-bearing windows = 18.06%, a ~20x increase over
    the ~0.9% implied. Claim 2 ''100%-pure dataset identifier'' is false — flow spans
    6 datasets at purity 0.5455 (n=2858) and stress spans 8 at 0.4751 (n=2387), so
    roughly half of same-state positive pairs are cross-dataset and the triplet margin
    is no longer forced to encode provenance; the per-epoch series is strictly broader
    than the day-reduced label (flow 4->6, stress 7->8 datasets), so majority-collapsing
    was destroying supply. This is a LABEL-SUPPLY measurement only: it licenses a
    Phase B triplet arm as non-vacuous, it does not predict that retraining helps
    (and exp-e28b later showed it does not).'
  transfers_to:
  - hyp-e26-within-window-transitions-20260815
  - hyp-e28-segment-triplet-retrain-20260815
  - hyp-e24-lab-protocol-subdaily-state-20260815
key_numbers:
  day_flow_n_datasets: 4.0
  day_flow_purity: 0.6045
  day_stress_n_datasets: 7.0
  day_stress_purity: 0.3834
  epoch_flow_n: 2858.0
  epoch_flow_n_datasets: 6.0
  epoch_flow_purity: 0.5455
  epoch_stress_n: 2387.0
  epoch_stress_n_datasets: 8.0
  epoch_stress_purity: 0.4751
  frac_labelled_windows: 0.6233
  n_labelled_windows: 7054.0
  n_samples: 11318.0
  n_transition_windows: 1274.0
  single_state_share_claimed: 0.991
  single_state_share_measured: 0.8194
  transition_share_measured: 0.1806
observed_effects:
- delta: -0.1716
  diagnostic_id: eiv_affect_multichannel_increment
  direction: down
- delta: -0.4545
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  direction: down
parent_experiment_id: exp-e24-lab-cohorts-20260815
prediction_match:
  overall: false
  per_diagnostic:
    eiv_affect_multichannel_increment: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-e25-triplet-degeneracy-fixed-20260815]].
Branches from [[exp-e24-lab-cohorts-20260815]].
External run: logs/e25_triplet_degeneracy_premise.json

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.991 → after 0.8194  (Δ -0.1716, down ✗ predicted up)
- [[eiv-dataset-provenance-shortcut-balacc]]: before 1.0 → after 0.5455  (Δ -0.4545, down)

## Gates
- `g1_single_state_share_below_claim`: PASS
- `g2_states_span_multiple_datasets`: PASS

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

Read the headline, not the verdict: 'refutes' here refers to the PARKED PROBLEM's two blocking premises being false, and both preregistered gates PASSED (the mechanical refutation comes from the tracked diagnostic moving down, 0.991 -> 0.8194). Claim 1 '99.1% of windows carry a single state' is false — of 7054 labelled windows (62.33% of 11318 samples) 5780 carry one state, 1175 two and 99 three, i.e. 1274 transition-bearing windows = 18.06%, a ~20x increase over the ~0.9% implied. Claim 2 '100%-pure dataset identifier' is false — flow spans 6 datasets at purity 0.5455 (n=2858) and stress spans 8 at 0.4751 (n=2387), so roughly half of same-state positive pairs are cross-dataset and the triplet margin is no longer forced to encode provenance; the per-epoch series is strictly broader than the day-reduced label (flow 4->6, stress 7->8 datasets), so majority-collapsing was destroying supply. This is a LABEL-SUPPLY measurement only: it licenses a Phase B triplet arm as non-vacuous, it does not predict that retraining helps (and exp-e28b later showed it does not).

Transfers to: [[hyp-e26-within-window-transitions-20260815]], [[hyp-e28-segment-triplet-retrain-20260815]], [[hyp-e24-lab-protocol-subdaily-state-20260815]]

## Key numbers
- `day_flow_n_datasets`: 4
- `day_flow_purity`: 0.6045
- `day_stress_n_datasets`: 7
- `day_stress_purity`: 0.3834
- `epoch_flow_n`: 2858
- `epoch_flow_n_datasets`: 6
- `epoch_flow_purity`: 0.5455
- `epoch_stress_n`: 2387
- `epoch_stress_n_datasets`: 8
- `epoch_stress_purity`: 0.4751
- `frac_labelled_windows`: 0.6233
- `n_labelled_windows`: 7054
- `n_samples`: 11318
- `n_transition_windows`: 1274
- `single_state_share_claimed`: 0.991
- `single_state_share_measured`: 0.8194
- `transition_share_measured`: 0.1806

## Notes
BOTH BLOCKING PREMISES OF THE PARKED PROBLEM ARE OBSOLETE. Phase B triplet retraining is now a JUSTIFIED arm.

CLAIM 1 '99.1% single state' -> MEASURED 0.8194. Of 7054 labelled windows: 5780 carry one state, 1175 carry two, 99 carry three. That is 1274 TRANSITION-BEARING windows (18.06%) against the ~0.9% the record implied — a 20x increase. Those windows are the valuable ones: a state change within ONE subject on ONE day, where between-subject trait confounding is absent by construction. E16/E17/E18 established that trait dominance is what buries cross-subject affect (per-user constant 0.7381 vs best physiological 0.5818); transition-bearing windows are the natural design that removes it.

CLAIM 2 '100%-pure dataset identifier' -> MEASURED 0.4751-0.5455. flow spans 6 datasets (purity 0.5455, n=2858); stress spans 8 (purity 0.4751, n=2387). About half of all same-state positive pairs are CROSS-DATASET, so the triplet margin is no longer forced to encode provenance. This was the load-bearing objection and it is gone.

The per-epoch series is also strictly broader than the day reduction (flow 4->6 datasets, stress 7->8), because majority-collapsing a window discards the minority states contributed by other cohorts. So a7fc419 did not merely re-time the labels — it recovered supply that infer_state_label was destroying.

WHY THIS MATTERS NOW RATHER THAN IN THE ABSTRACT. exp-e24 (same session) found real state signal where labels are dense: WESAD AUC 0.8497 vs clock 0.5640, neuro_stress 0.6806 vs 0.4930, subject-grouped. So Phase B now has BOTH a non-degenerate objective AND cohorts where the target demonstrably carries a large effect. Those two facts did not previously coexist.

SCOPE, STATED PLAINLY. This is a LABEL-SUPPLY measurement. It shows the triplet objective CAN now be non-vacuous; it does NOT show that retraining Phase B improves anything. That requires the actual retrain, scored on held-out subjects against the clock-only and per-user-constant controls this line has established, and preregistered before launch.

PROCESS: fourth premise re-check today (E20 refuted, E21 weakened, E23's MDE false, E25 obsolete). Every one cost CPU minutes; two would have been GPU-hours misspent. Sequencing disclosed honestly — this probe ran before its hypothesis was registered, with gate thresholds taken from the problem record's own prior claims rather than chosen after seeing the result.

#wearables #baseline #control #contrastive #dataset-whitelist #collapse #optihealth-eiv #data-contract-violation #matched-baseline
