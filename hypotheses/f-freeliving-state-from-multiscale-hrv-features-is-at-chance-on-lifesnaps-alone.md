---
ambition_class: reframe
created_at: '2026-09-03T06:46:25+00:00'
former_ids: []
gap_closure_estimate: 0.1
gates:
- blocking: true
  description: raw_multiscale/rf over_null on LifeSnaps-only rows >= +0.05 on BOTH
    the clean pool (all LifeSnaps subjects, GroupKFold) and the split-honest held-out
    pool (442 rows). Predicted to FAIL; a fail with |over_null| under mde is the powered
    null this hypothesis predicts.
  diagnostic_id: eiv_state_auc_over_null_freeliving
  id: g1
  mde: 0.031
- blocking: false
  description: 'Any arm at all (raw_shallow, raw_grid, enc_v41, enc_ms, union_ms,
    under any family) reads over_null >= +0.05 with perm_p <= 0.05 on LifeSnaps-only
    rows. Advisory: names which representation, if any, sees free-living state.'
  diagnostic_id: eiv_state_auc_over_null_freeliving
  id: g2
  mde: null
- blocking: true
  description: 'WESAD-only raw_multiscale/rf over_null >= +0.35 (the lab cell reproduces
    as a separate diagnostic). Blocking as a sanity check on the split itself: if
    the lab cell also collapses when fit alone, the device slice was an artefact of
    the mixture fit.'
  diagnostic_id: eiv_state_auc_over_null_lab
  id: g3
  mde: 0.031
- blocking: true
  description: 'Design: within-window permutation null per arm, fold-seed sd reported
    (--fold-seeds 5), rows built by the g5 harness byte-identically to exp-c1 and
    then filtered by dataset. No parameter is tuned on LifeSnaps.'
  diagnostic_id: eiv_state_auc_over_null_freeliving
  id: g4
  mde: null
id: f-freeliving-state-from-multiscale-hrv-features-is-at-chance-on-lifesnaps-alone
interventions: []
override_reason: ''
paper_refs:
- amin-2025-stress-reproducibility-consumer-wearables-2505-05694
- sensorfm-2026-2605-22759
predicted_effects:
- diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: up
  magnitude_note: 'raw_multiscale/rf on LifeSnaps-only rows: predicted <= +0.03 over
    null, i.e. inside k*sigma of the 0.0155 partition floor -> expect weak_signal/null.
    Clearing +0.05 (g1) would mean free-living state is NOT at chance and re-opens
    the lever search on this cell.'
- diagnostic_id: eiv_state_auc_over_null_lab
  direction: up
  magnitude_note: 'WESAD-only rows, same arm: predicted >= +0.35 over null (the pooled
    fit read 0.94-0.97 on this cohort).'
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
problem_tags:
- optihealth-eiv
- representation-learning
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- optihealth
- readout
- control
- baseline
---

## Statement
Scored on LifeSnaps ALONE (free-living EMA flow-vs-stress, subject-grouped folds, own within-window permutation null), the 15-feature multi-scale HR/HRV readout (raw_multiscale/rf, the arm that sets the pooled bar) reads momentary state above chance. PREDICTION: it does not — over_null lands at or under +0.03, inside 2 sigma of the measured partition floor (0.0155). The lab twin (WESAD alone) is predicted to stay at or above +0.35. If the free-living cell is a powered null at the FEATURE level, no encoder trained on the same HR/HRV input can be expected to read it either, and the free-living lever search moves to inputs (EDA at inference, context) rather than representations.

## Rationale
exp-c1-heldout-pool-ceiling-20260903 device slice: union_ms/gb reads WESAD 0.9728 (n=148) and LifeSnaps 0.4744 (n=442) on the same out-of-fold predictions, so the pooled +0.11 was a mixture. But that slice scored a classifier FIT ON THE MIXTURE, which can spend its capacity on the lab cohort; a within-LifeSnaps fit with its own null is the honest question. Amin 2025 (arXiv:2505.05694): wrist HRV-only stress detection is 0.73 LOSO on scripted stressors and collapses without EDA or a scripted induction. Rule-out that-changing-the-phase-a-objective-caption-d6b326: three Phase A objectives all sit under the handcrafted bar, so the feature-level cell is the ceiling any encoder must be measured against.

## Plan
scratch/g5_pool_ceiling.py --datasets lifesnaps (clean and heldout pools, --fold-seeds 5) and --datasets wesad (clean pool). CPU, ~30 min total. Record freeliving from the LifeSnaps runs, lab from the WESAD run, before = arm's null mean (0), after = over_null.

## Links
Tests problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].
Predicts [[eiv_state_auc_over_null_freeliving]] (up).
Predicts [[eiv_state_auc_over_null_lab]] (up).
Gate `g1` (blocking): raw_multiscale/rf over_null on LifeSnaps-only rows >= +0.05 on BOTH the clean pool (all LifeSnaps subjects, GroupKFold) and the split-honest held-out pool (442 rows). Predicted to FAIL; a fail with |over_null| under mde is the powered null this hypothesis predicts.
Gate `g2` (advisory): Any arm at all (raw_shallow, raw_grid, enc_v41, enc_ms, union_ms, under any family) reads over_null >= +0.05 with perm_p <= 0.05 on LifeSnaps-only rows. Advisory: names which representation, if any, sees free-living state.
Gate `g3` (blocking): WESAD-only raw_multiscale/rf over_null >= +0.35 (the lab cell reproduces as a separate diagnostic). Blocking as a sanity check on the split itself: if the lab cell also collapses when fit alone, the device slice was an artefact of the mixture fit.
Gate `g4` (blocking): Design: within-window permutation null per arm, fold-seed sd reported (--fold-seeds 5), rows built by the g5 harness byte-identically to exp-c1 and then filtered by dataset. No parameter is tuned on LifeSnaps.
Cites [[amin-2025-stress-reproducibility-consumer-wearables-2505-05694]], [[sensorfm-2026-2605-22759]].

#optihealth #readout #control #baseline #optihealth-eiv #representation-learning #matched-baseline
