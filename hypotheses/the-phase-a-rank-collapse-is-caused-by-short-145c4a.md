---
ambition_class: mechanism
created_at: '2026-08-30T18:33:05+00:00'
former_ids: []
gap_closure_estimate: 0.6
gates:
- blocking: true
  description: 'g1 BLOCKING (geometry, cheap, decided WITHOUT a C arm): the v19longA
    phase_a token cloud''s effective rank on the standard 300-row geometry probe must
    land at >= 7.0 -- i.e. recover at least ~70% of the healthy-vs-carrier gap (ab_ctrl
    phase_a 8.43, v11full phase_a 4.58, midpoint 6.5; the bar sits above the midpoint
    because a partial nudge is not a mechanism confirmation). Same probe script, same
    300 eval rows, same production path.'
  diagnostic_id: eiv_effective_rank
  id: g1-long-pool-recovers-phase-a-rank
  mde: 1.0
- blocking: false
  description: 'g2 advisory: inter-day cosine p50 of the phase_a cloud <= 0.16 (ab_ctrl
    phase_a reads 0.181, v11full phase_a 0.140 -- NOTE at phase_a the carrier''s cosine
    is LOWER, the discriminating axis at A is eff_rank; recorded to keep the full
    signature).'
  diagnostic_id: eiv_sensor_median_cosine
  id: g2-cosine-signature-recorded
  mde: 0.05
id: the-phase-a-rank-collapse-is-caused-by-short-145c4a
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: 'Mechanism: InfoNCE on windows whose prefixes are ~empty (27-41
    HR-min of 1440) gives the encoder pairs it cannot distinguish by content; the
    optimal embedding for indistinguishable positives/negatives is a shared direction
    -- rank collapse. Cutting them should recover most of the 4.58->8.43 gap. Prior
    ~55%: the same ''cut short cohorts'' lever FAILED at the C stage (v14), but the
    mechanism there was different (C reads tokens, A MAKES them) and the geometry
    axis is new. Calibration 0.408 overall, eiv_effective_rank 5/14. Gates decide.'
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags:
- matched-baseline
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- collapse
- contrastive
- sensor-text
- deployment-distribution
- epoch-resolution
---

## Statement
The Phase-A rank collapse is caused by SHORT NEAR-EMPTY WINDOWS IN THE InfoNCE POOL: training phase_a_12ch_v19longA (v11full profile with dataset_filter cut to the 7 long cohorts, one variable) on the fixed [14041] bundle recovers the token-cloud effective rank to >= 7.0 (v11full phase_a: 4.58; healthy ab_ctrl phase_a: 8.43), measured by the standard geometry probe with no C arm needed.

## Rationale
The v17/v18 double dissociation convicted the encoder checkpoint; the 5-way geometry probe localised the damage to PHASE A (v11full phase_a eff_rank 4.58 vs ab_ctrl phase_a 8.43, -46% BEFORE Phase B; B partially regrows rank for both lineages but the deficit inherits). What differs between those two Phase-A runs is the corpus and hence the InfoNCE pool: the post-rebuild pool admits ~1,989 windows from 14 short lab cohorts, nine of them 100% under 60 HR-minutes -- near-empty prefixes as contrastive views. The old healthy pool had p05=330 HR-min. One 4-minute Phase A with the pool cut to long cohorts, read out by the geometry probe, tests the mechanism without spending a C arm.

## Plan
Phase A ONLY (~4 min GPU), profile phase_a_12ch_v19longA = phase_a_12ch_v11full with dataset_filter cut to the 7 long cohorts (verified by dict diff: only dataset_filter+notes differ; weights all 1.0). NO C ARM -- the v18 do-not-retry forbids funding C as a probe; the readout is the token-geometry probe (scratch/_token_geometry_probe.py) on the fresh phase_a checkpoint against the measured anchors. Decision rule: g1 PASS -> the short-window InfoNCE pool is the rank-collapse mechanism; next step is the production fix (min_hr_coverage gate at Phase A -- the parameter ALREADY EXISTS in SensorTextPairDataset -- or the long-roster A/B), then ONE C arm off the recovered encoder to confirm end-to-end. g1 FAIL -> pool composition is not the A-stage mechanism either; next suspects inside Phase A training on new corpora: text-caption distribution shift (hash captions of near-identical days), batch composition, or the 2026-08-24 tensor content itself; bracket with a phase_a geometry probe on baseline_sync/phase_a ([13518]).

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_effective_rank]] (up).
Gate `g1-long-pool-recovers-phase-a-rank` (blocking): g1 BLOCKING (geometry, cheap, decided WITHOUT a C arm): the v19longA phase_a token cloud's effective rank on the standard 300-row geometry probe must land at >= 7.0 -- i.e. recover at least ~70% of the healthy-vs-carrier gap (ab_ctrl phase_a 8.43, v11full phase_a 4.58, midpoint 6.5; the bar sits above the midpoint because a partial nudge is not a mechanism confirmation). Same probe script, same 300 eval rows, same production path.
Gate `g2-cosine-signature-recorded` (advisory): g2 advisory: inter-day cosine p50 of the phase_a cloud <= 0.16 (ab_ctrl phase_a reads 0.181, v11full phase_a 0.140 -- NOTE at phase_a the carrier's cosine is LOWER, the discriminating axis at A is eff_rank; recorded to keep the full signature).

#collapse #contrastive #sensor-text #deployment-distribution #epoch-resolution #matched-baseline #optihealth-eiv
