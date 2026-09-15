---
ambition_class: mechanism
created_at: '2026-08-31T01:16:30+00:00'
former_ids: []
gap_closure_estimate: 0.7
gates:
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr >= 0.55 (the B-free class). Comparators,
    same A and C: B@25+triplet 0.327; B@25 no-triplet 0.240; no B 0.567; old-corpus
    B 0.583. PASS -> the small-anchor law unifies both defect sites and B returns
    to the recipe at batch 92. FAIL -> B is toxic beyond anchor count (projector drift
    next).'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-anchor-parity-heals-b
  mde: 0.1
- blocking: false
  description: 'g2 advisory: MAE < 5.414 (v23''s no-B) AND corr > 0.567 -- B earns
    its place back outright.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-b-earns-its-place
  mde: 0.8
id: anchor-parity-heals-b-the-unified-small-anchor-c0e5ae
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'The unified law: small-anchor contrastive on post-rebuild corpora
    collapses geometry wherever it runs. A was healed by 16->92 anchors (eff_rank
    4.47->7.44, exp-v20 supports). B runs the same InfoNCE at 25 anchors and degrades
    C by -0.24 to -0.33 corr (v21/v24). If anchor count is the mechanism at B too,
    92 should recover the B-free class. Prior ~60%: the mechanism is measured at A
    and the objective is shared; the counter-scenario is projector drift, which anchors
    cannot fix. Calibration 0.408; gates decide.'
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags:
- matched-baseline
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: testing
supersedes: null
topic_tags:
- collapse
- contrastive
- deployment-distribution
- sensor-text
- epoch-resolution
---

## Statement
ANCHOR PARITY HEALS B (the unified small-anchor law): phase_b_12ch_v25bigbatch (batch_size 25 -> 92, one variable) off the same v20strat A, followed by the same band04 C, lands in the B-free class (supabase corr >= 0.55) -- where B@25 read 0.327 (triplet on) and 0.240 (triplet off).

## Rationale
exp-v24 exonerated the triplet and left B's own InfoNCE as the prime suspect, unifying both defect sites under one candidate law: contrastive stages on this corpus need ~90+ anchors/step. A's fix was restoring stratified_batches (92 anchors); B has no stratified mechanism in run_phase_b_orthogonalization, but raw batch_size is the same lever. batch 25 -> 92 is the exact anchor-parity test.

## Plan
One variable vs exp-v21's B: phase_b_12ch_v25bigbatch = phase_b_12ch_v11full with batch_size 25 -> 92 (dict diff verified; VRAM check implicit -- B holds no LLM, the text cache is precomputed, 92x1440x24 floats is ~32 MB). Same v20strat A, same band04 C, same scoring. Runner scratch/run_v25_bigbatch.sh. If VRAM fails the run dies loudly at B and costs nothing downstream.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-anchor-parity-heals-b` (blocking): g1 BLOCKING: supabase hr_raw corr >= 0.55 (the B-free class). Comparators, same A and C: B@25+triplet 0.327; B@25 no-triplet 0.240; no B 0.567; old-corpus B 0.583. PASS -> the small-anchor law unifies both defect sites and B returns to the recipe at batch 92. FAIL -> B is toxic beyond anchor count (projector drift next).
Gate `g2-b-earns-its-place` (advisory): g2 advisory: MAE < 5.414 (v23's no-B) AND corr > 0.567 -- B earns its place back outright.

#collapse #contrastive #deployment-distribution #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
