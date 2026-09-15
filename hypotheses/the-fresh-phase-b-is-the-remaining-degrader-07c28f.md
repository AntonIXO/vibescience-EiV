---
ambition_class: mechanism
created_at: '2026-08-30T23:41:51+00:00'
former_ids: []
gap_closure_estimate: 0.6
gates:
- blocking: true
  description: 'g1 BLOCKING (the discriminator, preregistered in exp-v21''s interpretation
    before v23 scored): supabase hr_raw corr materially above the fresh-B chain''s
    0.327 and into the v17 near-healthy class (>= 0.55) => the fresh Phase B is the
    degrader; corr staying in the 0.2-0.35 collapse class => a residual A-stage defect
    invisible to eff_rank.'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-discriminate-b-vs-a-residual
  mde: 0.1
- blocking: false
  description: 'g2 advisory: skill > 0 (fully healthy class).'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-fully-healthy
  mde: 0.8
id: the-fresh-phase-b-is-the-remaining-degrader-07c28f
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: If the fresh-B-on-new-corpus is the remaining carrier, dropping
    it should reproduce the v17 class (0.55-0.60). Registered at experiment start
    alongside the already-run training; scoring numbers were recorded by the runner
    before this registration -- stated plainly for calibration accounting.
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
- deployment-distribution
- sensor-text
- epoch-resolution
---

## Statement
THE FRESH PHASE B IS THE REMAINING DEGRADER: band04 C-only trained directly off the strat-fixed v20strat/phase_a (no fresh Phase B) lands in the v17 near-healthy class (supabase corr >= 0.55), while the identical C off the fresh v21chain/phase_b read 0.327 / top1 0.90.

## Rationale
exp-v21 left exactly two carrier candidates: the fresh Phase B trained on the new corpus (all fresh Bs collapse downstream despite ranks 8.7-12.4; v17's near-healthy ran off an OLD-corpus B) vs a residual A-stage property invisible to eff_rank. Removing the fresh B while holding everything else at v21's values separates them in one C arm.

## Plan
C-only (byte-untouched band04, seed 42) directly off outputs/checkpoints/v20strat/phase_a -- the strat-fixed A on the fixed [14041] bundle -- SKIPPING the fresh Phase B that v21 inserted. Only difference vs exp-v21's C stage: the encoder checkpoint (v20strat/phase_a vs v21chain/phase_b). Same scoring instrument. Decision rule preregistered in exp-v21-fixed-chain-20260831's interpretation.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-discriminate-b-vs-a-residual` (blocking): g1 BLOCKING (the discriminator, preregistered in exp-v21's interpretation before v23 scored): supabase hr_raw corr materially above the fresh-B chain's 0.327 and into the v17 near-healthy class (>= 0.55) => the fresh Phase B is the degrader; corr staying in the 0.2-0.35 collapse class => a residual A-stage defect invisible to eff_rank.
Gate `g2-fully-healthy` (advisory): g2 advisory: skill > 0 (fully healthy class).

#collapse #deployment-distribution #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
