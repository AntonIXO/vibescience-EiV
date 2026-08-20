---
created_at: '2026-08-20T21:19:54+00:00'
former_ids: []
gates:
- 'PRECONDITION: H-W5 supported AND the W6 mask-weighted arm already RUN. Attention
  pooling is strictly more expressive than a mask-weighted mean; running it first
  confounds ''presence-weighting helps'' with ''a trainable pool helps''.'
- 'g1-two-readout-families BLOCKING: report a tree ensemble AND a linear model, each
  against its OWN within-window shuffle null, naming the estimator in every quoted
  number. Single-readout results are inadmissible (exp-e34 showed the arm ordering
  INVERTS between RF and linear).'
- 'g2-clean-pool-not-full-pool BLOCKING: exclude nurse_stress from the transition
  pool. The full pool is ruled INVALID (`that-the-full-corpus-12-547-window-flow-vs-d55a19`)
  -- nurse_stress is 43% of it, its labels are shift-schedule-carried (clock-alone
  AUC 0.6358), and it softens the within-window null so even zero-parameter arms read
  as null.'
- 'g3-beat-shallow-bound-fraction BLOCKING: >=+0.05 over own null on the clean pool.
  The zero-parameter handcrafted bound is +0.0989 (z+4.69); best learned head to date
  is +0.022 and the 5-seed pooled estimate was +0.0079.'
- 'g4-attention-localisation: report attention mass on the patch(es) covering the
  labelled epoch vs uniform 1/23.'
- If this fails, record the lesson that state is SUPPLY-limited rather than architecture-limited
  and STOP funding encoder-side state arms until new dense induced-stress labels land,
  grouped by stressor type. Do NOT propose a variant.
hypothesis_id: null
id: w8-cardinality-invariant-attention-pooling-a16135
notes: []
priority: 4
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
status: queued
topic_tags:
- extent-heterogeneity
- pooling
- state-supervision
updated_at: '2026-08-20T21:19:54+00:00'
---

## Prompt
W8 -- CARDINALITY-INVARIANT ATTENTION POOLING, SCORED ON MOMENTARY STATE. SELF-CONTAINED prompt. LAST in the family and the only one whose primary target is state.

=== THE CHANGE ===
Intervention `eiv-attention-pooling-over-patches`: replace the fixed-arity cross-patch pool with Set-Transformer PMA (arXiv:1810.00825, learnable seed queries, cardinality-invariant) or gated attention MIL (arXiv:1802.04712), with the M_mask presence fraction as an additive attention bias so fill patches can be driven to ~0 weight. Keep sensor_slots and d_llm unchanged so the LM-facing prefix geometry is untouched.

=== THE PRIOR IS BAD. READ IT BEFORE SPENDING GPU. ===
Every learned state arm here has been weak or negative: exp-046 (enc_pca8 -0.0492 z-1.68 under RF), exp-048 (5-seed extension REFUTED the PL60 lever, pooled +0.0079 vs a +0.008 bar), exp-e27, exp-e29. Under RF a RANDOM-INIT encoder sits exactly on its own null (-0.0022, z-0.08), which is the strongest argument that this architecture never surfaced momentary state at all.
What keeps a residual alive, and it is narrow: (a) under a matched LINEAR readout the trained encoder DOES clear its null at z+2.83 while handcrafted features do not (exp-e34), so the 'no headroom' argument is formally RF-ONLY; (b) exp-048's refutation still left 10/10 cells POSITIVE, sign-test p~=0.03, i.e. a consistent ~+0.006 effect below single-seed resolution; (c) none of those arms addressed pooling ARITY -- they all pooled presence-blind, so on short cohorts they read a representation contracted to 2-8% of its spread (W4b). If W5 confirms that contraction, every prior state null on short-extent data was measured through a broken instrument. That is the entire case for one more state arm and it is CONTINGENT ON W5.

=== SCORE AGAINST ===
Hypothesis `h-w8-cardinality-invariant-attention-pooling-f01250`. Do NOT open a new hypothesis. Recover exact gate ids/bars from /root/vibescience-vault/hypotheses/h-w8-cardinality-invariant-attention-pooling-f01250.md.
Primary: eiv_momentary_state_auc_over_null >= +0.05 over its own within-window shuffle null on the CLEAN pool, under BOTH readout families.

=== THE HONEST GAP IN THE LITERATURE ===
The 2026-08-20 sweep found NO published work that attaches a sparse point label (one EMA ping) to a long physiological bag AND quantifies attention mass on the labelled segment against fixed-window baselines. Closest is arXiv:2604.06990 (attention MIL for questionnaire stress from wearables, LOSO R2 0.24-0.28, r 0.42-0.49) which is bag-level and proves no localisation. So g4 is genuinely novel measurement, not a reproduction.

=== IF IT FAILS ===
Conclude that state is SUPPLY-limited, not architecture-limited. Stop funding encoder-side state arms until new dense induced-stress labels land, grouped by STRESSOR TYPE (arXiv:2405.09563 shows stressor type -- not intensity or device -- governs cross-dataset stress transfer). Record that as the lesson; do not propose a variant.

## Preconditions
H-W5 supported; W6 run and closed. GPU free. Clean transition pool (nurse_stress excluded, n~2272, 95 subjects) prepared. Read `that-the-full-corpus-12-547-window-flow-vs-d55a19`, `the-function-class-account-of-exp-046-that-8-511840`, and the eiv_momentary_state_auc_over_null diagnostic description (2026-08-18 amendment) BEFORE designing.

## Links
Queued for problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Intended gates: `PRECONDITION: H-W5 supported AND the W6 mask-weighted arm already RUN. Attention pooling is strictly more expressive than a mask-weighted mean; running it first confounds 'presence-weighting helps' with 'a trainable pool helps'.`, `g1-two-readout-families BLOCKING: report a tree ensemble AND a linear model, each against its OWN within-window shuffle null, naming the estimator in every quoted number. Single-readout results are inadmissible (exp-e34 showed the arm ordering INVERTS between RF and linear).`, `g2-clean-pool-not-full-pool BLOCKING: exclude nurse_stress from the transition pool. The full pool is ruled INVALID (`that-the-full-corpus-12-547-window-flow-vs-d55a19`) -- nurse_stress is 43% of it, its labels are shift-schedule-carried (clock-alone AUC 0.6358), and it softens the within-window null so even zero-parameter arms read as null.`, `g3-beat-shallow-bound-fraction BLOCKING: >=+0.05 over own null on the clean pool. The zero-parameter handcrafted bound is +0.0989 (z+4.69); best learned head to date is +0.022 and the 5-seed pooled estimate was +0.0079.`, `g4-attention-localisation: report attention mass on the patch(es) covering the labelled epoch vs uniform 1/23.`, `If this fails, record the lesson that state is SUPPLY-limited rather than architecture-limited and STOP funding encoder-side state arms until new dense induced-stress labels land, grouped by stressor type. Do NOT propose a variant.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#extent-heterogeneity #pooling #state-supervision
