---
created_at: '2026-08-20T21:19:02+00:00'
former_ids: []
gates:
- 'PRECONDITION: runbook w5-three-stage-extent-contraction-measurement-7a781f closed
  and H-W5 SUPPORTED. If W5 refuted, ABANDON this runbook and record why.'
- 'g0-synthetic-crop-power-check is BLOCKING and CPU-only: crop dense days to the
  short-cohort extent distribution and measure how much readout skill the EXISTING
  frozen encoder loses as a function of crop length. If it loses <20% at 41-minute
  crops, extent is cheap and this arm is NOT funded.'
- 'g1-paired-crop-eval: score on the SAME dense days at full and cropped extent (subject
  and day held constant). A lifesnaps-vs-CASE comparison is NOT admissible -- it confounds
  extent with cohort, device and stressor type.'
- 'g2-target-rebuild-verified: assert captions/targets/labels are rebuilt from the
  CROPPED span on >=20 examples by recomputing from the cropped tensor and diffing.
  A cropped input paired with a full-day target makes the run test_invalid.'
- 'g3-dense-no-harm: eiv_dense128_hr_raw_mae_bpm must not regress beyond 1.0 bpm.'
- 'g4-shortcut-decodability: re-run the extent->dataset-identity probe (reference
  bal-acc 0.7520 vs 0.0625 chance); crop augmentation should LOWER provenance decodability.'
- 'SEED DISCIPLINE: >=3 seeds with per-seed values reported. The Phase-C supabase
  seed band is 6.29 bpm and exceeds the 4.02 trivial baseline, so single-seed deployment
  comparisons are uninterpretable.'
hypothesis_id: null
id: w7-extent-crop-augmentation-t1-rung-of-the-long-1f7f7f
notes:
- at: '2026-08-29T22:11:08+00:00'
  text: 'ABANDONED ON MEASUREMENT, not on priority. exp-w7-stage0-crop-power-20260821
    refuted the premise for ~4 CPU minutes instead of the ~6 GPU-hours this runbook
    budgeted: the encoder reads a 41-minute crop at 0.964 (avg) / 0.950 (masked) retention
    against a gate demanding >= 20% loss, so there is no readout fragility for crop
    augmentation to teach out of. The two defects are separable and only one existed
    -- representation CONTRACTION is real and now fixed and generalising (W5/W6/W12/W13),
    readout FRAGILITY is not real. Problem resolved; do not re-queue.'
priority: 3
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
status: abandoned
topic_tags:
- extent-heterogeneity
- wearables
- deployment-distribution
updated_at: '2026-08-29T22:11:08+00:00'
---

## Prompt
W7 -- EXTENT-CROP AUGMENTATION (T1 rung of the long-queued rb-dynamic-window-20260812). SELF-CONTAINED prompt.

=== THE PREMISE, MEASURED ===
Corpus ac7d2b1c. Median HR-present minutes per window: lifesnaps 1357, pmdata 1382, dryad 512 vs case 41, nurse_stress 38, popane_s3 36, neuro_stress 32, popane_s4 27. Each dataset appears at essentially ONE extent, so the encoder can hardcode a per-provenance expected coverage instead of learning to read a partial window. Extent alone predicts dataset identity at bal-acc 0.7520 vs 0.0625 chance (12.03x).

=== WHAT IS ALREADY DEAD -- DO NOT RE-MOTIVATE THIS WAY ===
T0 of the parent runbook (inference-time present-mean imputation) is MEASURED DEAD: v4w supabase MAE 16.72 -> 16.56 bpm, v5c 20.83 -> 21.07, 133/200 and 154/200 emissions byte-identical. Do not propose inference-time de-dilution again.
E11 (`e11-s-supabase-transfer-failure-is-5472c8`) closed inconclusive/mechanism_wrong: coverage covariate shift explained only ~20% of the dense->supabase hrv gap, and its mix-training arm moved supa hrv 13.675 -> 9.990 while hr_peak skill DEGRADED -6.967 -> -10.585 and hr_raw LOST CI-positivity. Its lesson says do not spend more on synthetic-coverage domain matching. The burden of proof is on this arm.
What distinguishes this arm from E11, and if you disagree with all three points do not run it: (i) E11 trained linear HEADS on frozen L34 features and could not teach the ENCODER anything; (ii) E11's masks were hr coverage ~U[0.57,0.95] while the labelled stress cohorts here sit at c=0.019-0.18, an order of magnitude more extreme; (iii) E11's target was supabase deployment MAE, whereas this arm's primary target is PAIRED cross-extent readout robustness, which E11 never measured.

