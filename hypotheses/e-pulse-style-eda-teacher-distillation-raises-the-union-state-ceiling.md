---
ambition_class: mechanism
created_at: '2026-09-02T20:56:13+00:00'
former_ids: []
gap_closure_estimate: 0.4
gates:
- blocking: true
  description: eff_rank >= 7.0 after Phase A (veto before B and C are spent)
  diagnostic_id: eiv_effective_rank
  id: g0
  mde: null
- blocking: true
  description: union_ms(E)/gb over_null >= +0.19 on the g5 clean pool, both families,
    own nulls, random twin in the same invocation
  diagnostic_id: eiv_state_auc_pool_ceiling
  id: g1
  mde: 0.025
- blocking: true
  description: enc_E trained-minus-twin (multi-span gb) >= +0.065
  diagnostic_id: eiv_state_auc_trained_minus_random
  id: g2
  mde: 0.0141
- blocking: true
  description: split-honest hr_raw on the 128 rows within the seed-family k=2 band
    of 2.843; scale in [0.85,1.35]
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  id: g3
  mde: null
- blocking: true
  description: 'one variable: identical v41pl60 profiles with split=train, seed 42,
    plus ONLY the distillation flag; config diff asserted by the driver before launch'
  diagnostic_id: eiv_effective_rank
  id: g4
  mde: null
id: e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling
interventions: []
override_reason: ''
paper_refs:
- pulse-2025-privileged-eda-teacher-2510-24058
- yang-2022-privileged-features-distillation-2209-08754
- lopez-paz-2015-generalized-distillation-1511-03643
- amin-2025-stress-reproducibility-consumer-wearables-2505-05694
predicted_effects:
- diagnostic_id: eiv_state_auc_pool_ceiling
  direction: up
  magnitude_note: 'union_ms(E)/gb >= +0.19 on the g5 clean pool (v41 +0.1649); delta
    >= +0.025. THIS is the primary: encoder-only gains that leave the union flat are
    null.'
- diagnostic_id: eiv_state_auc_trained_minus_random
  direction: up
  magnitude_note: enc_E multi-span gb trained-minus-twin >= +0.065 (v41 +0.0340).
- diagnostic_id: eiv_effective_rank
  direction: none
  magnitude_note: '>= 7.0 veto; expect 9-10 like v41 (9.26) and s43 (10.18) if the
    private/reconstruction path does its job.'
- diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: none
  magnitude_note: within the seed-family band of 2.843; HR must not pay for state.
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
problem_tags:
- optihealth-eiv
- representation-learning
- matched-baseline
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- optihealth
- state-supervision
- wearables
- readout
- control
- baseline
---

## Statement
TRACK E (GPU, conditional on C-1 confirming the ceiling transfers). Distilling a frozen EDA TEACHER's representation into a shared subspace of the PL60 encoder -- PULSE's design (arXiv:2510.24058): a small EDA encoder trained on privileged_events.parquet (eda_tonic, by_subject fit, per exp-t3), the sensor encoder split into shared + private embeddings, the shared subspace matched to the teacher's pooled and hidden states on windows where EDA exists, the private path unchanged, distillation weight <= 0.1 -- raises what the ENCODER carries about momentary state AND raises the UNION ceiling. Committed predictions, on the g5 clean pool with random twin and both families: (1) enc_E multi-span gb trained-minus-twin >= +0.065 (v41: +0.0340; delta >= +0.03, outside the band); (2) union_ms(E)/gb over_null >= +0.19 (v41: +0.1649; delta >= +0.025) -- the condition that matters, because if EDA only teaches what 45 HR/HRV features already know the union does not move and the arm is null even when (1) passes; (3) g0 eff_rank >= 7.0, g1/g2 HR gates unchanged (split-honest hr_raw within the seed-family band of 2.843, scale in [0.85,1.35]). NOT the per-patch EDA value-regression head the 09-01 plan sketched: that is the design Yang 2022 warns about and exp-055 already showed the encoder's tokens do not recover per-patch EDA even at 14-corpus supply.

## Rationale
exp-t3 (supports): EDA is a state signal, not a corpus fingerprint, but only as eda_tonic on the by_subject fit (conditional MI over null flat across fits while I(EDA;dataset) collapses 6.3x). exp-055 (refuted supply-limited claim): the trained encoder's tokens do NOT carry per-patch EDA even with 1.1M privileged rows over 934 subjects -- so the information has to be PUT in, and a regression head on the value is the route with a measured null. PULSE puts it in at the representation level and reports WESAD LOSO 0.965 STRESS without EDA at inference, matching the with-EDA model; its ablations say the private/reconstruction path is what prevents collapse, which is this project's recurring failure (eff_rank vetoes, v22/v29 heads costing -1.5 to -1.8). Amin 2025: EDA is what lifts wrist stress detection from 0.73 to 0.95, so the ceiling above 0.72 is where EDA lives. Yang 2022: distil the LESS predictive privileged signal at low weight -- tonic, not SCR rate, weight <= 0.1. Lopez-Paz 2015 is the method family.

## Plan
(1) CPU: train a small EDA teacher (1-D conv over per-minute eda_tonic by_subject-standardised, windows from privileged_events.parquet, self-supervised + state-probe sanity check). (2) Code, default-off flag privileged_distill: {teacher_ckpt, weight<=0.1, shared_dim, private_recon_weight} in Phase A/B; shared/private split on the fused representation; hidden-state + pooled matching on EDA-bearing windows only. (3) Driver: seed-family pattern (two-key config assertion extended to the flag), g0 veto, g5 with twin + both families, held-out capture. ~3-4 GPU-hours. Runs only after C-1 confirms and after Track A has been read.

## Links
Tests problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].
Predicts [[eiv_state_auc_pool_ceiling]] (up).
Predicts [[eiv_state_auc_trained_minus_random]] (up).
Predicts [[eiv_effective_rank]] (none).
Predicts [[eiv_splithonest_heldout_hr_raw_mae_bpm]] (none).
Gate `g0` (blocking): eff_rank >= 7.0 after Phase A (veto before B and C are spent)
Gate `g1` (blocking): union_ms(E)/gb over_null >= +0.19 on the g5 clean pool, both families, own nulls, random twin in the same invocation
Gate `g2` (blocking): enc_E trained-minus-twin (multi-span gb) >= +0.065
Gate `g3` (blocking): split-honest hr_raw on the 128 rows within the seed-family k=2 band of 2.843; scale in [0.85,1.35]
Gate `g4` (blocking): one variable: identical v41pl60 profiles with split=train, seed 42, plus ONLY the distillation flag; config diff asserted by the driver before launch
Cites [[pulse-2025-privileged-eda-teacher-2510-24058]], [[yang-2022-privileged-features-distillation-2209-08754]], [[lopez-paz-2015-generalized-distillation-1511-03643]], [[amin-2025-stress-reproducibility-consumer-wearables-2505-05694]].

#optihealth #state-supervision #wearables #readout #control #baseline #optihealth-eiv #representation-learning #matched-baseline
