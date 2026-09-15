---
ambition_class: mechanism
created_at: '2026-08-30T21:40:16+00:00'
former_ids: []
gap_closure_estimate: 0.5
gates:
- blocking: true
  description: 'g0 BLOCKING (auto-enforced by runner): v22max phase_a eff_rank >=
    7.0 on the standard geometry probe. Added supervision must NOT re-collapse the
    cloud the strat fix just recovered (v20: 7.44). Runner aborts before B/C on failure.'
  diagnostic_id: eiv_effective_rank
  id: g0-supervision-keeps-geometry
  mde: 1.0
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr >= 0.60 after the full A->B->C chain.'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-corr-healthy
  mde: 0.1
- blocking: true
  description: 'g2 BLOCKING (the beat-baseline bar): supabase hr_raw MAE <= 2.836
    -- the v10conly family MEDIAN, i.e. the recorded baseline. skill > 0 alone is
    not enough for this arm; it exists to justify the label supervision against the
    plain-strat v21 chain.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-beats-v10-baseline-median
  mde: 0.8
- blocking: false
  description: 'g3 advisory: dense-128 hr_raw MAE < 8.0.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-advisory
  mde: 1.0
id: maximal-label-utilisation-on-the-repaired-9c2265
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'Honest split: g0 (geometry survives supervision) ~75% -- the heads
    are per-patch probes with 0.3 weights, small against InfoNCE, and the affect arm
    precedent (phase_a_12ch_analog_affect) trained without collapse on the old corpus.
    g2 (beats 2.836) ~35% -- label heads have never yet moved the HR deployment axis
    in this repo''s record (affect/privileged arms moved their own probes, not supabase
    MAE), so the likely outcome is g0 PASS + g2 marginal. The arm is still worth one
    chain: it is the first time ALL the new ground truth reaches training on a geometrically-healthy
    encoder. Calibration 0.408; gates decide.'
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags:
- matched-baseline
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- deployment-distribution
- sensor-text
- baseline
- contrastive
- collapse
- epoch-resolution
---

## Statement
MAXIMAL LABEL UTILISATION on the repaired geometry: the v22max chain (Phase A = strat fix + diary/context label heads + affect head + statemask, 19-ds whitelist; B/C unchanged) keeps the token-cloud eff_rank >= 7.0 AND beats the recorded baseline on deployment -- supabase hr_raw MAE <= 2.836 (v10conly family median) with corr >= 0.60.

## Rationale
Anton's directive: after the regression fix, utilise the most datasets and labels and beat the baseline. The root cause is confirmed (exp-v20) and guarded (commit 767ebd8); v21 (strat-only chain) is the minimal-fix arm in flight. This arm is the maximal-utilisation complement: same fixed geometry, plus every label head that is safe on the current evidence (diary + context from ddb7498's registry, affect at 0.3 over the 19-ds supply, statemask hygiene). Roster stays the 19-ds whitelist -- dryad excluded per the standing rule, stress_raw head deferred with it. v21-vs-v22 then isolates the supervision block exactly.

## Plan
Queued behind v21 on the GPU (runner waits for v21 CHAIN DONE). Phase A = phase_a_12ch_v22max: v20strat + supervision block {label_head_weights {diary:0.3, context:0.3}, affect_weight 0.3, mask_unlabelled_state true} -- deliberately compound vs v20strat because v20 fixed the geometry and this asks whether ADDED supervision keeps it while reaching 2.8x more ground truth (452,607 label-epochs; diary/context are ddb7498's new heads, affect reaches dapper/k_emocon/popane_s5-s7 for the first time in a chain). stress_raw head deferred: its only source dryad_ifh_affect is A/B-excluded by the standing hr_peak-mode-collapse record. Geometry gate between A and B (abort exit 42). B = phase_b_12ch_v11full unchanged; C = byte-untouched band04; scoring same instrument. Comparators: v21 chain (strat-only, running now) isolates the supervision block's contribution; v10conly median 2.836 is the baseline bar. Decision rule: g0 FAIL -> supervision re-collapses geometry, drop to per-head bisection (diary/context/affect one at a time, 20 min each, geometry-gated); g0 PASS + g2 FAIL but skill > 0 -> supervision is geometry-safe but not deployment-accretive on hr_raw -- keep heads for their own label probes, baseline stays v21; all PASS -> the maximal-utilisation chain is the new baseline candidate.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Gate `g0-supervision-keeps-geometry` (blocking): g0 BLOCKING (auto-enforced by runner): v22max phase_a eff_rank >= 7.0 on the standard geometry probe. Added supervision must NOT re-collapse the cloud the strat fix just recovered (v20: 7.44). Runner aborts before B/C on failure.
Gate `g1-corr-healthy` (blocking): g1 BLOCKING: supabase hr_raw corr >= 0.60 after the full A->B->C chain.
Gate `g2-beats-v10-baseline-median` (blocking): g2 BLOCKING (the beat-baseline bar): supabase hr_raw MAE <= 2.836 -- the v10conly family MEDIAN, i.e. the recorded baseline. skill > 0 alone is not enough for this arm; it exists to justify the label supervision against the plain-strat v21 chain.
Gate `g3-dense-advisory` (advisory): g3 advisory: dense-128 hr_raw MAE < 8.0.

#deployment-distribution #sensor-text #baseline #contrastive #collapse #epoch-resolution #matched-baseline #optihealth-eiv
