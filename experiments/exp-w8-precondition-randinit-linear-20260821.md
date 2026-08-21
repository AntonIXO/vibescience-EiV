---
artifacts: []
closed: true
config_note: 'Executes the ~10-CPU-minute precondition that H-W8 and the JEPA runbook
  BOTH name as mandatory before any GPU is spent on an encoder-side state arm: re-measure
  rand_pca8 and enc_pca8 under a matched LINEAR readout with their own within-window
  nulls, because the ''no headroom'' argument rests on a random-init arm that was
  only ever measured under RandomForest.


  Script scratch/w8_precondition_randinit_linear.py, readings R1-R4 committed PRE-DATA.
  Corpus ac7d2b1c, frozen ab_ctrl_s43 phase_b encoder, W=120, PCA-8 fit inside each
  train fold only, 24 within-window permutations per cell, subject-grouped GroupKFold(5).


  EVALUATION POOL IS THE CLEAN ONE, per H-W8''s own g2 gate and rule-out that-the-full-corpus-12-547-window-flow-vs-d55a19:
  nurse_stress EXCLUDED (43% of the full pool, shift-schedule-carried labels at clock-alone
  AUC 0.6358, giant windows that soften the within-window null). The rebuilt pool
  reproduces the rule-out''s reference EXACTLY -- n=2272, 95 subjects, 684 windows,
  flow-vs-stress -- which is the harness-validity anchor for everything below.


  BOTH READOUT FAMILIES REPORTED WITH PER-READOUT NULLS, as eiv_momentary_state_auc_over_null''s
  2026-08-18 amendment mandates (nulls move with the readout: RF 0.5491/0.5522 vs
  linear 0.5568/0.5553 on the same features).'
cost: null
crash_reason: ''
created_at: '2026-08-21T04:48:34+00:00'
diagnostics_measured:
- after: 0.0263
  before: 0.0405
  diagnostic_id: eiv_momentary_state_auc_over_null
do_not_retry: []
external_run: logs/w8_precondition_randinit_linear.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g0-ordering-after-w6
  passed: true
- evidence: ''
  gate_id: g1-two-readout-families
  passed: true
- evidence: ''
  gate_id: g2-clean-pool-not-full-pool
  passed: true
- evidence: ''
  gate_id: g3-beat-shallow-bound-fraction
  passed: false
- evidence: ''
  gate_id: g4-attention-localisation
  passed: false
git_ref: ''
hypothesis_id: h-w8-cardinality-invariant-attention-pooling-f01250
id: exp-w8-precondition-randinit-linear-20260821
interpretation:
  at: '2026-08-21T04:49:54+00:00'
  failure_mode: claim_false
  lesson: "TRAINING ADDS NOTHING TO THE MOMENTARY-STATE SIGNAL, AND THE OLD 'NO HEADROOM'
    ARGUMENT WAS ALSO WRONG -- both corrections land in the same run and they point
    the same way. Clean pool (n=2272, 95 subjects, exact reproduction of the reference):\n
    \ enc_pca8  RF +0.0382 z+2.94 | LINEAR +0.0263 z+2.94\n  rand_pca8 RF +0.0443
    z+3.21 | LINEAR +0.0312 z+4.61\nRandom-init MATCHES OR BEATS trained in all four
    cells; trained-minus-random is -0.0049 (linear), -0.0061 (RF).\n\nTWO PRIOR CLAIMS
    DIE HERE, and it matters that they die together. (1) 'A random-init encoder sits
    exactly on its own null (-0.0022, z-0.08), so a new objective/architecture has
    no headroom' -- that was RF-only and is an artifact; under a matched linear readout
    random-init clears its null at z+4.61. (2) 'The trained encoder carries a weak
    but real momentary-state signal (+0.0405, z+2.83)' -- it does clear its null,
    but so does a RANDOM PROJECTION of the same input, slightly better. The honest
    synthesis: there is a small real signal in the raw input reachable by an arbitrary
    linear projection over the patch grid, and Phase A/B training neither creates
    nor improves it.\n\nWHY THAT KILLS POOLING-SIDE INTERVENTIONS FOR THIS TARGET.
    H-W8 proposed cardinality-invariant attention pooling on the theory that prior
    nulls were measured through presence-blind pooling of contracted short-cohort
    representations. Changing HOW you pool a representation can only help if the representation
    carries something worth surfacing. Measured, its learned content on this target
    is nil. There is nothing to pool better.\n\nAND THE COMPETING EXPLANATION WAS
    ELIMINATED IN THE SAME SESSION, which is what makes this conclusive rather than
    suggestive. exp-w6b confirmed the contraction fix end-to-end (short-cohort ratio
    0.5630 -> 0.6897), so 'the state nulls were an artifact of contracted representations'
    has been directly tested and the state signal still does not appear. Extent was
    a real defect on the SCALE axis, it is fixed, and it was never what stood between
    this encoder and momentary state. Two different defects; only one existed on this
    axis.\n\nREUSABLE RULE, the sharpest one from the whole sweep: A CONTROL ARM IS
    ONLY AS GOOD AS THE READOUT IT WAS MEASURED UNDER. The 'no headroom' argument
    shaped GPU decisions for weeks (it is quoted in the JEPA runbook as the reason
    not to fund a state arm) and rested on a single RF measurement of a random-init
    encoder. Ten CPU minutes under a second readout family inverted it. When a control
    is load-bearing for a funding decision, measure it under at least two estimator
    families -- the diagnostic's own description has mandated this since 2026-08-18
    and this run is why.\n\nCONSEQUENCE, matching what H-W8 preregistered for exactly
    this outcome: state is SUPPLY-limited, not architecture-limited. No further encoder-side
    state arms until new dense induced-stress labels land, grouped by STRESSOR TYPE
    (arXiv:2405.09563: stressor type governs cross-dataset stress transfer, not intensity
    or device). Do not propose a variant."
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: -0.0142
  diagnostic_id: eiv_momentary_state_auc_over_null
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_extent_variance_compression_ratio: false
    eiv_momentary_state_auc_over_null: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[h-w8-cardinality-invariant-attention-pooling-f01250]].
