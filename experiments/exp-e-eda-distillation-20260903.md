---
artifacts:
- logs/g5_edadistill.json
- logs/emission_v41edadistill_heldout_summary.json
- logs/e_geometry.json
- logs/e_phase_a.log
- outputs/eda_teacher/teacher_report.json
- scratch/run_e.sh
- scratch/e_train_eda_teacher.py
closed: true
config_note: 'scratch/run_e.sh, full A->B->C chain. ONE VARIABLE against v41rebased,
  asserted by the driver: phase_a gains privileged_distill_weight 0.1, privileged_teacher
  outputs/eda_teacher/teacher.pt, privileged_private_recon_weight 0.05; phase_b and
  phase_c change only split->train. Same PL60/ST30 geometry, roster, seed 42, sensor_micro_batches
  4. Teacher: scratch/e_train_eda_teacher.py, a 24k-parameter 2-layer conv over the
  per-patch eda_tonic trace (by_subject fit per exp-t3), masked-reconstruction pretrained
  on 785 EDA-bearing windows across 8 corpora, frozen before the student starts; its
  embedding has effective rank 3.0 at d=64. Student: `fused` pooled over channel AND
  patch axes, first 96 of 192 dims projected to the teacher''s 64 and matched with
  SmoothL1 on windows carrying >=4 tonic patches; the remaining 96 dims decode the
  window''s own channel means (the private/reconstruction path PULSE''s ablations
  name as what prevents collapse). DEVIATION FROM THE PREREGISTRATION, stated before
  the numbers: run_phase_b_orthogonalization takes no privileged parameters, so distillation
  is applied in PHASE A ONLY and Phase B inherits the encoder. TWO EARLIER LAUNCHES
  DIED IN MY OWN CODE: the teacher module parsed argv at import time and consumed
  `eiv train phase-a ...`; then the bridge pooled fused over the channel axis only,
  leaving the patch axis (mat1/mat2 4324x192 vs 96x64).'
cost:
  gpu_hours: 2.3
  wall_hours: 2.4
crash_reason: ''
created_at: '2026-09-03T02:26:57+00:00'
diagnostics_measured:
- after: 0.1558
  before: 0.1649
  diagnostic_id: eiv_state_auc_pool_ceiling
- after: 0.0118
  before: 0.034
  diagnostic_id: eiv_state_auc_trained_minus_random
- after: 9.1451
  before: 9.2609
  diagnostic_id: eiv_effective_rank
- after: 3.164
  before: 2.843
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
- after: 1.064
  before: 0.992
  diagnostic_id: eiv_gen_between_sample_std
- after: 0.0634
  before: 0.0852
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry:
- Do not re-run PULSE-style EDA distillation at weight 0.1 into a positionally-split
  shared subspace of `fused`, Phase A only, with this teacher. Every cell moved DOWN
  (union_ms/gb -0.0091, enc_ms/gb -0.0218, trained-minus-twin 0.0340 -> 0.0118) and
  split-honest hr_raw went 2.843 -> 3.164, outside the measured seed band. It is not
  a collapse (eff_rank 9.15) and not a config error (one-variable assertion passed).
- Do not blame the private/reconstruction path or representational collapse for this
  null. eff_rank is 9.145 against v41's 9.261 -- PULSE's named failure mode did not
  occur, so an ablation of the private path is not the follow-up.
- Do not distil from a teacher whose embedding has effective rank 3.0 at d=64 without
  first asking what a rank-3 target can teach a 96-dimensional subspace. That number
  was measured and written into teacher_report.json BEFORE the student ran, and it
  should have been treated as a precondition rather than a curiosity.
external_run: logs/g5_edadistill.json
former_ids: []
gate_results:
- evidence: eff_rank 9.1451 after Phase A against the >= 7.0 veto, essentially unchanged
    from v41rebased's 9.2609. The shared/private split did not collapse the representation,
    so PULSE's stated failure mode -- the one the private reconstruction path exists
    to prevent -- did not occur. B and C were correctly spent.
  gate_id: g0
  passed: true