=== THE ARM ===
Intervention `eiv-extent-crop-augmentation`: during training, randomly crop DENSE windows (c>0.75) to a contiguous span drawn from the empirical short-cohort extent distribution (27-224 minutes), zero-fill the remainder, update M_mask, and REBUILD targets/captions/labels from the cropped span. Score against hypothesis `h-w7-extent-crop-augmentation-teaches-de-7bc554` -- do NOT open a new hypothesis. Recover exact gate ids/bars from /root/vibescience-vault/hypotheses/h-w7-extent-crop-augmentation-teaches-de-7bc554.md.

=== PRIOR ART (verified arXiv ids 2026-08-20) ===
ElasticAST arXiv:2407.08691 (fixed-length models collapse off training length; native-length training holds; mask attention pooling). NaViT arXiv:2307.06304 (variable-resolution training beats fixed-res even AT the fixed res). ACL-ECG Sensors 2026 (80-100% random scale crop, +1.29% AUROC, ~5-8x label efficiency). RL-BioAug arXiv:2601.13964 (RL agent selects Crop-and-Resize 77% for EEG seizure). HONEST GAP: nobody has published cropping long free-living days to lab-session lengths for a wearable->LLM encoder, so the effect size is unknown and g0 exists to bound it before GPU is spent.

=== SINGLE VARIABLE ===
yaml-diff the profile bodies and assert the only differences are the augmentation flag, the seed, and notes. If an extra knob was needed, declare it in the intervention description and pre-queue the isolation arm BEFORE reading the result.

## Preconditions
H-W5 supported. GPU free (~2h per arm on the RTX 5070, <11.25 GiB). Corpus ac7d2b1c. Read rule_out `that-the-mean-gap-naive-pooled-mean-present-5bb2e3` and hypothesis `e11-s-supabase-transfer-failure-is-5472c8` BEFORE designing the arm.

## Log
- **2026-08-29T22:11:08+00:00** — ABANDONED ON MEASUREMENT, not on priority. exp-w7-stage0-crop-power-20260821 refuted the premise for ~4 CPU minutes instead of the ~6 GPU-hours this runbook budgeted: the encoder reads a 41-minute crop at 0.964 (avg) / 0.950 (masked) retention against a gate demanding >= 20% loss, so there is no readout fragility for crop augmentation to teach out of. The two defects are separable and only one existed -- representation CONTRACTION is real and now fixed and generalising (W5/W6/W12/W13), readout FRAGILITY is not real. Problem resolved; do not re-queue.

## Links
Queued for problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Intended gates: `PRECONDITION: runbook w5-three-stage-extent-contraction-measurement-7a781f closed and H-W5 SUPPORTED. If W5 refuted, ABANDON this runbook and record why.`, `g0-synthetic-crop-power-check is BLOCKING and CPU-only: crop dense days to the short-cohort extent distribution and measure how much readout skill the EXISTING frozen encoder loses as a function of crop length. If it loses <20% at 41-minute crops, extent is cheap and this arm is NOT funded.`, `g1-paired-crop-eval: score on the SAME dense days at full and cropped extent (subject and day held constant). A lifesnaps-vs-CASE comparison is NOT admissible -- it confounds extent with cohort, device and stressor type.`, `g2-target-rebuild-verified: assert captions/targets/labels are rebuilt from the CROPPED span on >=20 examples by recomputing from the cropped tensor and diffing. A cropped input paired with a full-day target makes the run test_invalid.`, `g3-dense-no-harm: eiv_dense128_hr_raw_mae_bpm must not regress beyond 1.0 bpm.`, `g4-shortcut-decodability: re-run the extent->dataset-identity probe (reference bal-acc 0.7520 vs 0.0625 chance); crop augmentation should LOWER provenance decodability.`, `SEED DISCIPLINE: >=3 seeds with per-seed values reported. The Phase-C supabase seed band is 6.29 bpm and exceeds the 4.02 trivial baseline, so single-seed deployment comparisons are uninterpretable.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#extent-heterogeneity #wearables #deployment-distribution
