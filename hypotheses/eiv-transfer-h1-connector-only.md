---
ambition_class: mechanism
created_at: '2026-09-18T11:14:33+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: Actual A, B-pair, B-triplet and C admitted subject sets disjoint from
    val/test; all hashes match; no legacy v41 upstream weights reused; identical locked
    rows/budget. Test-panel outputs remain unopened.
  diagnostic_id: eiv_transfer_fimae_hr_val
  id: g0-integrity
  mde: null
- blocking: true
  description: L-P failure-inclusive MAE>=0.5bpm AND subject-clustered paired95%CI
    lower bound>0 on locked validation. Threshold is engineering, not statistical
    MDE.
  diagnostic_id: eiv_transfer_fimae_hr_val
  id: g1-worthwhile
  mde: null
- blocking: true
  description: P numeric parse coverage>=0.99 and no more than0.01 below L; failure-inclusive
    denominator unchanged.
  diagnostic_id: eiv_transfer_parse_rate
  id: g2-coverage
  mde: null
- blocking: true
  description: Upper95% paired day-block-bootstrap bound of P-L Supabase loss<=0.5bpm.
    Seen-subject guard only; insufficient blocks=not reached.
  diagnostic_id: eiv_transfer_fimae_hr_supa
  id: g3-deployment
  mde: null
- blocking: true
  description: Native beats zero, norm-matched random AND metadata-only controls with
    clustered CI excluding0; donor direction accuracy>=0.65 AND donor-output MAE to
    donor truth<MAE to target truth. At least24 donor pairs/8 subjects on validation
    or gate not reached. Check coverage/slot-mask matching; no gold-output prefix.
  diagnostic_id: eiv_transfer_content_gap
  id: g4-content
  mde: null
- blocking: true
  description: Real train-save-load-generate smoke, frozen original LM weights, nonzero
    connector gradient, no NaN/OOM, actual mode stored and reloaded.
  diagnostic_id: eiv_transfer_fimae_hr_val
  id: g5-executed
  mde: null
id: eiv-transfer-h1-connector-only
interventions:
- eiv-transfer-connector-only
override_reason: ''
paper_refs:
- rep2text-2511-06571
- xbridge-2608-11676
- c2c-2510-03215
predicted_effects:
- diagnostic_id: eiv_transfer_fimae_hr_val
  direction: down
  magnitude_note: P lower than L by>=0.5bpm; practical effect bar, NOT a measured
    power/noise floor.
- diagnostic_id: eiv_transfer_content_gap
  direction: up
  magnitude_note: P must demonstrate native content utility versus both nulls; no
    output-change-only claim.
problem_id: eiv-functional-sensor-interface-transfer
problem_tags:
- optihealth-eiv
- matched-baseline
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- cross-modal
- medgemma
- sensor-text
---

## Statement
Under the locked repaired-subject-split recipe, P42 connector-only will improve failure-inclusive HR validation MAE over L42 LoRA by at least0.5bpm and preserve sparse deployment performance, because task-level connector gradients make the existing sensor code usable by a fixed receiver. This is a conditional allocation comparison, not proof of universal superiority or equivalent parameter capacity.

## Rationale
Rep2Text/BLIP-2 demonstrate learned input interfaces; old E23 kept LoRA and is not this test. Geometry/subspace probes E29-E31 cannot establish functional receiver use. Source tower must be newly trained after the A/B filtering repair.

## Plan
Approved plan /root/optiHealth-EiV/.hermes/plans/2026-09-18_130810-sensor-latent-transfer-campaign.md; sha256=f8fda40866318cae3b47155a5dc53b2ef46857dac8783b4688abb3696c626df4. Final executable protocol/code/model/data hashes will be committed in start_experiment.config_note before full training. Budget24h including GPU infrastructure; one shared train-only A/B tower, Cseed42 then conditional paired Cseed43. No test-output-based tuning.

## Links
Tests problem [[eiv-functional-sensor-interface-transfer]].
Applies [[eiv-transfer-connector-only]].
Predicts [[eiv_transfer_fimae_hr_val]] (down).
Predicts [[eiv_transfer_content_gap]] (up).
Gate `g0-integrity` (blocking): Actual A, B-pair, B-triplet and C admitted subject sets disjoint from val/test; all hashes match; no legacy v41 upstream weights reused; identical locked rows/budget. Test-panel outputs remain unopened.
Gate `g1-worthwhile` (blocking): L-P failure-inclusive MAE>=0.5bpm AND subject-clustered paired95%CI lower bound>0 on locked validation. Threshold is engineering, not statistical MDE.
Gate `g2-coverage` (blocking): P numeric parse coverage>=0.99 and no more than0.01 below L; failure-inclusive denominator unchanged.
Gate `g3-deployment` (blocking): Upper95% paired day-block-bootstrap bound of P-L Supabase loss<=0.5bpm. Seen-subject guard only; insufficient blocks=not reached.
Gate `g4-content` (blocking): Native beats zero, norm-matched random AND metadata-only controls with clustered CI excluding0; donor direction accuracy>=0.65 AND donor-output MAE to donor truth<MAE to target truth. At least24 donor pairs/8 subjects on validation or gate not reached. Check coverage/slot-mask matching; no gold-output prefix.
Gate `g5-executed` (blocking): Real train-save-load-generate smoke, frozen original LM weights, nonzero connector gradient, no NaN/OOM, actual mode stored and reloaded.
Cites [[rep2text-2511-06571]], [[xbridge-2608-11676]], [[c2c-2510-03215]].

#cross-modal #medgemma #sensor-text #optihealth-eiv #matched-baseline
