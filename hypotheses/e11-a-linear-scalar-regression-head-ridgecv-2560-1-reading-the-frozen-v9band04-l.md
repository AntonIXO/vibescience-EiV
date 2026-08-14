---
created_at: '2026-08-14T10:01:49+00:00'
gates:
- blocking: true
  description: dense-128 hr_peak head MAE < min(always-median 16.95, hr_raw-derived
    17.73) with paired bootstrap CI95 of the per-row |err| difference excluding zero
    — positive skill, the sign-flip E9 failed to deliver.
  id: g1-dense-peak-head-skill
- blocking: true
  description: 'donor-prefix control: refit/score with permuted soft prefixes; donor
    MAE minus real MAE must be materially positive (conditioning is per-sample, not
    a population prior). Required control of eiv_gen_peak_skill_bpm family.'
  id: g2-donor-gap-positive
- blocking: false
  description: supabase-200 hr_peak head MAE vs trivial 17.59 — transfer to the sparse
    deployment population (E8 supply was measured dense-only; this is the open question
    the probe decides).
  id: g3-supabase-transfer
- blocking: false
  description: 'hrv_raw head: dense corr > 0.60 (beat emission path) and supa MAE
    vs trivial 8.31; hr_raw head R2 >= 0.9 dense as within-probe positive control.'
  id: g4-hrv-and-raw-controls
id: e11-a-linear-scalar-regression-head-ridgecv-2560-1-reading-the-frozen-v9band04-l
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_gen_peak_donor_gap_bpm
  direction: up
  magnitude_note: ''
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
problem_tags: []
status: inconclusive
supersedes: null
topic_tags:
- readout
- deployment-distribution
- medgemma
- optihealth
- collapse
- baseline
---

## Statement
E11: A LINEAR scalar regression head (RidgeCV, 2560->1) reading the frozen v9band04 LM's final-layer (L34) hidden state at each numeric slot's value-emitting position recovers deployment-relevant numeric accuracy that greedy lm_head decoding destroys — because E6-E10 proved the supply reaches L34 (peak increment +0.5032, hrv prefix R2 0.969) and E9 proved CLM digit-CE cannot teach lm_head to read it, while E28 proved a linear scalar readout of the SAME kind of hidden state achieves MAE 11.8 vs trivial 20.1 on e23v2. Design constraints from the record: LINEAR only (E28 rule-out: non-linear heads add nothing — the cap is information, not capacity); eval features must come from SELF-GENERATED prefixes (the stored v9band04_s42 capture texts), not oracle prefixes, so the measurement matches the annotate-path distribution; batch-1 forwards only (batched-generate rule-out). Prediction: dense-128 hr_peak head MAE 11-13 bpm vs strongest trivial ~16.95 (skill +4 to +6, sign-flip from -2.88); hrv dense strong; supabase peak/hrv = open transfer question the probe decides. If confirmed, arm B (productionizing the head into eiv annotate: text+head merge) becomes the deployment path pending Anton's architecture sign-off.

## Rationale


## Plan


## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Predicts [[eiv_gen_peak_skill_bpm]] (up).
Predicts [[eiv_gen_peak_donor_gap_bpm]] (up).
Gate `g1-dense-peak-head-skill` (blocking): dense-128 hr_peak head MAE < min(always-median 16.95, hr_raw-derived 17.73) with paired bootstrap CI95 of the per-row |err| difference excluding zero — positive skill, the sign-flip E9 failed to deliver.
Gate `g2-donor-gap-positive` (blocking): donor-prefix control: refit/score with permuted soft prefixes; donor MAE minus real MAE must be materially positive (conditioning is per-sample, not a population prior). Required control of eiv_gen_peak_skill_bpm family.
Gate `g3-supabase-transfer` (advisory): supabase-200 hr_peak head MAE vs trivial 17.59 — transfer to the sparse deployment population (E8 supply was measured dense-only; this is the open question the probe decides).
Gate `g4-hrv-and-raw-controls` (advisory): hrv_raw head: dense corr > 0.60 (beat emission path) and supa MAE vs trivial 8.31; hr_raw head R2 >= 0.9 dense as within-probe positive control.

#readout #deployment-distribution #medgemma #optihealth #collapse #baseline