External run: logs/w8_precondition_randinit_linear.json

## Measurements
- [[eiv_momentary_state_auc_over_null]]: before 0.0405 → after 0.0263  (Δ -0.0142, down ✗ predicted up)

## Gates
- `g0-ordering-after-w6`: PASS
- `g1-two-readout-families`: PASS
- `g2-clean-pool-not-full-pool`: PASS
- `g3-beat-shallow-bound-fraction`: **FAIL**
- `g4-attention-localisation`: **FAIL**

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

TRAINING ADDS NOTHING TO THE MOMENTARY-STATE SIGNAL, AND THE OLD 'NO HEADROOM' ARGUMENT WAS ALSO WRONG -- both corrections land in the same run and they point the same way. Clean pool (n=2272, 95 subjects, exact reproduction of the reference):
  enc_pca8  RF +0.0382 z+2.94 | LINEAR +0.0263 z+2.94
  rand_pca8 RF +0.0443 z+3.21 | LINEAR +0.0312 z+4.61
Random-init MATCHES OR BEATS trained in all four cells; trained-minus-random is -0.0049 (linear), -0.0061 (RF).

TWO PRIOR CLAIMS DIE HERE, and it matters that they die together. (1) 'A random-init encoder sits exactly on its own null (-0.0022, z-0.08), so a new objective/architecture has no headroom' -- that was RF-only and is an artifact; under a matched linear readout random-init clears its null at z+4.61. (2) 'The trained encoder carries a weak but real momentary-state signal (+0.0405, z+2.83)' -- it does clear its null, but so does a RANDOM PROJECTION of the same input, slightly better. The honest synthesis: there is a small real signal in the raw input reachable by an arbitrary linear projection over the patch grid, and Phase A/B training neither creates nor improves it.

WHY THAT KILLS POOLING-SIDE INTERVENTIONS FOR THIS TARGET. H-W8 proposed cardinality-invariant attention pooling on the theory that prior nulls were measured through presence-blind pooling of contracted short-cohort representations. Changing HOW you pool a representation can only help if the representation carries something worth surfacing. Measured, its learned content on this target is nil. There is nothing to pool better.

AND THE COMPETING EXPLANATION WAS ELIMINATED IN THE SAME SESSION, which is what makes this conclusive rather than suggestive. exp-w6b confirmed the contraction fix end-to-end (short-cohort ratio 0.5630 -> 0.6897), so 'the state nulls were an artifact of contracted representations' has been directly tested and the state signal still does not appear. Extent was a real defect on the SCALE axis, it is fixed, and it was never what stood between this encoder and momentary state. Two different defects; only one existed on this axis.

