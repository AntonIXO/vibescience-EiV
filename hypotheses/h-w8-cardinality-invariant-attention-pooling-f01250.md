---
ambition_class: null
created_at: '2026-08-20T21:16:32+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: 'PRECONDITION: H-W5 passed AND H-W6''s masked-pooling arm already run.
    Attention pooling is strictly more expressive than mask-weighted mean and strictly
    harder to attribute; running it first would confound ''presence-weighting helps''
    with ''a trainable pool helps''. The mask-weighted arm is the cheap ablation that
    must come first.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g0-ordering-after-w6
  mde: 0.6
- blocking: true
  description: TWO READOUT FAMILIES, NULLS RECOMPUTED PER READOUT. eiv_momentary_state_auc_over_null's
    own description MANDATES this after exp-e34 showed the arm ordering INVERTS between
    RandomForest and linear (raw_patch_grid RF +0.1120 z+2.66 / LINEAR +0.0143 z+1.10;
    enc_pca8 RF -0.0492 z-1.68 / LINEAR +0.0405 z+2.83). Report a tree ensemble AND
    a linear model, each against its OWN within-window shuffle null, and name the
    estimator in every quoted number. A single-readout result is not admissible.
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g1-two-readout-families
  mde: 0.05
- blocking: true
  description: 'EVALUATION POOL EXCLUDES nurse_stress. `that-the-full-corpus-12-547-window-flow-vs-d55a19`
    ruled the full transition pool INVALID: nurse_stress is 43% of it (1,729 of 4,001
    rows from 12 subjects), its labels are shift-schedule-carried (clock-alone AUC
    0.6358), and its giant windows soften the within-window null so that even zero-parameter
    reference arms read as null. Score on the CLEAN pool (n=2272, 95 subjects) where
    raw_shallow recovers to +0.074 z+7.14. Scoring on the full pool invalidates the
    run.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g2-clean-pool-not-full-pool
  mde: 0.05
- blocking: true
  description: BEAT THE ZERO-PARAMETER BOUND, NOT ZERO. The handcrafted raw_shallow
    bound is AUC 0.6430 (+0.0989 over null, z+4.69) and no learned arm has ever reached
    it; the best trained-head effect on record is +0.022 (exp-049) and a 5-seed extension
    REFUTED the PL60 state lever at pooled +0.0079. This arm must clear +0.05 over
    its own null on the clean pool to count, i.e. roughly half the shallow bound --
    more than double anything previously achieved by a learned head.
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g3-beat-shallow-bound-fraction
  mde: 0.05
- blocking: false
  description: LOCALISATION EVIDENCE. Report the attention mass assigned to the patch(es)
    actually covering the labelled epoch vs uniform 1/23. This is the claim Ilse et
    al. make for histopathology and which the 2026-08-20 literature sweep found NOBODY
    has demonstrated for a sparse EMA label on a physiological bag. If mass is at
    chance the pooling may still help via cardinality-invariance, but the MIL localisation
    story is dead and must be reported as such.
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g4-attention-localisation
  mde: 0.05
id: h-w8-cardinality-invariant-attention-pooling-f01250
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: 'PRIMARY and deliberately the deployment-decisive target, not the
    mechanism one. Predict >=+0.05 over its own within-window shuffle null on the
    CLEAN (nurse_stress-excluded) transition pool, under BOTH readout families. Reference:
    raw_shallow +0.0989 z+4.69 is the zero-parameter bound; best learned head to date
    is +0.022 (exp-049) and the 5-seed pooled estimate was +0.0079.'
- diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
  magnitude_note: 'Mechanism effect: cardinality-invariant pooling should hold short-cohort
    spread at >=0.60, matching or exceeding the mask-weighted arm.'
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
reopen_reason: ''
reopened_from: null
status: proposed
supersedes: null
topic_tags:
- extent-heterogeneity
- pooling
- state-supervision
- wearables
- denominator
- deployment-distribution
---

## Statement
H-W8 (CARDINALITY-INVARIANT ATTENTION POOLING, SCORED ON MOMENTARY STATE). Conditional on H-W5 AND H-W6. ONE TARGET: momentary within-window state. This is the only hypothesis in the family whose primary effect is a STATE metric, and it is deliberately the LAST one -- the state axis has the worst track record in this vault and must not be used to justify the cheaper mechanism arms.

CLAIM. Replacing the fixed-arity pool with PMA / gated-attention MIL over the 23 patch tokens, with the M_mask presence fraction as an additive attention bias, does two things a mask-weighted mean cannot: it is cardinality-invariant BY CONSTRUCTION, and it can LOCALISE which patch carries a sparse label instead of averaging it away. Our label geometry is exactly a MIL bag -- one session/EMA label over 23 patches of which 1-2 are real for a short session.