- evidence: THE PRIMARY GATE, and it fails in the wrong direction. union_ms/gb +0.1558
    against a bar of >= +0.19; it did not merely fail to rise, it FELL from v41's
    +0.1649. Every union and encoder cell moves the same way (union/gb 0.1586 -> 0.1441,
    enc_ms/gb 0.0852 -> 0.0634, enc_ms/ridge 0.0772 -> 0.0635, enc_v41/gb 0.0803 ->
    0.0654) while the random twin is unmoved (0.0512 -> 0.0516), which is the signature
    of a real effect of the treatment rather than fold noise -- the fold-seed sd on
    this cell is 0.0155 and the union drop is 0.0091, but the encoder-only drop is
    0.0218.
  gate_id: g1
  passed: false
- evidence: 'enc_E minus its own twin, multi-span gb: +0.0118 against a bar of >=
    +0.065, and against v41''s +0.0340. Distillation removed roughly two thirds of
    the (small) state signal the caption objective had put into the encoder.'
  gate_id: g2
  passed: false
- evidence: 'split-honest hr_raw 3.164 bpm against the measured two-seed band 2.696
    +/- 0.416 (upper bound 3.112) -- outside it, by 0.052. scale 1.064 is in [0.85,1.35]
    and corr is 0.905, so this is a genuine regression in the value rather than a
    calibration artefact. Adapter provenance verified: split train, seed 42, 5,889
    rows. hr_peak 12.970 looks better than v41''s 14.288 but its scale is 0.688, out
    of band, so it is not creditable.'
  gate_id: g3
  passed: false
- evidence: 'One variable, asserted by the driver before launch: phase_a differs from
    configs/training.yaml on exactly {privileged_distill_weight, privileged_private_recon_weight,
    privileged_teacher, split} and phase_b / phase_c on {split} alone. Same PL60/ST30
    geometry, roster, seed 42, sensor_micro_batches 4. All three stages completed
    rc=0 with zero tracebacks.'
  gate_id: g4
  passed: true
git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
hypothesis_id: e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling
id: exp-e-eda-distillation-20260903
interpretation:
  at: '2026-09-03T02:28:13+00:00'
  failure_mode: mechanism_wrong
  lesson: 'PULSE''s design does not transfer to EiV at this teacher quality, and the
    reason was sitting in the teacher''s own report before the student ran: the EDA
    teacher''s embedding has EFFECTIVE RANK 3.0 at d=64, trained on 785 EDA-bearing
    windows. Matching 96 of the encoder''s 192 dimensions to a rank-3 target spends
    half the representation reproducing three directions, and every trained cell fell
    for it -- union_ms/gb 0.1649 -> 0.1558, enc_ms/gb 0.0852 -> 0.0634, trained-minus-twin
    0.0340 -> 0.0118, split-honest hr_raw 2.843 -> 3.164 (outside the measured +/-0.416
    band). The random twin did not move (0.0512 -> 0.0516), so this is the treatment
    and not fold noise. Crucially it is NOT the failure PULSE warns about: eff_rank
    9.145 vs 9.261 means no collapse, so ablating the private/reconstruction path
    is not the follow-up. Yang 2022''s non-monotone hazard is usually told as "the
    privileged teacher is too predictive"; this is the opposite end of the same curve
    -- a teacher too impoverished to be worth half a representation. THE GENERAL RULE
    TO CARRY: before distilling from a teacher, measure the effective rank and the
    supply of its embedding and compare them to the width of the subspace you intend
    to match; a rank-3 teacher and a 96-dim shared subspace is a mismatch you can
    see for free. WHAT IS NOT REFUTED: EDA as a privileged signal (exp-t3 stands --
    eda_tonic on the by_subject fit carries state, not corpus identity), and representation-level
    distillation as a family (Apple arXiv:2412.11276 and PhysioOmni arXiv:2504.19596
    both work, from teachers trained on 20M minutes / 172K participants rather than
    785 windows). What is refuted is THIS teacher into THIS subspace at THIS weight
    in Phase A. Any successor is a new hypothesis and, per exp-c1, must be judged
    on HELD-OUT per-cohort numbers rather than the clean-pool union -- which is itself
    a WESAD/LifeSnaps mixture and so a poor gate to have chosen.'
  transfers_to:
  - h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale
  - c3b-caption-label-clauses-are-what-put-state-in-the-encoder
