---
ambition_class: mechanism
created_at: '2026-08-31T02:41:05+00:00'
former_ids: []
gap_closure_estimate: 0.4
gates:
- blocking: true
  description: 'g1 BLOCKING (per-arm, geometry): for each of the three single-head
    arms report eff_rank against the bracket [v22max all-heads 5.94, v20strat no-heads
    7.44]. A head is GUILTY if its arm reads < 6.7 (more than half the compound damage)
    and INNOCENT if >= 7.0. The verdict is the guilty set; any partition is informative.'
  diagnostic_id: eiv_effective_rank
  id: g1-locate-rank-cost-per-head
  mde: 0.5
id: per-head-rank-cost-bisection-adding-each-dfd199
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_effective_rank
  direction: down
  magnitude_note: 'Expectation from construction: context (categorical CE over 20
    classes, densest supply ~8.9k cells) is the most likely rank-eater; diary (masked
    MSE, 3.3k cells, pmdata-only) the least. Prediction: context guilty, diary innocent,
    affect uncertain. Calibration 0.408; the bracket decides.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
- subject-heldout
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- contrastive
- collapse
- sensor-text
- wearables
- epoch-resolution
- baseline
- control
---

## Statement
PER-HEAD RANK-COST BISECTION: adding each supervision head alone (diary 0.3 / context 0.3 / affect 0.3) to the strat-fixed Phase A partitions the v22 compound rank cost (-1.50) into per-head contributions measurable by the geometry probe, identifying at least one head that keeps eff_rank >= 7.0 (safe to integrate) or proving the cost is superadditive.

## Rationale
Anton's goal: utilise ALL labels in all three phases WITHOUT collapse. exp-v22 measured the compound supervision block at -1.50 eff_rank on the strat-fixed A -- but a compound arm cannot say WHICH head pays. Three single-head arms bracket every head against [5.94, 7.44] and cost 75 GPU-minutes total with zero C spend. This is the standard path to re-integrating the 452k label-epochs on the healed chain.

## Plan
Three Phase-A arms (scratch/run_v26_bisection.sh, sequential on GPU, ~25 min each): v26diary / v26context / v26affect, each = phase_a_12ch_v20strat + exactly ONE supervision component at the v22 weight (0.3). Dict-diff verified per arm. Readout: standard geometry probe per arm, no B/C (deployment untouched by design -- rank first, then only the innocent heads graduate to a chain). Decision rule: innocent heads (>= 7.0) get added to the healed baseline chain in ONE combined arm next; guilty heads (< 6.7) get retried at 0.1 weight or as detached (stop-gradient) probes per the literature path. If ALL three are individually innocent but v22's compound was guilty, the cost is superadditive -- then the combined-innocent arm decides the max safe set.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_effective_rank]] (down).
Gate `g1-locate-rank-cost-per-head` (blocking): g1 BLOCKING (per-arm, geometry): for each of the three single-head arms report eff_rank against the bracket [v22max all-heads 5.94, v20strat no-heads 7.44]. A head is GUILTY if its arm reads < 6.7 (more than half the compound damage) and INNOCENT if >= 7.0. The verdict is the guilty set; any partition is informative.

#contrastive #collapse #sensor-text #wearables #epoch-resolution #baseline #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
