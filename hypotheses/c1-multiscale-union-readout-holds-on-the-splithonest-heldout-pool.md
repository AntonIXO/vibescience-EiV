---
ambition_class: reframe
created_at: '2026-09-02T20:55:51+00:00'
former_ids: []
gap_closure_estimate: 0.5
gates:
- blocking: true
  description: union_ms/gb over_null >= +0.11 on the 1,485-row held-out pool, under
    BOTH ridge and gb families, each against its own permutation null (z >= 3 on each)
  diagnostic_id: eiv_state_auc_pool_ceiling
  id: g1
  mde: 0.03
- blocking: true
  description: raw_multiscale/rf over_null >= +0.10 on the same rows -- the no-encoder
    arm must carry most of the ceiling, or the claim 'shippable without the chain'
    is false
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g2
  mde: 0.03
- blocking: false
  description: random twin of the PL60 encoder scored in the SAME invocation under
    both families; reported beside every encoder cell (advisory)
  diagnostic_id: eiv_state_auc_trained_minus_random
  id: g3
  mde: null
- blocking: true
  description: 5 fold-seed repeats of the winning arm; sd registered on eiv_state_auc_pool_ceiling
    as noise_source=measured before any later arm is priced against it
  diagnostic_id: eiv_state_auc_pool_ceiling
  id: g4
  mde: null
id: c1-multiscale-union-readout-holds-on-the-splithonest-heldout-pool
interventions: []
override_reason: ''
paper_refs:
- amin-2025-stress-reproducibility-consumer-wearables-2505-05694
- prajod-2022-ecg-stress-generalizability-2210-06225
- marco-blanco-2025-rocket-random-projection-theory-2502-17061
predicted_effects:
- diagnostic_id: eiv_state_auc_pool_ceiling
  direction: up
  magnitude_note: 'union_ms/gb on the 1,485-row held-out pool: over_null +0.13 +/-
    0.03 (AUC >= 0.66). Gate: >= +0.11 under both families. Below +0.06 on either
    family = the clean-pool ceiling does not transfer and the slot stays unshippable.'
- diagnostic_id: eiv_state_auc_trained_minus_random
  direction: none
  magnitude_note: 'encoder-only multi-span gb cell: positive but inside +/-0.03 on
    the held-out pool; the encoder is not what carries the ceiling.'
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: raw_multiscale/rf +0.12 +/- 0.03 on the held-out pool.
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- representation-learning
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- optihealth
- readout
- control
- baseline
---

## Statement
TRACK C-1. The g5 pool ceiling is a property of the labels, not of the clean pool it was measured on: scored on the 1,485-row split-honest held-out pool (data/eval_v3_1min val+test, subjects no stage of v41rebased trained on), the multi-scale handcrafted readout raw_multiscale/rf keeps over_null >= +0.10 and union_ms/gb keeps over_null >= +0.11 (AUC >= 0.66), under BOTH estimator families with their own nulls and the encoder's random twin scored in the same invocation. Predicted magnitudes committed BEFORE the run: raw_multiscale/rf +0.12 +/- 0.03 (down from +0.1515 on the clean pool, because the held-out pool is smaller and carries fewer flow/stress-annotated windows); union_ms/gb +0.13 +/- 0.03; trained-minus-twin on the encoder-only multi-span gb cell stays positive but inside +/-0.03. Cross-device slice (dryad_ifh_affect, the only out-of-roster cohort) reported separately with its n. ALSO produces the first MEASURED sigma for eiv_state_auc_pool_ceiling: the winning arm repeated over 5 GroupKFold fold-seeds, sd registered as the floor.

## Rationale
The 09-01 plan pre-committed to 'drop the state slot' on a g3 null that turned out to be a single-family readout artefact; g5 showed the labels support AUC 0.72. But g5 ran on the CLEAN pool (3 of 21 corpora carry usable flow/stress minute labels) and everything in this project that was measured on one population and quoted on another has been withdrawn at least once. Amin 2025 (arXiv:2505.05694) reads 0.73 for wrist HRV-only mixed stressors and 0.723 when a pre-trained tool crosses to a different device, so a held-out number in the 0.66-0.70 range is the literature-consistent expectation, and anything below 0.60 would say the clean-pool ceiling was population-specific. Prajod & Andre 2022 (arXiv:2210.06225) is the reason the device slice is mandatory. CPU only; no GPU. If confirmed, the product's state slot has a shippable implementation today and the chain's own head is retired for state.

## Plan
CPU. Extend scratch/g5_pool_ceiling.py with --pool heldout (rows from data/eval_v3_1min val+test via the same split_subjects.json keys the scorer uses), --families ridge,gb, --fold-seeds 5, --device-slice. Arms: raw_shallow, raw_multiscale, union_ms, enc_ms, enc_ms@random. Output logs/g5_heldout_pool.json. Then register the measured sd on eiv_state_auc_pool_ceiling and record_diagnostics on this experiment. ~1 CPU-hour. No GPU.

## Links
Tests problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].
Predicts [[eiv_state_auc_pool_ceiling]] (up).
Predicts [[eiv_state_auc_trained_minus_random]] (none).
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Gate `g1` (blocking): union_ms/gb over_null >= +0.11 on the 1,485-row held-out pool, under BOTH ridge and gb families, each against its own permutation null (z >= 3 on each)
Gate `g2` (blocking): raw_multiscale/rf over_null >= +0.10 on the same rows -- the no-encoder arm must carry most of the ceiling, or the claim 'shippable without the chain' is false
Gate `g3` (advisory): random twin of the PL60 encoder scored in the SAME invocation under both families; reported beside every encoder cell (advisory)
Gate `g4` (blocking): 5 fold-seed repeats of the winning arm; sd registered on eiv_state_auc_pool_ceiling as noise_source=measured before any later arm is priced against it
Cites [[amin-2025-stress-reproducibility-consumer-wearables-2505-05694]], [[prajod-2022-ecg-stress-generalizability-2210-06225]], [[marco-blanco-2025-rocket-random-projection-theory-2502-17061]].

#optihealth #readout #control #baseline #optihealth-eiv #matched-baseline #subject-heldout #representation-learning