key_numbers:
  distill_weight: 0.1
  eff_rank: 9.1451
  enc_ms_gb: 0.0634
  enc_ms_gb_v41: 0.0852
  enc_trained_minus_twin: 0.0118
  enc_trained_minus_twin_v41: 0.034
  hr_peak_mae: 12.97
  hr_peak_scale: 0.688
  hr_raw_band_upper: 3.112
  hr_raw_mae: 3.164
  hr_raw_scale: 1.064
  private_recon_weight: 0.05
  teacher_effective_rank: 3.0
  teacher_windows: 785.0
  twin_unmoved_E: 0.0516
  twin_unmoved_v41: 0.0512
  union_ms_gb: 0.1558
  union_ms_gb_v41: 0.1649
observed_effects:
- delta: -0.0091
  diagnostic_id: eiv_state_auc_pool_ceiling
  direction: down
- delta: -0.0222
  diagnostic_id: eiv_state_auc_trained_minus_random
  direction: down
- delta: -0.1158
  diagnostic_id: eiv_effective_rank
  direction: down
- delta: 0.321
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: up
- delta: 0.072
  diagnostic_id: eiv_gen_between_sample_std
  direction: up
- delta: -0.0218
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: down
parent_experiment_id: exp-c1-heldout-pool-ceiling-20260903
prediction_match:
  overall: false
  per_diagnostic:
    eiv_effective_rank: false
    eiv_splithonest_heldout_hr_raw_mae_bpm: false
    eiv_state_auc_pool_ceiling: false
    eiv_state_auc_trained_minus_random: false
provenance: {}
recipe_fingerprint:
  git_ref: fix/soft-token-norm-scale@ad78bc2+dirty
verdict: refutes
---

## Summary
Tests hypothesis [[e-pulse-style-eda-teacher-distillation-raises-the-union-state-ceiling]].
Branches from [[exp-c1-heldout-pool-ceiling-20260903]].
At commit `fix/soft-token-norm-scale@ad78bc2+dirty`.
External run: logs/g5_edadistill.json

## Measurements
- [[eiv_state_auc_pool_ceiling]]: before 0.1649 → after 0.1558  (Δ -0.0091, down ✗ predicted up)
- [[eiv_state_auc_trained_minus_random]]: before 0.034 → after 0.0118  (Δ -0.0222, down ✗ predicted up)
- [[eiv_effective_rank]]: before 9.2609 → after 9.1451  (Δ -0.1158, down ✗ predicted none)
- [[eiv_splithonest_heldout_hr_raw_mae_bpm]]: before 2.843 → after 3.164  (Δ +0.321, up ✗ predicted none)
- [[eiv_gen_between_sample_std]]: before 0.992 → after 1.064  (Δ +0.072, up)
- [[eiv_momentary_state_auc_over_null]]: before 0.0852 → after 0.0634  (Δ -0.0218, down)

