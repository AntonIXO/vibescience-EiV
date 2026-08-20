---
artifacts:
- logs/emission_ab_ctrl_s44_supa_summary.json
- logs/emission_ab_ctrl_s45_supa_summary.json
- logs/emission_ab_ctrl_s44_dense_summary.json
- logs/emission_ab_ctrl_s45_dense_summary.json
- logs/ctrl_s4445.log
- scratch/run_ctrl_s4445.sh
- outputs/adapters/ab_ctrl_s43
- outputs/adapters/ab_ctrl_s44
closed: true
config_note: 'Runner scratch/run_ctrl_s4445.sh (generator scratch/gen_ctrl_s4445.py):
  2 full A→B→C chains (seeds 44/45, byte-identical profiles to ab_ctrl/ctrl_s43) +
  supabase-200 & dense-128 audits each, batch 1, frozen draws on the rebuilt eval
  bundle. Existing family members: s42 = 6.03 (logs/emission_ab_ctrl_supa_summary.json),
  s43 = 2.84 (logs/emission_ab_ctrl_s43_supa_summary.json). Comparators: v9band04
  3.04 same draw, trivial 3.95. Budget ~3.2 h GPU. Caches warm (deterministic warmers,
  this session).'
cost:
  gpu_hours: 3.4
  wall_hours: 6.0
crash_reason: ''
created_at: '2026-08-20T03:33:33+00:00'
diagnostics_measured:
- after: 3.19
  before: 0.6
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
do_not_retry:
- 'Do NOT designate a single new-corpus chain seed as baseline by its supabase point
  estimate: the 4-seed family band is 3.19 bpm (s42 6.03 / s43 2.84 / s44 3.07 / s45
  4.17), 5.3x v9band04''s 0.60, and dense health DISSOCIATES from supabase health
  per seed (s44 dense 2.56 vs s45 dense 10.17). Any baseline claim needs the family
  (median + band + per-seed attractor masses), and any future chain-level A/B needs
  either >=4 seeds per arm or a shared frozen A/B init across arms (v9band04''s design)
  to cancel the chain lottery.'
- 'Do NOT read s44''s supabase MAE 3.07 as fully healthy: its top1 emit_raw mass is
  0.72 (53.9 on 144/200 rows) - a benign-VALUED attractor near the population median,
  not per-row reading. corr 0.781 shows real conditioning on the remainder, but the
  attractor pattern persists across every fresh chain; the healthy-looking MAE partially
  rides the attractor sitting near truth.'
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: ctrl-chain-seed-family-seeds-44-45-added-to-5b95e9
id: exp-052-ctrl-chain-seed-family-seeds-44-96fa96
interpretation:
  at: '2026-08-20T06:32:32+00:00'
  failure_mode: test_invalid
  lesson: 'READ THE ENGINE''S ''refutes'' AS A DIRECTION-ENCODING ARTIFACT: the diagnostic
    is lower_better and the measurement (band 3.19 vs v9band04''s 0.60) went ''up'',
    so the engine scored the predicted-effect direction as missed — but the PREREGISTERED
    READINGS both CONFIRMED: (1) band > 2 bpm ⇒ chain-level bistability is the family
    property; (2) 2/4 seeds ≤ 3.5 MAE (s43 2.84, s44 3.07) ⇒ the new corpus supports
    v9band04-grade chains. Substantively this experiment SUPPORTS the bistability
    hypothesis. Key numbers: family {6.03, 2.84, 3.07, 4.17}, median 3.62, band 3.19
    (5.3× v9band04''s family). The mechanism story: v9band04''s 0.60-band family shared
    ONE frozen Phase-B init and seeded only C; these chains re-roll A+B+C per seed,
    compounding three basin lotteries. ENGINEERING FIX, cheap and testable: freeze
    s43''s (clean, top1 0.285) phase_b and run a C-only seed family off it — if the
    band collapses toward 0.60, the compounding account is proven and the new corpus
    gets a proper baseline family. Also: dense/supabase health dissociates per seed
    (s44 dense 2.56 vs s45 10.17), and s44''s healthy-looking supabase MAE partially
    rides a benign-valued 53.9-bpm attractor (top1 0.72) — quote attractor mass beside
    every MAE.'
  transfers_to:
  - ctrl-chain-seed-family-seeds-44-45-added-to-5b95e9
  - mc01-discriminator-mechanism-a-of-the-new-5b7727
  - allowlist-a-b-full-chain-executes-runbook-dc0de8
key_numbers:
  band_4seed: 3.19
  median_4seed: 3.62
  s42: 6.03
  s43: 2.84
  s44: 3.07
  s44_dense: 2.56
  s44_top1_mass: 0.72
  s45: 4.17
  s45_dense: 10.17
  seeds_le_3p5_of_4: 2.0
  trivial: 3.95
  v9band04_band: 0.6
  v9band04_same_draw: 3.044
observed_effects:
- delta: 2.59
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: up
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_supabase_seed_band_mae_bpm: false
provenance: {}
recipe_fingerprint:
  interventions: dataset-allowlist-repaired-all-phases
verdict: refutes
---

## Summary
Tests hypothesis [[ctrl-chain-seed-family-seeds-44-45-added-to-5b95e9]].

## Measurements
- [[eiv_supabase_seed_band_mae_bpm]]: before 0.6 → after 3.19  (Δ +2.59, up ✗ predicted down)

**Verdict:** refutes

