---
created_at: '2026-08-14T10:58:34+00:00'
former_ids:
- e11-s-supabase-transfer-failure-is-caused-by-coverage-covariate-shift-and-is-fix
gates:
- blocking: true
  description: 'MECHANISM: E11''s plain dense-fit heads, scored on coverage-degraded
    held-out dense rows (supabase-law masks, truth known), lose material skill vs
    the same rows unmasked — hrv_raw skill drop ≥ 3 MAE-units or sign flip. Establishes
    coverage shift (not supabase population/device weirdness) as the cause of E11''s
    transfer failure.'
  diagnostic_id: null
  id: g1-coverage-shift-mechanism
  mde: null
- blocking: true
  description: 'RECOVERY IN-DISTRIBUTION: mix-trained heads (plain + coverage-augmented
    views) achieve positive hrv_raw skill on held-out coverage-degraded dense rows,
    paired bootstrap CI95 > 0. Shows the fix works where truth is knowable.'
  diagnostic_id: null
  id: g2-augmented-recovery-dense
  mde: null
- blocking: false
  description: 'DEPLOYMENT PRIZE: frozen supabase-200 captures — mix-trained hrv_raw
    head MAE < trivial always-median 8.314 (E11 plain: 13.675). Secondary: hr_peak
    supa skill improvement vs E11''s −6.967. If g1+g2 pass but g3 fails, the residual
    is population shift beyond coverage — decisive either way.'
  diagnostic_id: null
  id: g3-supabase-deployment
  mde: null
- blocking: false
  description: 'CONTROLS: donor-prefix gap positive on augmented eval rows; shuffled-target
    refit destroys skill; hr_raw head stays CI-positive on supa (E11: +1.007).'
  diagnostic_id: null
  id: g4-controls
  mde: null
id: e11-s-supabase-transfer-failure-is-5472c8
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: up
  magnitude_note: 'supabase hrv head skill vs trivial: E11 plain −5.36 → positive
    if coverage is the whole story; partial recovery if population shift also contributes'
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
  magnitude_note: supa peak head skill −6.97 → toward 0; dense peak unchanged (~+1.7,
    CI-weak)
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
problem_tags: []
status: inconclusive
supersedes: null
topic_tags:
- readout
- deployment-distribution
- medgemma
- optihealth
- baseline
- collapse
---

## Statement
E11's supabase transfer failure is caused by COVERAGE covariate shift, and is fixable at the head level: linear RidgeCV heads trained on a mix of plain and coverage-augmented dense self-prefix L34 features (supabase-law block masks: hr coverage ~U[0.57,0.95] contiguous-block dropout, hrv thinned to ~0.33) will (1) recover positive skill on coverage-degraded dense rows and (2) improve supabase deployment skill, without any LM training.

## Rationale
E11 measured a monotone covariate shift: dense-fit L34 linear heads got WORSE on supabase as dense n_train grew (skill −1.74@n40 → −6.97@n700) while dense-side skill grew — heads specialize to the dense feature manifold. Supabase coverage law measured 2026-08-14: hr coverage p5–p95 = 0.57–0.95 (median 0.80, block-structured, present-run median ~171 min), hrv coverage median 0.33. Dense lifesnaps/pmdata rows are >0.9. The masked-cell fill (zero = pop median) plus presence-channel input means coverage directly reshapes soft tokens and hence L34 features. If we synthesize supabase-law coverage on dense rows (truth still known!), we can (a) test the mechanism and (b) train heads that straddle both manifolds — zero LM training, E28 linear-only rule-out respected.

## Plan


## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Predicts [[eiv_nonhr_channel_grouped_r2]] (up).
Predicts [[eiv_gen_peak_skill_bpm]] (up).
Gate `g1-coverage-shift-mechanism` (blocking): MECHANISM: E11's plain dense-fit heads, scored on coverage-degraded held-out dense rows (supabase-law masks, truth known), lose material skill vs the same rows unmasked — hrv_raw skill drop ≥ 3 MAE-units or sign flip. Establishes coverage shift (not supabase population/device weirdness) as the cause of E11's transfer failure.
Gate `g2-augmented-recovery-dense` (blocking): RECOVERY IN-DISTRIBUTION: mix-trained heads (plain + coverage-augmented views) achieve positive hrv_raw skill on held-out coverage-degraded dense rows, paired bootstrap CI95 > 0. Shows the fix works where truth is knowable.
Gate `g3-supabase-deployment` (advisory): DEPLOYMENT PRIZE: frozen supabase-200 captures — mix-trained hrv_raw head MAE < trivial always-median 8.314 (E11 plain: 13.675). Secondary: hr_peak supa skill improvement vs E11's −6.967. If g1+g2 pass but g3 fails, the residual is population shift beyond coverage — decisive either way.
Gate `g4-controls` (advisory): CONTROLS: donor-prefix gap positive on augmented eval rows; shuffled-target refit destroys skill; hr_raw head stays CI-positive on supa (E11: +1.007).

#readout #deployment-distribution #medgemma #optihealth #baseline #collapse
