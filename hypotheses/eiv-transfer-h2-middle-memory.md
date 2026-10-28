---
ambition_class: mechanism
created_at: '2026-09-18T11:14:34+00:00'
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
  description: E-M failure-inclusive MAE>=0.5bpm AND subject-clustered paired95%CI
    lower bound>0 on locked validation. Threshold is engineering, not statistical
    MDE.
  diagnostic_id: eiv_transfer_fimae_hr_val
  id: g1-worthwhile
  mde: null
- blocking: true
  description: M numeric parse coverage>=0.99 and no more than0.01 below E; failure-inclusive
    denominator unchanged.
  diagnostic_id: eiv_transfer_parse_rate
  id: g2-coverage
  mde: null
- blocking: true
  description: Upper95% paired day-block-bootstrap bound of M-E Supabase loss<=0.5bpm.
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
    sensor-memory/gate gradient and unchanged frozen projector, no NaN/OOM, actual
    mode stored and reloaded.
  diagnostic_id: eiv_transfer_fimae_hr_val
  id: g5-executed
  mde: null
id: eiv-transfer-h2-middle-memory
interventions:
- eiv-transfer-memory-depth
override_reason: ''
paper_refs:
- rep2text-2511-06571
- xbridge-2608-11676
- c2c-2510-03215
predicted_effects:
- diagnostic_id: eiv_transfer_fimae_hr_val
  direction: down
  magnitude_note: M lower than E by>=0.5bpm; engineering bar.
- diagnostic_id: eiv_transfer_content_gap
  direction: up
  magnitude_note: M preserves content-specific sensor utility.
problem_id: eiv-functional-sensor-interface-transfer
problem_tags:
- optihealth-eiv
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- medgemma
- sensor-text
---

## Statement
Moving the identical gated96-slot sensor-memory interface from receiver layer0(E42) to layer17(M42) reduces validation failure-inclusive HR MAE by>=0.5bpm while passing coverage, native-content, sparse-deployment and execution gates. Only insertion depth changes; source tokens, width, parameters and initialization are identical.

## Rationale
C2C/XBridge motivate residual gated access; unlike arbitrary heterogeneous KV copying, this uses explicit sensor memory. Early-vs-middle module comparison isolates depth from added capacity or a longer source representation.

## Plan
Approved plan /root/optiHealth-EiV/.hermes/plans/2026-09-18_130810-sensor-latent-transfer-campaign.md; sha256=f8fda40866318cae3b47155a5dc53b2ef46857dac8783b4688abb3696c626df4. Final executable protocol/code/model/data hashes will be committed in start_experiment.config_note before full training. Budget24h including GPU infrastructure; one shared train-only A/B tower, Cseed42 then conditional paired Cseed43. No test-output-based tuning.

## Links
Tests problem [[eiv-functional-sensor-interface-transfer]].
Applies [[eiv-transfer-memory-depth]].
Predicts [[eiv_transfer_fimae_hr_val]] (down).
Predicts [[eiv_transfer_content_gap]] (up).
Gate `g0-integrity` (blocking): Actual A, B-pair, B-triplet and C admitted subject sets disjoint from val/test; all hashes match; no legacy v41 upstream weights reused; identical locked rows/budget. Test-panel outputs remain unopened.
Gate `g1-worthwhile` (blocking): E-M failure-inclusive MAE>=0.5bpm AND subject-clustered paired95%CI lower bound>0 on locked validation. Threshold is engineering, not statistical MDE.
Gate `g2-coverage` (blocking): M numeric parse coverage>=0.99 and no more than0.01 below E; failure-inclusive denominator unchanged.
Gate `g3-deployment` (blocking): Upper95% paired day-block-bootstrap bound of M-E Supabase loss<=0.5bpm. Seen-subject guard only; insufficient blocks=not reached.
Gate `g4-content` (blocking): Native beats zero, norm-matched random AND metadata-only controls with clustered CI excluding0; donor direction accuracy>=0.65 AND donor-output MAE to donor truth<MAE to target truth. At least24 donor pairs/8 subjects on validation or gate not reached. Check coverage/slot-mask matching; no gold-output prefix.
Gate `g5-executed` (blocking): Real train-save-load-generate smoke, frozen original LM weights, nonzero sensor-memory/gate gradient and unchanged frozen projector, no NaN/OOM, actual mode stored and reloaded.
Cites [[rep2text-2511-06571]], [[xbridge-2608-11676]], [[c2c-2510-03215]].

#cross-modal #medgemma #sensor-text #optihealth-eiv #matched-baseline
