---
ambition_class: mechanism
created_at: '2026-08-30T18:42:43+00:00'
former_ids: []
gap_closure_estimate: 0.8
gates:
- blocking: true
  description: 'g1 BLOCKING: v20strat phase_a token-cloud effective rank >= 7.0 on
    the standard 300-row geometry probe (same-run anchors: healthy ab_ctrl phase_a
    8.35, carrier v11full phase_a 4.47, v19longA 5.19). Recovering >= 70% of the gap
    convicts the anchors-per-batch drift as the mechanism.'
  diagnostic_id: eiv_effective_rank
  id: g1-stratified-batches-recover-rank
  mde: 1.0
- blocking: false
  description: 'g2 advisory: cos_p95 <= 0.80 (healthy phase_a 0.695, carrier 0.891,
    v19 0.881) -- the top of the similarity distribution is where v19''s partial move
    failed; record it.'
  diagnostic_id: eiv_sensor_median_cosine
  id: g2-cos-p95-top-of-distribution
  mde: 0.05
id: the-phase-a-rank-collapse-is-caused-by-the-d5e8e6
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: 'MECHANISM, from the two shipped phase_configs: ab_ctrl''s Phase
    A ran stratified_batches giving 92 anchors/batch = 184,000 anchors over 2000 steps;
    v11full ran plain batch_size 16 = 32,000 -- a 5.75x cut in InfoNCE negatives per
    step. Fewer negatives = weaker uniformity pressure, and the shipped losses agree:
    final uniformity -6.83 (healthy) vs -5.41 (carrier), final InfoNCE 1.377 vs 0.287
    (the carrier''s loss is EASIER, exactly what fewer negatives does). Uniformity
    pressure is what spreads the cloud; without it the cloud contracts onto shared
    directions = rank collapse. This is the first candidate whose mechanism PREDICTS
    the measured geometry axis directly. Prior ~65%. Calibration 0.408; gates decide.'
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags:
- matched-baseline
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags:
- collapse
- contrastive
- sensor-text
- deployment-distribution
- epoch-resolution
---

## Statement
The Phase-A rank collapse is caused by the ANCHOR-COUNT DRIFT: v11-era Phase-A profiles dropped stratified_batches (92 anchors/batch in the healthy lineage -> plain batch_size 16), cutting InfoNCE anchors 184k -> 32k and uniformity pressure with it. Restoring stratified_batches alone (phase_a_12ch_v20strat, one variable vs v11full) recovers the token-cloud effective rank to >= 7.0 (carrier 4.47, healthy 8.35).

## Rationale
exp-v19 refuted pool composition at the A stage; the preregistered next bracket was recipe drift, and the DIRECT diff of the two shipped phase_config.json files (not profile YAML -- the artifacts that actually ran) found it: the healthy lineage trained with stratified_batches {lifesnaps:45,pmdata:12,*:35} while every v11-era profile dropped the key (None), silently cutting anchors per batch 92 -> 16 and total anchors 184k -> 32k. The shipped loss traces corroborate the mechanism (uniformity -6.83 vs -5.41). This also retro-explains why EVERY post-rebuild A run is a carrier regardless of corpus content: the v11 profile family was written fresh without the key, and baseline_sync/analog arms inherited the plain batch path. Single variable, 8 GPU-minutes, geometry readout.

## Plan
Phase A ONLY (~4-8 min GPU; 92 anchors/batch costs more than 16), profile phase_a_12ch_v20strat = phase_a_12ch_v11full + stratified_batches {lifesnaps:45, pmdata:12, *:35} (dict diff verified: only stratified_batches+notes). Full 19-ds roster kept -- this deliberately does NOT stack with v19's roster cut, so the two arms bracket the two candidate mechanisms independently. Readout: geometry probe, no C arm (v18 do-not-retry). Decision rule: g1 PASS -> the anchors/negatives drift is the mechanism; production fix = restore stratified_batches (or equivalent anchor count) in every v11-era A profile, retrain A/B once (~15 min), geometry-check, then ONE C arm end-to-end and the collapse problem closes with a named root cause. g1 FAIL -> recipe drift eliminated for the batch-shape axis; next bracket is corpus CONTENT (v11full profile on canonical_1min_conv, 4 min) and the remaining drift keys (state_target soft/hard, disable_state_probe False/True) in one combined arm.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_effective_rank]] (up).
Gate `g1-stratified-batches-recover-rank` (blocking): g1 BLOCKING: v20strat phase_a token-cloud effective rank >= 7.0 on the standard 300-row geometry probe (same-run anchors: healthy ab_ctrl phase_a 8.35, carrier v11full phase_a 4.47, v19longA 5.19). Recovering >= 70% of the gap convicts the anchors-per-batch drift as the mechanism.
Gate `g2-cos-p95-top-of-distribution` (advisory): g2 advisory: cos_p95 <= 0.80 (healthy phase_a 0.695, carrier 0.891, v19 0.881) -- the top of the similarity distribution is where v19's partial move failed; record it.

#collapse #contrastive #sensor-text #deployment-distribution #epoch-resolution #matched-baseline #optihealth-eiv