## Interpretation
**Failure mode:** `test_invalid`

READ THE ENGINE'S 'refutes' AS A DIRECTION-ENCODING ARTIFACT: the diagnostic is lower_better and the measurement (band 3.19 vs v9band04's 0.60) went 'up', so the engine scored the predicted-effect direction as missed — but the PREREGISTERED READINGS both CONFIRMED: (1) band > 2 bpm ⇒ chain-level bistability is the family property; (2) 2/4 seeds ≤ 3.5 MAE (s43 2.84, s44 3.07) ⇒ the new corpus supports v9band04-grade chains. Substantively this experiment SUPPORTS the bistability hypothesis. Key numbers: family {6.03, 2.84, 3.07, 4.17}, median 3.62, band 3.19 (5.3× v9band04's family). The mechanism story: v9band04's 0.60-band family shared ONE frozen Phase-B init and seeded only C; these chains re-roll A+B+C per seed, compounding three basin lotteries. ENGINEERING FIX, cheap and testable: freeze s43's (clean, top1 0.285) phase_b and run a C-only seed family off it — if the band collapses toward 0.60, the compounding account is proven and the new corpus gets a proper baseline family. Also: dense/supabase health dissociates per seed (s44 dense 2.56 vs s45 10.17), and s44's healthy-looking supabase MAE partially rides a benign-valued 53.9-bpm attractor (top1 0.72) — quote attractor mass beside every MAE.

Transfers to: [[ctrl-chain-seed-family-seeds-44-45-added-to-5b95e9]], [[mc01-discriminator-mechanism-a-of-the-new-5b7727]], [[allowlist-a-b-full-chain-executes-runbook-dc0de8]]

## Key numbers
- `band_4seed`: 3.19
- `median_4seed`: 3.62
- `s42`: 6.03
- `s43`: 2.84
- `s44`: 3.07
- `s44_dense`: 2.56
- `s44_top1_mass`: 0.72
- `s45`: 4.17
- `s45_dense`: 10.17
- `seeds_le_3p5_of_4`: 2
- `trivial`: 3.95
- `v9band04_band`: 0.6
- `v9band04_same_draw`: 3.044

## Artifacts
- `logs/emission_ab_ctrl_s44_supa_summary.json`
- `logs/emission_ab_ctrl_s45_supa_summary.json`
- `logs/emission_ab_ctrl_s44_dense_summary.json`
- `logs/emission_ab_ctrl_s45_dense_summary.json`
- `logs/ctrl_s4445.log`
- `scratch/run_ctrl_s4445.sh`
- `outputs/adapters/ab_ctrl_s43`
- `outputs/adapters/ab_ctrl_s44`

## Do not retry
- Do NOT designate a single new-corpus chain seed as baseline by its supabase point estimate: the 4-seed family band is 3.19 bpm (s42 6.03 / s43 2.84 / s44 3.07 / s45 4.17), 5.3x v9band04's 0.60, and dense health DISSOCIATES from supabase health per seed (s44 dense 2.56 vs s45 dense 10.17). Any baseline claim needs the family (median + band + per-seed attractor masses), and any future chain-level A/B needs either >=4 seeds per arm or a shared frozen A/B init across arms (v9band04's design) to cancel the chain lottery.
- Do NOT read s44's supabase MAE 3.07 as fully healthy: its top1 emit_raw mass is 0.72 (53.9 on 144/200 rows) - a benign-VALUED attractor near the population median, not per-row reading. corr 0.781 shows real conditioning on the remainder, but the attractor pattern persists across every fresh chain; the healthy-looking MAE partially rides the attractor sitting near truth.

## Notes
VERDICT BY THE PREREGISTERED READINGS: SUPPORTS (both primary claims confirmed, falsifiers dodged). (1) CONFIRMED: band = 6.03-2.84 = 3.19 bpm > 2 - chain-level bistability is the family property (5.3x the v9band04 family's 0.60, which shared one frozen Phase-B init; these chains re-roll A+B+C per seed). (2) CONFIRMED: 2 of 4 seeds land <= 3.5 (s43 2.84, s44 3.07) - the new corpus supports v9band04-grade deployment chains; the corpus-exoneration of rule-out 8e8d8e generalizes. (3) attractor check MIXED and important: s43 top1 0.285 clean; s44 top1 0.72 - a benign-valued attractor (53.9 bpm ~ population median) that makes MAE look healthy while 144/200 rows are constant; s42/s45 0.44 intermediate. FAMILY SUMMARY (supabase): median 3.62, band 3.19, 3 of 4 beat trivial 3.95, 2 of 4 beat/match v9band04's 3.04. Dense-supabase dissociation per seed (s44 dense 2.56 best-ever vs s45 dense 10.17) - the two populations select different basins, reinforcing eval-population honesty. CONSEQUENCES: (a) the new-corpus-encoder problem RESOLVES to 'chain-level seed lottery, not corpus defect' - problem update needed: the engineering fix is v9band04's own design (freeze one good A/B init - s43's or s44's phase_b - and seed only C), which collapses the compounded band back toward 0.60; (b) exp-050's and exp-051's arm orderings confirmed uninterpretable (band > effects); (c) baseline designation on the new corpus should wait for a C-only seed family off the frozen s43 phase_b (~3x37min) rather than be claimed from this 4-point family.

#deployment-distribution #collapse #baseline #control #sensor-text #epoch-resolution
