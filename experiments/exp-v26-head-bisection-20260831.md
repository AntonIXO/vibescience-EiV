---
artifacts:
- outputs/checkpoints/v26diary/phase_a
- outputs/checkpoints/v26context/phase_a
- outputs/checkpoints/v26affect/phase_a
- logs/v26_driver.log
- logs/v26diary_geometry.json
- logs/v26context_geometry.json
- logs/v26affect_geometry.json
- scratch/run_v26_bisection.sh
closed: true
config_note: 'v26 bisection: three sequential Phase-A arms (v26diary / v26context
  / v26affect), each = phase_a_12ch_v20strat + ONE supervision component at 0.3 (dict-diff
  verified per arm). Geometry probe per arm (gate script cloned from _geom_gate_v20.py).
  Bracket: v20strat no-heads 7.44, v22max all-heads 5.94, healthy anchor 8.35. No
  B/C spend. Runner scratch/run_v26_bisection.sh.'
cost:
  gpu_hours: 1.1
  wall_hours: 1.1
crash_reason: ''
created_at: '2026-08-31T02:41:12+00:00'
diagnostics_measured:
- after: 8.5989
  before: 7.4385
  diagnostic_id: eiv_effective_rank
do_not_retry:
- 'Do NOT blame any single label head for the v22 rank cost: diary 8.21, context 8.17,
  affect 8.60 individually (all ABOVE the no-head 7.44) while the triple reads 5.94.
  The cost is interaction; bisect PAIRS, not heads.'
external_run: ''
former_ids: []
gate_results:
- evidence: 'PASS, with the superadditive branch triggered: ALL THREE heads individually
    INNOCENT and rank-POSITIVE -- diary 8.21, context 8.17, affect 8.60 (bracket:
    no-heads 7.44, all-heads 5.94, healthy 8.35). Every single head ADDS +0.73..+1.16
    rank; the triple SUBTRACTS 1.50. The compound cost is pure interaction, not any
    head''s own gradient. Per the preregistered rule the next arm is pairwise/max-safe-set.
    Note affect alone EXCEEDS the healthy anchor -- consistent with the v26 affect
    supply now spanning 6 corpora (dapper/k_emocon/popane_s5-s7 added).'
  gate_id: g1-locate-rank-cost-per-head
  passed: true
git_ref: ''
hypothesis_id: per-head-rank-cost-bisection-adding-each-dfd199
id: exp-v26-head-bisection-20260831
interpretation:
  at: '2026-08-31T03:45:49+00:00'
  failure_mode: claim_false
  lesson: 'The formal ''refutes'' is against the predicted DIRECTION (I predicted
    rank cost; every head alone ADDS rank: diary 8.21, context 8.17, affect 8.60 vs
    no-heads 7.44 -- affect exceeds the healthy anchor). The informative content:
    the v22 compound damage (-1.50) is PURE MULTI-HEAD INTERACTION, no single head
    is guilty. Single-head supervision at 0.3 both utilises labels AND improves geometry
    -- the affect arm is the immediate winner (largest label consumer: valence/arousal/stress
    over 6 corpora, and best rank on record for a fresh new-corpus A). Next: v27 chain
    off v26affect A (baseline gate MAE <= 2.836), then pairwise bisection for the
    interaction.'
  transfers_to:
  - per-head-rank-cost-bisection-adding-each-dfd199
  - maximal-label-utilisation-on-the-repaired-9c2265
key_numbers:
  affect_rank: 8.5989
  all_heads_rank: 5.9358
  context_rank: 8.1747
  diary_rank: 8.2103
  healthy_anchor: 8.353
  no_heads_rank: 7.4385
observed_effects:
- delta: 1.1604
  diagnostic_id: eiv_effective_rank
  direction: up
parent_experiment_id: exp-v22-max-supervision-20260831
prediction_match:
  overall: false
  per_diagnostic:
    eiv_effective_rank: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[per-head-rank-cost-bisection-adding-each-dfd199]].
Branches from [[exp-v22-max-supervision-20260831]].

## Measurements
- [[eiv_effective_rank]]: before 7.4385 → after 8.5989  (Δ +1.1604, up ✗ predicted down)

## Gates
- `g1-locate-rank-cost-per-head`: PASS — PASS, with the superadditive branch triggered: ALL THREE heads individually INNOCENT and rank-POSITIVE -- diary 8.21, context 8.17, affect 8.60 (bracket: no-heads 7.44, all-heads 5.94, healthy 8.35). Every single head ADDS +0.73..+1.16 rank; the triple SUBTRACTS 1.50. The compound cost is pure interaction, not any head's own gradient. Per the preregistered rule the next arm is pairwise/max-safe-set. Note affect alone EXCEEDS the healthy anchor -- consistent with the v26 affect supply now spanning 6 corpora (dapper/k_emocon/popane_s5-s7 added).

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The formal 'refutes' is against the predicted DIRECTION (I predicted rank cost; every head alone ADDS rank: diary 8.21, context 8.17, affect 8.60 vs no-heads 7.44 -- affect exceeds the healthy anchor). The informative content: the v22 compound damage (-1.50) is PURE MULTI-HEAD INTERACTION, no single head is guilty. Single-head supervision at 0.3 both utilises labels AND improves geometry -- the affect arm is the immediate winner (largest label consumer: valence/arousal/stress over 6 corpora, and best rank on record for a fresh new-corpus A). Next: v27 chain off v26affect A (baseline gate MAE <= 2.836), then pairwise bisection for the interaction.

Transfers to: [[per-head-rank-cost-bisection-adding-each-dfd199]], [[maximal-label-utilisation-on-the-repaired-9c2265]]

## Key numbers
- `affect_rank`: 8.5989
- `all_heads_rank`: 5.9358
- `context_rank`: 8.1747
- `diary_rank`: 8.2103
- `healthy_anchor`: 8.353
- `no_heads_rank`: 7.4385

## Artifacts
- `outputs/checkpoints/v26diary/phase_a`
- `outputs/checkpoints/v26context/phase_a`
- `outputs/checkpoints/v26affect/phase_a`
- `logs/v26_driver.log`
- `logs/v26diary_geometry.json`
- `logs/v26context_geometry.json`
- `logs/v26affect_geometry.json`
- `scratch/run_v26_bisection.sh`

## Do not retry
- Do NOT blame any single label head for the v22 rank cost: diary 8.21, context 8.17, affect 8.60 individually (all ABOVE the no-head 7.44) while the triple reads 5.94. The cost is interaction; bisect PAIRS, not heads.

## Notes
SUPERADDITIVE branch confirmed, and better than that: every head alone is rank-POSITIVE (+0.73..+1.16 over the no-head 7.44; affect 8.60 EXCEEDS the healthy anchor 8.35). Single-head supervision is not merely safe -- it spreads the cloud. The v22 damage (-1.50) is pure multi-head interaction (three per-patch gradient fields at 0.3 each pulling the same d_model=192 space). Path to full utilisation: (a) v27 chain off the AFFECT A (best rank, biggest label consumer: valence/arousal/stress over 6 corpora incl the new dapper/k_emocon/popane s5-s7) against the baseline gate; (b) pairwise bisection (diary+context / diary+affect / context+affect) to find the poisonous pair or a safe pair; (c) if pairs are safe, weight-decay the triple (0.1-0.15 each).

#contrastive #collapse #sensor-text #wearables #epoch-resolution #baseline #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