REUSABLE RULE, the sharpest one from the whole sweep: A CONTROL ARM IS ONLY AS GOOD AS THE READOUT IT WAS MEASURED UNDER. The 'no headroom' argument shaped GPU decisions for weeks (it is quoted in the JEPA runbook as the reason not to fund a state arm) and rested on a single RF measurement of a random-init encoder. Ten CPU minutes under a second readout family inverted it. When a control is load-bearing for a funding decision, measure it under at least two estimator families -- the diagnostic's own description has mandated this since 2026-08-18 and this run is why.

CONSEQUENCE, matching what H-W8 preregistered for exactly this outcome: state is SUPPLY-limited, not architecture-limited. No further encoder-side state arms until new dense induced-stress labels land, grouped by STRESSOR TYPE (arXiv:2405.09563: stressor type governs cross-dataset stress transfer, not intensity or device). Do not propose a variant.

## Notes
H-W8 IS NOT FUNDED, and the precondition it named itself is what killed it -- for ~10 CPU minutes instead of a GPU family.

MEASURED, clean pool (n=2272, 95 subjects, 684 windows, flow-vs-stress, pos 0.521 -- an EXACT reproduction of the reference in ro that-the-full-corpus-12-547-window-flow-vs-d55a19, which is the harness anchor):
  arm         readout   AUC      null              over_null    z
  enc_pca8    RF        0.5873   0.5491+-0.0130    +0.0382    +2.94
  enc_pca8    LINEAR    0.5831   0.5568+-0.0089    +0.0263    +2.94
  rand_pca8   RF        0.5965   0.5522+-0.0138    +0.0443    +3.21
  rand_pca8   LINEAR    0.5865   0.5553+-0.0068    +0.0312    +4.61

THE PRECONDITION ASKED A YES/NO QUESTION AND GOT AN ANSWER NEITHER BRANCH ANTICIPATED. It asked whether random-init is inert under a linear readout (R1: 'no headroom' survives) or clears its null (R2: the argument weakens). Measured: rand_pca8 is NOT inert -- it clears its null at +0.0312, z+4.61 -- so R1 fails. But R2's intended implication does not follow, because the trained encoder does NOT beat it. trained-minus-random under linear is -0.0049; under RF it is -0.0061. RANDOM-INIT MATCHES OR EXCEEDS TRAINED IN ALL FOUR CELLS.

WHAT THAT MEANS, stated carefully because it is the opposite of a rescue: the weak momentary-state signal that survives to the encoder's tokens is a property of the UNTRAINED architecture's projection of the raw input -- essentially a random feature map over the patch grid -- not of anything Phase A/B learned. The earlier RF-only claim ('random-init sits exactly on its own null, so there is no headroom') was itself a readout artifact: under a linear readout random-init is comfortably above null. The corrected statement is stronger and worse for H-W8: there IS a small signal, training does not produce it, and training does not improve it.

WHY THIS REFUTES H-W8 SPECIFICALLY. H-W8's case was: prior state nulls were measured through presence-blind pooling on contracted representations, so a cardinality-invariant attention pool might recover what was lost. That case required the trained representation to CARRY something a better pool could surface. It does not carry more than a random projection does. Replacing the pooling operator over a representation whose learned content is nil on this target cannot manufacture signal -- there is nothing to pool better.

AND THE COMPETING EXPLANATION IS NOW ELIMINATED TOO. This session's exp-w6b confirmed the contraction fix end-to-end (short-cohort ratio 0.5630 -> 0.6897), so 'the state nulls were an artifact of contracted short-cohort representations' has been directly addressed -- and the state signal still does not appear, on a pool that excludes the contaminating cohort. Extent was a real defect on the SCALE axis and is fixed; it was never the thing standing between this encoder and momentary state.

NO CELL REACHES THE +0.05 BAR under either readout, and the handcrafted raw_shallow bound of +0.0989 remains unapproached by anything learned.

CONSEQUENCE, which is the same conclusion H-W8 itself preregistered for this outcome: state is SUPPLY-limited, not architecture-limited. Stop funding encoder-side state arms until new dense induced-stress labels land, grouped by STRESSOR TYPE (arXiv:2405.09563 measures stressor type, not intensity or device, as the governing factor in cross-dataset stress transfer). The hypothesis said 'record that as the lesson; do not propose a variant', and that is what is being done.

#extent-heterogeneity #pooling #state-supervision #wearables #denominator #deployment-distribution #optihealth-eiv #data-contract-violation #matched-baseline