## Gates
- `g0`: PASS — eff_rank 9.1451 after Phase A against the >= 7.0 veto, essentially unchanged from v41rebased's 9.2609. The shared/private split did not collapse the representation, so PULSE's stated failure mode -- the one the private reconstruction path exists to prevent -- did not occur. B and C were correctly spent.
- `g1`: **FAIL** — THE PRIMARY GATE, and it fails in the wrong direction. union_ms/gb +0.1558 against a bar of >= +0.19; it did not merely fail to rise, it FELL from v41's +0.1649. Every union and encoder cell moves the same way (union/gb 0.1586 -> 0.1441, enc_ms/gb 0.0852 -> 0.0634, enc_ms/ridge 0.0772 -> 0.0635, enc_v41/gb 0.0803 -> 0.0654) while the random twin is unmoved (0.0512 -> 0.0516), which is the signature of a real effect of the treatment rather than fold noise -- the fold-seed sd on this cell is 0.0155 and the union drop is 0.0091, but the encoder-only drop is 0.0218.
- `g2`: **FAIL** — enc_E minus its own twin, multi-span gb: +0.0118 against a bar of >= +0.065, and against v41's +0.0340. Distillation removed roughly two thirds of the (small) state signal the caption objective had put into the encoder.
- `g3`: **FAIL** — split-honest hr_raw 3.164 bpm against the measured two-seed band 2.696 +/- 0.416 (upper bound 3.112) -- outside it, by 0.052. scale 1.064 is in [0.85,1.35] and corr is 0.905, so this is a genuine regression in the value rather than a calibration artefact. Adapter provenance verified: split train, seed 42, 5,889 rows. hr_peak 12.970 looks better than v41's 14.288 but its scale is 0.688, out of band, so it is not creditable.
- `g4`: PASS — One variable, asserted by the driver before launch: phase_a differs from configs/training.yaml on exactly {privileged_distill_weight, privileged_private_recon_weight, privileged_teacher, split} and phase_b / phase_c on {split} alone. Same PL60/ST30 geometry, roster, seed 42, sensor_micro_batches 4. All three stages completed rc=0 with zero tracebacks.

**Verdict:** refutes

## Interpretation
**Failure mode:** `mechanism_wrong`

PULSE's design does not transfer to EiV at this teacher quality, and the reason was sitting in the teacher's own report before the student ran: the EDA teacher's embedding has EFFECTIVE RANK 3.0 at d=64, trained on 785 EDA-bearing windows. Matching 96 of the encoder's 192 dimensions to a rank-3 target spends half the representation reproducing three directions, and every trained cell fell for it -- union_ms/gb 0.1649 -> 0.1558, enc_ms/gb 0.0852 -> 0.0634, trained-minus-twin 0.0340 -> 0.0118, split-honest hr_raw 2.843 -> 3.164 (outside the measured +/-0.416 band). The random twin did not move (0.0512 -> 0.0516), so this is the treatment and not fold noise. Crucially it is NOT the failure PULSE warns about: eff_rank 9.145 vs 9.261 means no collapse, so ablating the private/reconstruction path is not the follow-up. Yang 2022's non-monotone hazard is usually told as "the privileged teacher is too predictive"; this is the opposite end of the same curve -- a teacher too impoverished to be worth half a representation. THE GENERAL RULE TO CARRY: before distilling from a teacher, measure the effective rank and the supply of its embedding and compare them to the width of the subspace you intend to match; a rank-3 teacher and a 96-dim shared subspace is a mismatch you can see for free. WHAT IS NOT REFUTED: EDA as a privileged signal (exp-t3 stands -- eda_tonic on the by_subject fit carries state, not corpus identity), and representation-level distillation as a family (Apple arXiv:2412.11276 and PhysioOmni arXiv:2504.19596 both work, from teachers trained on 20M minutes / 172K participants rather than 785 windows). What is refuted is THIS teacher into THIS subspace at THIS weight in Phase A. Any successor is a new hypothesis and, per exp-c1, must be judged on HELD-OUT per-cohort numbers rather than the clean-pool union -- which is itself a WESAD/LifeSnaps mixture and so a poor gate to have chosen.

Transfers to: [[h-reconstruction-pretraining-does-not-clear-the-sensorfm-bar-at-eiv-scale]], [[c3b-caption-label-clauses-are-what-put-state-in-the-encoder]]

## Key numbers
- `distill_weight`: 0.1
- `eff_rank`: 9.1451
- `enc_ms_gb`: 0.0634
- `enc_ms_gb_v41`: 0.0852
- `enc_trained_minus_twin`: 0.0118
- `enc_trained_minus_twin_v41`: 0.034
- `hr_peak_mae`: 12.97
- `hr_peak_scale`: 0.688
- `hr_raw_band_upper`: 3.112
- `hr_raw_mae`: 3.164
- `hr_raw_scale`: 1.064
- `private_recon_weight`: 0.05
- `teacher_effective_rank`: 3
- `teacher_windows`: 785
- `twin_unmoved_E`: 0.0516
- `twin_unmoved_v41`: 0.0512
- `union_ms_gb`: 0.1558
- `union_ms_gb_v41`: 0.1649

