---
ambition_class: mechanism
created_at: '2026-08-31T06:34:38+00:00'
former_ids: []
gap_closure_estimate: 0.7
gates:
- blocking: true
  description: 'g0 BLOCKING (auto-enforced): v29all01 phase_a eff_rank >= 7.0. Bracket:
    singles at 0.3 read 8.17-8.60, the 0.3-triple read 5.94, no-heads 7.44. PASS ->
    the interaction poison is dose-dependent and 0.1 clears it; FAIL -> interaction
    persists even at 0.1, full utilisation needs detached probes.'
  diagnostic_id: eiv_effective_rank
  id: g0-triple-at-01-keeps-geometry
  mde: 1.0
- blocking: true
  description: 'g1 BLOCKING (beat the baseline, the goal''s letter): supabase hr_raw
    MAE <= 2.836 with corr >= 0.60. Comparator v28affect01 (affect-only 0.1): 3.030
    / 0.793. The diary+context additions supply pmdata self-reports and LifeSnaps
    mood/place on top of affect -- if label synergy exists at the safe dose, this
    is where it shows.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g1-beats-baseline-letter
  mde: 0.8
- blocking: false
  description: 'g2 advisory: no-harm vs v28 (supa MAE within 2.23-3.83, corr >= 0.60)
    -- full utilisation costs nothing over affect-only.'
  diagnostic_id: eiv_gen_peak_corr
  id: g2-no-harm-vs-v28
  mde: 0.1
id: full-utilisation-at-the-safe-dose-the-v29-chain-616efb
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'If head interactions scale with weight (0.3 triple = -1.50 rank;
    naive 1/3 scaling -> -0.17, invisible), the 0.1-triple keeps v28''s gains and
    may add diary/context signal. Prior: g0 ~70%, g1 ~35% (the 2.836 letter is 0.19
    below v28 and label heads'' marginal deployment value is unproven beyond affect).
    Calibration 0.408; gates decide.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
- subject-heldout
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- deployment-distribution
- sensor-text
- baseline
- contrastive
- wearables
- epoch-resolution
- control
---

## Statement
FULL UTILISATION AT THE SAFE DOSE: the v29 chain (strat-A + diary 0.1 + context 0.1 + affect 0.1 + statemask -> B@30 -> band04 C) keeps eff_rank >= 7.0 AND beats the baseline letter (supabase MAE <= 2.836, corr >= 0.60), completing the utilise-all-labels goal in one chain.

## Rationale
The dose ladder so far: 0.3-triple poisons rank (-1.50, v22); 0.3 singles are rank-positive but affect at 0.3 breaks calibration (+1.5 MAE, v27); 0.1 affect is the best chain on the books (v28). The remaining cell of the design is the triple at 0.1 -- it either completes the maximal-utilisation goal in one arm or proves the interaction is not dose-linear, forcing the detached-probe path. Geometry gate makes the failure cheap (~25 min).

## Plan
Full chain, one supervision-dose variable: phase_a_12ch_v29all01 = v20strat + {diary 0.1, context 0.1, affect 0.1, statemask} (dict-diff verified). Geometry gate between A and B (abort exit 42 -- if the 0.1-triple still poisons rank, no B/C spend). Then B@30 -> band04 C, same scoring. Comparators: v28affect01 3.030/0.793 (affect-only), v25r30 3.203/0.717 (none). This is the maximal-utilisation arm at the measured safe dose: every wired head, all 6 affect corpora, pmdata diary, LifeSnaps context, statemask hygiene.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Gate `g0-triple-at-01-keeps-geometry` (blocking): g0 BLOCKING (auto-enforced): v29all01 phase_a eff_rank >= 7.0. Bracket: singles at 0.3 read 8.17-8.60, the 0.3-triple read 5.94, no-heads 7.44. PASS -> the interaction poison is dose-dependent and 0.1 clears it; FAIL -> interaction persists even at 0.1, full utilisation needs detached probes.
Gate `g1-beats-baseline-letter` (blocking): g1 BLOCKING (beat the baseline, the goal's letter): supabase hr_raw MAE <= 2.836 with corr >= 0.60. Comparator v28affect01 (affect-only 0.1): 3.030 / 0.793. The diary+context additions supply pmdata self-reports and LifeSnaps mood/place on top of affect -- if label synergy exists at the safe dose, this is where it shows.
Gate `g2-no-harm-vs-v28` (advisory): g2 advisory: no-harm vs v28 (supa MAE within 2.23-3.83, corr >= 0.60) -- full utilisation costs nothing over affect-only.

#deployment-distribution #sensor-text #baseline #contrastive #wearables #epoch-resolution #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
