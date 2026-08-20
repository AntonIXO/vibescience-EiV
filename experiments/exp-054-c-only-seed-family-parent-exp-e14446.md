---
artifacts:
- logs/emission_conly_s44_supa_summary.json
- logs/emission_conly_s45_supa_summary.json
- logs/conly_family.log
- scratch/run_conly_family.sh
- outputs/adapters/ab_ctrl_s43
- outputs/adapters/conly_s44
- outputs/adapters/conly_s45
- outputs/checkpoints/ab_ctrl_s43/phase_b
closed: true
config_note: 'Runner scratch/run_conly_family.sh: C at seeds 44/45 (c_ctrl_s44/s45
  profiles) off the FROZEN outputs/checkpoints/ab_ctrl_s43/phase_b, supabase-200 audits
  batch 1; combined with the existing C-seed-43 (ab_ctrl_s43 adapter) = 3-point C-only
  family on the frozen draw. Parent exp-052 (chain band 3.19). Comparators: v9band04
  family band 0.60, v9band04 same-draw 3.04, trivial 3.95. Budget ~1.6 h GPU.'
cost:
  gpu_hours: 1.6
  wall_hours: 1.8
crash_reason: ''
created_at: '2026-08-20T06:33:12+00:00'
diagnostics_measured:
- after: 0.8
  before: 3.19
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
do_not_retry:
- 'Do NOT retrain Phase A/B per seed when running Phase-C seed studies or A/B comparisons
  on this pipeline: the chain-level band is 3.19 bpm (exp-052) while the C-only band
  off one frozen Phase-B init is 0.80 bpm (this run) — the lottery compounding is
  PROVEN. Freeze one audited A/B init per family; seed only C. This is v9band04''s
  design and it is now measured as the reason its band was 0.60.'
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: c-only-seed-family-parent-exp-052-the-3-19-bpm-1ea9ec
id: exp-054-c-only-seed-family-parent-exp-e14446
interpretation:
  at: '2026-08-20T08:16:59+00:00'
  failure_mode: null
  lesson: 'COMPOUNDING PROVEN, PROBLEM RESOLVED: freezing one good Phase-B init and
    seeding only C collapses the deployment seed band 3.19 → 0.80 bpm (4×, within
    0.2 of v9band04''s 0.60). The ''new corpus breaks Phase C'' scare was A+B+C basin
    lotteries multiplying — not corpus, not captions, not caches, not allowlists.
    Engineering rule now in the record: NEVER retrain A/B per seed in a C-stage study.
    Bonus: the C-only family (frozen ab_ctrl_s43 B + band04 C, seeds 43/44/45) is
    a v10 baseline candidate — median 2.84, band 0.80, all seeds beat trivial, s45''s
    2.74 is the project''s best deployment number. Open cell before designation: dense-guard
    captures for conly_s44/s45.'
  transfers_to:
  - c-only-seed-family-parent-exp-052-the-3-19-bpm-1ea9ec
  - ctrl-chain-seed-family-seeds-44-45-added-to-5b95e9
key_numbers:
  c_only_band: 0.8
  chain_band_parent: 3.19
  family_median: 2.84
  prereg_bar_band: 1.2
  prereg_bar_median: 3.5
  s43_mae: 2.84
  s43_top1: 0.285
  s44_mae: 3.54
  s44_top1: 0.525
  s45_mae: 2.74
  s45_top1: 0.48
  trivial: 3.95
  v9band04_band: 0.6
  v9band04_same_draw: 3.044
observed_effects:
- delta: -2.39
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_supabase_seed_band_mae_bpm: true
provenance: {}
recipe_fingerprint:
  interventions: dataset-allowlist-repaired-all-phases
verdict: supports
---

## Summary
Tests hypothesis [[c-only-seed-family-parent-exp-052-the-3-19-bpm-1ea9ec]].

## Measurements
- [[eiv_supabase_seed_band_mae_bpm]]: before 3.19 → after 0.8  (Δ -2.39, down ✓ predicted down)

