---
created_at: '2026-08-14T07:58:04+00:00'
gates:
- blocking: true
  description: 'BLOCKING (existence at the encoder): subject-grouped OOF increment
    R2(peak | avg+max patch tokens) - R2(peak | true daily mean) >= 0.25 on the strict-clean
    1-min pool. Below that, the trained patch tokens have discarded within-patch extremes
    and a pooling-branch arm cannot recover them without retraining the encoder itself.'
  id: g1-avgmax-increment
- blocking: true
  description: 'BLOCKING (the branch adds over the incumbent): increment(avg+max)
    - increment(avg-pool) >= +0.10 on the same folds. If avg-pool already carries
    whatever is there, a max branch is dead weight.'
  id: g2-delta-over-avg
- blocking: false
  description: 'Informational: same pair computed on RAW-INPUT patch maxima (max of
    denormalized hr over each of the 23 patch windows, exact algebra) as the supply
    ceiling; and ridge MAE vs always-predict-median for engineering scale.'
  id: g3-supply-ceiling
id: hyp-e6-peak-maxpool-readout-20260814
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: 'Committed: avg+max increment lands in 0.15-0.45 and beats avg-pool
    by >= +0.08; genuine uncertainty on clearing the 0.25 bar because the encoder
    was TRAINED under mean-pool pressure at 120-min patches (patch MLP may have kept
    only level). The 15-min references (avg 0.1685 vs avg+max 0.2968 on excursion
    target) are from raw-ish patch features on the OLD grid and only weakly transfer.
    If the raw-input per-patch-max ceiling (g3) is high while both learned arms fail
    g1, that is the informative outcome: training, not architecture, discards the
    peak.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- pooling
- epoch-resolution
- medgemma
- collapse
- sensor-text
---

## Statement
H-E6 (encoder read-out): the v9band04 encoder's patch tokens still contain the daily-peak excursion at 1-minute resolution, and a max-pool readout branch recovers it — measured as eiv_peak_excursion_increment_r2 (avg+max concat over the 23 patch tokens) substantially above the avg-pool readout on the same strict-clean pool, subject-grouped.

## Rationale
Dissociation criterion (2026-08-13): hr_peak dense corr 0.17-0.35 across all v9band04 seeds with negative skill even on dense -> representation never carried the peak -> target-law levers RULED OUT (filed); justified lever is the encoder excursion/max-pool branch. Caption ladder agrees: level 0.023 -> hand dispersion 0.708 -> bound 0.971. Exact algebra stands: max over 23 patch maxima == true daily peak to 0.0000 bpm on all 6624 days, so SUPPLY through the patch partition is perfect; the open question is whether the TRAINED patch embeddings retain it linearly. eiv_peak_excursion_increment_r2 is the preregistered gate metric for exactly this class of change (window-shortening trap immunity), and it is CPU-cheap, so it must run before any Phase-C GPU arm. Runbook lineage: 'patch construction part (b) learned pool->sensor_slots untested at 1-minute' (retraction record 2026-08-08).

## Plan
E6 CPU/GPU-cheap probe scratch/e6_peak_maxpool_probe.py. Load v9band04_s42 phase_c sensor_modules (encoder+fusion, manifest sensor_config; with_presence_v1, input_dim 24). Forward data/eval_v3_1min strict-clean pool (hr_cov>0.9, day mean>40 bpm raw, up to 4000 samples). Features from POST-FUSION hr-channel patch tokens [23,192]: arm A avg over patches (192-d), arm B avg+max per-dim concat (384-d); secondary: all-channel mean variant. RidgeCV, GroupKFold(5) by subject, OOF R2 against raw daily peak; control R2(peak|true daily mean, 1 feature) on identical folds; increments = R2_repr - R2_mean. Supply ceiling: ridge on the 23 exact per-patch raw maxima. Output logs/e6_peak_maxpool_probe.json. Verdict routes: PASS g1+g2 -> queue GPU arm (max-pool branch into projector input / pooling change, Phase C retrain on v9band04 recipe, scored on eiv_gen_peak_skill_bpm with donor control). FAIL g1 with ceiling HIGH -> encoder training destroyed extremes; the arm must move to Phase A/B objective or raw-feature bypass branch, not Phase-C pooling. FAIL g1 with ceiling LOW too -> instrument/pool problem, stop and re-derive.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Gate `g1-avgmax-increment` (blocking): BLOCKING (existence at the encoder): subject-grouped OOF increment R2(peak | avg+max patch tokens) - R2(peak | true daily mean) >= 0.25 on the strict-clean 1-min pool. Below that, the trained patch tokens have discarded within-patch extremes and a pooling-branch arm cannot recover them without retraining the encoder itself.
Gate `g2-delta-over-avg` (blocking): BLOCKING (the branch adds over the incumbent): increment(avg+max) - increment(avg-pool) >= +0.10 on the same folds. If avg-pool already carries whatever is there, a max branch is dead weight.
Gate `g3-supply-ceiling` (advisory): Informational: same pair computed on RAW-INPUT patch maxima (max of denormalized hr over each of the 23 patch windows, exact algebra) as the supply ceiling; and ridge MAE vs always-predict-median for engineering scale.

#cross-modal #pooling #epoch-resolution #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