THE PRIOR IS BAD AND I AM STATING IT UP FRONT RATHER THAN BURYING IT. Every learned state arm on this vault has been weak or negative: exp-046 (enc_pca8 -0.0492 z-1.68 under RF), exp-048 (5-seed extension REFUTED the PL60 lever, pooled +0.0079 against a +0.008 bar), exp-e27, exp-e29. A random-init encoder sits exactly on its own null under RF (-0.0022, z-0.08), which is the strongest single argument that this architecture never surfaced momentary state at all. What keeps a residual alive is narrow and specific: (a) under a matched LINEAR readout the trained encoder DOES clear its null at z+2.83 while handcrafted features do not (exp-e34), so the 'no headroom' argument is formally RF-only; (b) exp-048's refutation still left 10/10 cells POSITIVE with sign-test p~=0.03, i.e. a consistent ~+0.006 effect below single-seed resolution; (c) NONE of those arms addressed pooling arity -- they all pooled presence-blind, so on the short cohorts they were reading a representation contracted to 2-8% of its spread. If W5 confirms that contraction, every prior state null on short-extent data was measured through a broken instrument. That is the honest case for one more state arm, and it is contingent on W5.

IF THIS FAILS the correct conclusion is that state is SUPPLY-limited, not architecture-limited, and the vault should stop funding encoder-side state arms entirely until new dense induced-stress labels land -- grouped by STRESSOR TYPE per arXiv:2405.09563, not merely by availability. I will record that as the lesson rather than proposing a variant.

## Rationale


## Plan


## Links
Tests problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Predicts [[eiv_extent_variance_compression_ratio]] (up).
Gate `g0-ordering-after-w6` (blocking): PRECONDITION: H-W5 passed AND H-W6's masked-pooling arm already run. Attention pooling is strictly more expressive than mask-weighted mean and strictly harder to attribute; running it first would confound 'presence-weighting helps' with 'a trainable pool helps'. The mask-weighted arm is the cheap ablation that must come first.
Gate `g1-two-readout-families` (blocking): TWO READOUT FAMILIES, NULLS RECOMPUTED PER READOUT. eiv_momentary_state_auc_over_null's own description MANDATES this after exp-e34 showed the arm ordering INVERTS between RandomForest and linear (raw_patch_grid RF +0.1120 z+2.66 / LINEAR +0.0143 z+1.10; enc_pca8 RF -0.0492 z-1.68 / LINEAR +0.0405 z+2.83). Report a tree ensemble AND a linear model, each against its OWN within-window shuffle null, and name the estimator in every quoted number. A single-readout result is not admissible.
Gate `g2-clean-pool-not-full-pool` (blocking): EVALUATION POOL EXCLUDES nurse_stress. `that-the-full-corpus-12-547-window-flow-vs-d55a19` ruled the full transition pool INVALID: nurse_stress is 43% of it (1,729 of 4,001 rows from 12 subjects), its labels are shift-schedule-carried (clock-alone AUC 0.6358), and its giant windows soften the within-window null so that even zero-parameter reference arms read as null. Score on the CLEAN pool (n=2272, 95 subjects) where raw_shallow recovers to +0.074 z+7.14. Scoring on the full pool invalidates the run.
Gate `g3-beat-shallow-bound-fraction` (blocking): BEAT THE ZERO-PARAMETER BOUND, NOT ZERO. The handcrafted raw_shallow bound is AUC 0.6430 (+0.0989 over null, z+4.69) and no learned arm has ever reached it; the best trained-head effect on record is +0.022 (exp-049) and a 5-seed extension REFUTED the PL60 state lever at pooled +0.0079. This arm must clear +0.05 over its own null on the clean pool to count, i.e. roughly half the shallow bound -- more than double anything previously achieved by a learned head.
Gate `g4-attention-localisation` (advisory): LOCALISATION EVIDENCE. Report the attention mass assigned to the patch(es) actually covering the labelled epoch vs uniform 1/23. This is the claim Ilse et al. make for histopathology and which the 2026-08-20 literature sweep found NOBODY has demonstrated for a sparse EMA label on a physiological bag. If mass is at chance the pooling may still help via cardinality-invariance, but the MIL localisation story is dead and must be reported as such.

#extent-heterogeneity #pooling #state-supervision #wearables #denominator #deployment-distribution #optihealth-eiv #data-contract-violation #matched-baseline
