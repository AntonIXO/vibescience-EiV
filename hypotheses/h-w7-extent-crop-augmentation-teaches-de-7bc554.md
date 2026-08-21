---
ambition_class: null
created_at: '2026-08-20T21:15:52+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: 'SYNTHETIC-CONTROL POWER CHECK FIRST, CPU-ONLY, BLOCKING. Before training:
    take DENSE lifesnaps/pmdata days where the answer is known, crop them to the short-cohort
    extent distribution, and measure how much HR-readout skill the EXISTING frozen
    encoder loses as a function of crop length. This establishes the recoverable headroom.
    If the frozen encoder loses <20% of its readout skill on 41-minute crops, extent
    is not costing us much and the augmentation arm is not funded. exp-e23''s synthetic-control
    precedent applies: measure what the design CAN recover before claiming it recovered
    anything.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g0-synthetic-crop-power-check
  mde: 0.2
- blocking: true
  description: PAIRED, SAME-SUBJECT-SAME-DAY EVALUATION. Skill must be scored on the
    SAME dense days at full extent and cropped extent, so subject and day are held
    constant and the only variable is extent. A cross-cohort comparison (lifesnaps
    vs CASE) is NOT admissible evidence here -- it confounds extent with cohort, device,
    and stressor type, which arXiv:2405.09563 shows is the dominant factor in cross-dataset
    stress transfer.
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g1-paired-crop-eval
  mde: 0.05
- blocking: true
  description: TARGET REBUILD CORRECTNESS. Assert that captions/targets/labels for
    a cropped window are rebuilt from the CROPPED span, not inherited from the full
    day. Verify on >=20 examples by recomputing the target from the cropped tensor
    and diffing against the emitted target. If a cropped input is paired with a full-day
    target, the arm trains the model to hallucinate unseen hours and the whole run
    is test_invalid.
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g2-target-rebuild-verified
  mde: 0.01
- blocking: true
  description: DENSE NO-HARM. eiv_dense128_hr_raw_mae_bpm must not regress by more
    than 1.0 bpm. Augmentation spends model capacity on a regime the dense population
    does not occupy; E11 measured exactly this trade (coverage-aug mix improved supa
    hrv while hr_peak skill degraded -6.967 -> -10.585 and hr_raw lost CI-positivity).
    That precedent is the reason this gate is blocking rather than advisory.
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-no-harm
  mde: 1.0
- blocking: false
  description: 'SHORTCUT CONTROL. Re-run the extent->dataset-identity probe (W4/P3
    reference: bal-acc 0.7520 vs 0.0625 chance = 12.03x) on the augmented model''s
    representation. Crop augmentation breaks the extent<->cohort correspondence, so
    provenance decodability should FALL. If it does not, the augmentation did not
    do what it claims.'
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  id: g4-shortcut-decodability
  mde: 0.05
id: h-w7-extent-crop-augmentation-teaches-de-7bc554
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
  magnitude_note: PRIMARY. On PAIRED cropped-vs-full dense days (same subject, same
    day), the augmented encoder preserves >=0.60 of the full-extent between-subject
    spread at 41-minute crops, vs the incumbent's predicted ~0.04-0.09. Scored on
    held-out subjects.
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: none
  magnitude_note: 'Guard, not a win: the dense population must be unchanged. E11 measured
    that coverage augmentation is NOT free (supa hrv improved while hr_peak skill
    degraded -6.967 -> -10.585), so ''none'' is the honest prediction and any dense
    gain would itself be surprising.'
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- extent-heterogeneity
- wearables
- deployment-distribution
- dataset-whitelist
- pooling
- denominator
- state-supervision
---

## Statement
H-W7 (EXTENT-CROP AUGMENTATION TEACHES DE-DILUTION). Executes the T1 rung of the long-queued runbook `rb-dynamic-window-20260812`, whose T0 rung (inference-time present-mean imputation) was already MEASURED DEAD (v4w MAE 16.72 -> 16.56 bpm, v5c 20.83 -> 21.07, 133/200 emissions byte-identical). ONE TARGET: cross-extent readout robustness.

CLAIM. The encoder currently sees each dataset at essentially ONE extent -- lifesnaps always ~1357 measured minutes, CASE always ~41 -- so it can hardcode a per-provenance expected coverage instead of learning to read a partial window. Training with random contiguous crops of DENSE days down to the empirical short-cohort extent distribution (27-224 minutes), with targets rebuilt from the cropped span, forces the with_presence_v1 flags to be read and makes de-dilution a learned function rather than a memorised constant. The test is PAIRED: the same dense day at full and cropped extent, so subject and day are held constant and extent is the only variable.