**Verdict:** supports

## Interpretation
COMPOUNDING PROVEN, PROBLEM RESOLVED: freezing one good Phase-B init and seeding only C collapses the deployment seed band 3.19 → 0.80 bpm (4×, within 0.2 of v9band04's 0.60). The 'new corpus breaks Phase C' scare was A+B+C basin lotteries multiplying — not corpus, not captions, not caches, not allowlists. Engineering rule now in the record: NEVER retrain A/B per seed in a C-stage study. Bonus: the C-only family (frozen ab_ctrl_s43 B + band04 C, seeds 43/44/45) is a v10 baseline candidate — median 2.84, band 0.80, all seeds beat trivial, s45's 2.74 is the project's best deployment number. Open cell before designation: dense-guard captures for conly_s44/s45.

Transfers to: [[c-only-seed-family-parent-exp-052-the-3-19-bpm-1ea9ec]], [[ctrl-chain-seed-family-seeds-44-45-added-to-5b95e9]]

## Key numbers
- `c_only_band`: 0.8
- `chain_band_parent`: 3.19
- `family_median`: 2.84
- `prereg_bar_band`: 1.2
- `prereg_bar_median`: 3.5
- `s43_mae`: 2.84
- `s43_top1`: 0.285
- `s44_mae`: 3.54
- `s44_top1`: 0.525
- `s45_mae`: 2.74
- `s45_top1`: 0.48
- `trivial`: 3.95
- `v9band04_band`: 0.6
- `v9band04_same_draw`: 3.044

## Artifacts
- `logs/emission_conly_s44_supa_summary.json`
- `logs/emission_conly_s45_supa_summary.json`
- `logs/conly_family.log`
- `scratch/run_conly_family.sh`
- `outputs/adapters/ab_ctrl_s43`
- `outputs/adapters/conly_s44`
- `outputs/adapters/conly_s45`
- `outputs/checkpoints/ab_ctrl_s43/phase_b`

## Do not retry
- Do NOT retrain Phase A/B per seed when running Phase-C seed studies or A/B comparisons on this pipeline: the chain-level band is 3.19 bpm (exp-052) while the C-only band off one frozen Phase-B init is 0.80 bpm (this run) — the lottery compounding is PROVEN. Freeze one audited A/B init per family; seed only C. This is v9band04's design and it is now measured as the reason its band was 0.60.

## Notes
VERDICT: SUPPORTS — both preregistered claims confirmed, falsifiers dodged. (1) C-only band = 3.54-2.74 = 0.80 bpm <= 1.2 bar (vs 3.19 chain-level, 4x collapse; within 0.2 of v9band04's 0.60) — THE COMPOUNDING ACCOUNT IS PROVEN: the new-corpus 'breakage' was A+B+C basin lotteries multiplying, not a corpus or recipe defect. (2) family median 2.84 <= 3.5 — v9band04-grade: ALL THREE seeds beat the trivial 3.95, two beat v9band04's own 3.04 (s45 2.74 the best deployment number this project has recorded). (3) attractor masses: s43 0.285 clean, s45 0.480, s44 0.525 — s44 marginal, quote alongside. BASELINE CANDIDACY: the family (frozen ab_ctrl_s43 phase_b + band04-law C at seeds 43/44/45, incumbent 3-ds C dict) = median 2.84, band 0.80, all seeds positive-skill — a legitimate v10 baseline candidate ON THE NEW CORPUS, pending Anton's designation call and a dense-guard check on s45 (s44's dense was best-ever 2.56 in exp-052 but that was a DIFFERENT B; the C-only family's dense captures were not run for s44/s45 — flag as the one open cell before designation). PROBLEM RESOLUTION: new-corpus-12-547…-problem can move to synthesis — opened on a 4-chain 'all broken' observation, resolved as seed-compounding within 24h, with the engineering rule now in the record.

#deployment-distribution #baseline #control #collapse #sensor-text #epoch-resolution