## Artifacts
- `logs/g5_edadistill.json`
- `logs/emission_v41edadistill_heldout_summary.json`
- `logs/e_geometry.json`
- `logs/e_phase_a.log`
- `outputs/eda_teacher/teacher_report.json`
- `scratch/run_e.sh`
- `scratch/e_train_eda_teacher.py`

## Do not retry
- Do not re-run PULSE-style EDA distillation at weight 0.1 into a positionally-split shared subspace of `fused`, Phase A only, with this teacher. Every cell moved DOWN (union_ms/gb -0.0091, enc_ms/gb -0.0218, trained-minus-twin 0.0340 -> 0.0118) and split-honest hr_raw went 2.843 -> 3.164, outside the measured seed band. It is not a collapse (eff_rank 9.15) and not a config error (one-variable assertion passed).
- Do not blame the private/reconstruction path or representational collapse for this null. eff_rank is 9.145 against v41's 9.261 -- PULSE's named failure mode did not occur, so an ablation of the private path is not the follow-up.
- Do not distil from a teacher whose embedding has effective rank 3.0 at d=64 without first asking what a rank-3 target can teach a 96-dimensional subspace. That number was measured and written into teacher_report.json BEFORE the student ran, and it should have been treated as a precondition rather than a curiosity.

## Notes
DISTILLATION MADE EVERYTHING SLIGHTLY WORSE, CONSISTENTLY, AND NOT BY COLLAPSE.

  cell               v41rebased      E      delta
  union_ms/gb          +0.1649   +0.1558   -0.0091
  union/gb             +0.1586   +0.1441   -0.0144
  enc_ms/gb            +0.0852   +0.0634   -0.0218
  enc_ms/ridge         +0.0772   +0.0635   -0.0137
  enc_v41/gb           +0.0803   +0.0654   -0.0149
  enc_ms@random/gb     +0.0512   +0.0516   +0.0004   <- twin unmoved, as it must be
  trained minus twin   +0.0340   +0.0118   -0.0222
  split-honest hr_raw   2.843     3.164    +0.321 (band 2.696 +/- 0.416)

Every trained cell falls and the untouched twin does not, which is the signature of a real treatment effect rather than fold noise. All four blocking gates fail; g0 (geometry) and g4 (one variable) pass.

WHY THIS IS A CLEAN NEGATIVE AND NOT A BROKEN RUN. eff_rank 9.145 vs 9.261 -- the shared/private split did not collapse the representation, which is the failure PULSE's ablations warn about and the private reconstruction path exists to prevent. The driver asserted a one-variable config. All three stages ran rc=0. The teacher was frozen before the student started and never saw the 12 wrist channels.

THE MOST LIKELY MECHANISM, and it was measurable before the run: the teacher's embedding has EFFECTIVE RANK 3.0 at d=64 (outputs/eda_teacher/teacher_report.json, written before Phase A). Matching 96 of the encoder's 192 dimensions to a rank-3 target is mostly a constraint, not an education -- it spends half the representation reproducing three directions. Yang 2022's non-monotone hazard is usually told as "the teacher is too predictive"; this is the other end, a teacher too impoverished to be worth half a representation. The teacher is also thin in supply: 785 EDA-bearing windows over 8 corpora against the roster's ~9.4k.

WHAT WOULD BE A DIFFERENT EXPERIMENT RATHER THAN A RETRY: a teacher with a richer objective (contrastive across subjects rather than masked reconstruction of a slowly-varying tonic trace), a much smaller shared subspace matched to its actual rank, or distillation in Phase B where the triplet objective could use it. None of these is licensed by this result -- they are new hypotheses, and exp-c1 says any of them must be judged on HELD-OUT per-cohort numbers, since the clean-pool union this arm was gated on is itself a WESAD/LifeSnaps mixture.

#optihealth #state-supervision #wearables #readout #control #baseline #optihealth-eiv #representation-learning #matched-baseline