WHY THE PRIOR IS AGAINST THIS, STATED HONESTLY. `e11-s-supabase-transfer-failure-is-5472c8` closed inconclusive/mechanism_wrong with the lesson 'do not spend more effort on synthetic-coverage domain matching from public dense corpora'. I am proposing a form of synthetic coverage matching, so the burden is on me. Three scope facts distinguish this arm, and if the reader disagrees with all three the arm should not run: (i) E11 trained linear HEADS on frozen L34 features -- it could not teach the ENCODER anything, and the contraction happens upstream of L34; (ii) E11's masks were hr coverage ~U[0.57,0.95], an order of magnitude milder than the c=0.019-0.18 regime that actually contains our stress labels; (iii) E11's target was supabase deployment MAE, whereas this arm's primary target is paired cross-extent readout robustness, which E11 never measured. Per the vault's own rule that evidence is bound to its TARGET as well as its substrate, E11 does not close this.

PRIOR ART (verified 2026-08-20): ElasticAST arXiv:2407.08691 -- fixed-length models collapse off training length, native-length training holds; NaViT arXiv:2307.06304 -- variable-resolution training beats fixed-resolution EVEN AT the fixed resolution; ACL-ECG (Sensors 2026) uses 80-100% random scale cropping on ECG for +1.29% AUROC and ~5-8x label efficiency; RL-BioAug arXiv:2601.13964 finds an RL agent SELECTS Crop-and-Resize 77% of the time for EEG seizure tasks. HONEST GAP: no published work crops long free-living days to lab-session lengths for a wearable->LLM encoder and reports both directions, so the effect size here is genuinely unknown and the synthetic-control power check exists to bound it before GPU is spent.

## Rationale


## Plan


## Links
Tests problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Predicts [[eiv_extent_variance_compression_ratio]] (up).
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (none).
Gate `g0-synthetic-crop-power-check` (blocking): SYNTHETIC-CONTROL POWER CHECK FIRST, CPU-ONLY, BLOCKING. Before training: take DENSE lifesnaps/pmdata days where the answer is known, crop them to the short-cohort extent distribution, and measure how much HR-readout skill the EXISTING frozen encoder loses as a function of crop length. This establishes the recoverable headroom. If the frozen encoder loses <20% of its readout skill on 41-minute crops, extent is not costing us much and the augmentation arm is not funded. exp-e23's synthetic-control precedent applies: measure what the design CAN recover before claiming it recovered anything.
Gate `g1-paired-crop-eval` (blocking): PAIRED, SAME-SUBJECT-SAME-DAY EVALUATION. Skill must be scored on the SAME dense days at full extent and cropped extent, so subject and day are held constant and the only variable is extent. A cross-cohort comparison (lifesnaps vs CASE) is NOT admissible evidence here -- it confounds extent with cohort, device, and stressor type, which arXiv:2405.09563 shows is the dominant factor in cross-dataset stress transfer.
Gate `g2-target-rebuild-verified` (blocking): TARGET REBUILD CORRECTNESS. Assert that captions/targets/labels for a cropped window are rebuilt from the CROPPED span, not inherited from the full day. Verify on >=20 examples by recomputing the target from the cropped tensor and diffing against the emitted target. If a cropped input is paired with a full-day target, the arm trains the model to hallucinate unseen hours and the whole run is test_invalid.
Gate `g3-dense-no-harm` (blocking): DENSE NO-HARM. eiv_dense128_hr_raw_mae_bpm must not regress by more than 1.0 bpm. Augmentation spends model capacity on a regime the dense population does not occupy; E11 measured exactly this trade (coverage-aug mix improved supa hrv while hr_peak skill degraded -6.967 -> -10.585 and hr_raw lost CI-positivity). That precedent is the reason this gate is blocking rather than advisory.
Gate `g4-shortcut-decodability` (advisory): SHORTCUT CONTROL. Re-run the extent->dataset-identity probe (W4/P3 reference: bal-acc 0.7520 vs 0.0625 chance = 12.03x) on the augmented model's representation. Crop augmentation breaks the extent<->cohort correspondence, so provenance decodability should FALL. If it does not, the augmentation did not do what it claims.

#extent-heterogeneity #wearables #deployment-distribution #dataset-whitelist #pooling #denominator #state-supervision #optihealth-eiv #data-contract-violation #matched-baseline
