---
ambition_class: mechanism
created_at: '2026-09-18T11:14:34+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: Discovery gates passed before seed43 training; exact same source checkpoint;
    complete control/treatment pair, no recipe changes.
  diagnostic_id: eiv_transfer_fimae_hr_test
  id: g0-conditional
  mde: null
- blocking: true
  description: Both seed-specific test comparator-minus-treatment error deltas>0.
    Pooled per-row mean-error delta has97.5% subject-clustered CI entirely>0; Bonferroni
    protection for the two prespecified confirmatory contrasts.
  diagnostic_id: eiv_transfer_fimae_hr_test
  id: g1-repeat
  mde: null
- blocking: true
  description: Coverage>=0.99 with<=0.01 drop, native-vs-all-null and donor-tracking
    predicates, and Supabase upper harm CI<=0.5bpm hold at seed43. No failed guards
    hidden by pooling.
  diagnostic_id: eiv_transfer_fimae_hr_test
  id: g2-guards
  mde: null
- blocking: true
  description: All source, split, target, row, model and code hashes match; no test-driven
    recipe change; each seed printed separately; two draws not sold as a precise variance
    estimate.
  diagnostic_id: eiv_transfer_fimae_hr_test
  id: g3-integrity
  mde: null
id: eiv-transfer-h3-h1-confirmation
interventions: []
override_reason: ''
paper_refs:
- rep2text-2511-06571
- xbridge-2608-11676
- c2c-2510-03215
predicted_effects:
- diagnostic_id: eiv_transfer_fimae_hr_test
  direction: down
  magnitude_note: Both seed-specific test effects favorable; pooled error-delta CI
    positive.
problem_id: eiv-functional-sensor-interface-transfer
problem_tags:
- optihealth-eiv
- matched-baseline
reopen_reason: ''
reopened_from: null
status: proposed
supersedes: null
topic_tags:
- cross-modal
- medgemma
- sensor-text
---

## Statement
Conditional on H1 discovery passing, its locked L/P recipe contrast retains the favorable sign at C-seed43 and on the final test panel, conditional on the one shared seed42 source tower.

## Rationale
Paired replication, not best-seed selection. No encoder-seed variance claim.

## Plan
Conditional preregistration before discovery: launch only when full discovery gates pass and the complete second-seed pair fits the frozen24h budget; otherwise do not start.

## Links
Tests problem [[eiv-functional-sensor-interface-transfer]].
Predicts [[eiv_transfer_fimae_hr_test]] (down).
Gate `g0-conditional` (blocking): Discovery gates passed before seed43 training; exact same source checkpoint; complete control/treatment pair, no recipe changes.
Gate `g1-repeat` (blocking): Both seed-specific test comparator-minus-treatment error deltas>0. Pooled per-row mean-error delta has97.5% subject-clustered CI entirely>0; Bonferroni protection for the two prespecified confirmatory contrasts.
Gate `g2-guards` (blocking): Coverage>=0.99 with<=0.01 drop, native-vs-all-null and donor-tracking predicates, and Supabase upper harm CI<=0.5bpm hold at seed43. No failed guards hidden by pooling.
Gate `g3-integrity` (blocking): All source, split, target, row, model and code hashes match; no test-driven recipe change; each seed printed separately; two draws not sold as a precise variance estimate.
Cites [[rep2text-2511-06571]], [[xbridge-2608-11676]], [[c2c-2510-03215]].

#cross-modal #medgemma #sensor-text #optihealth-eiv #matched-baseline
