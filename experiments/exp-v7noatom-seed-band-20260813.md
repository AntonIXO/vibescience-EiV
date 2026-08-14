---
artifacts: []
closed: true
config_note: 'Profiles lora_proxy_12ch_wl_noatom_s43/_s44, byte-diff vs baseline profile
  = {seed, notes} only (verified via yaml load). Phase C only, init outputs/checkpoints/v5c/phase_b.
  Score: frozen 200-row supabase draw (default_rng(0)/223 pool) + dense 128, scratch/emission_diversity_audit.py.
  Seed 42 = existing v7noatom, reused not retrained. Pipeline scratch/run_v7noatom_seed_band.sh,
  log logs/v7noatom_seed_band.log; check internal *_RC= markers.'
crash_reason: ''
created_at: '2026-08-13T08:05:13+00:00'
diagnostics_measured:
- after: 6.29
  before: 11.37
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
- after: 0.0
  before: 0.75
  diagnostic_id: eiv_supabase_floor_emission_rate
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: 'Exact-30 emissions on the frozen 200-row supabase draw: seed 42 0/200,
    seed 43 0/200, seed 44 0/200. Worst floor rate 0.000 < 0.05. Structural claim
    replicates; baseline designation stands.'
  gate_id: g1-all-seeds-floor-rate-below-0.05
  passed: true
- evidence: 'Band = 6.29 bpm (max-min supabase hr_raw MAE). Per-seed: 42=11.54 (top-1
    38.9 mass 0.555, dense 2.89), 43=15.78 (top-1 31.1+31.3 = 108/200, dense 3.65),
    44=9.49 (top-1 31.6 mass 0.310, dense 3.38). Trivial always-median 4.02. Pre-fix
    family band 11.37 -> 6.29 (45% narrower). Preregistered magnitude bar band<6.0
    NARROWLY FAILED (6.29). Row identity verified across all three captures.'
  gate_id: g2-band-reported-with-per-seed-values
  passed: true
- evidence: Seed 44 scored best (9.49 < 11.54) and was NOT promoted; v7noatom seed
    42 remains the named baseline checkpoint. Honored by design.
  gate_id: g3-no-best-seed-promotion
  passed: true
git_ref: 1d4c663
hypothesis_id: the-atom-fix-a71034c-removed-the-d96b6e
id: exp-v7noatom-seed-band-20260813
key_numbers: {}
observed_effects:
- delta: -5.08
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
- delta: -0.75
  diagnostic_id: eiv_supabase_floor_emission_rate
  direction: down
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_supabase_floor_emission_rate: false
    eiv_supabase_seed_band_mae_bpm: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-atom-fix-a71034c-removed-the-d96b6e]].
At commit `1d4c663`.

## Measurements
- [[eiv_supabase_seed_band_mae_bpm]]: before 11.37 → after 6.29  (Δ -5.08, down ✓ predicted down)
- [[eiv_supabase_floor_emission_rate]]: before 0.75 → after 0.0  (Δ -0.75, down ✗ predicted none)

## Gates
- `g1-all-seeds-floor-rate-below-0.05`: PASS — Exact-30 emissions on the frozen 200-row supabase draw: seed 42 0/200, seed 43 0/200, seed 44 0/200. Worst floor rate 0.000 < 0.05. Structural claim replicates; baseline designation stands.
- `g2-band-reported-with-per-seed-values`: PASS — Band = 6.29 bpm (max-min supabase hr_raw MAE). Per-seed: 42=11.54 (top-1 38.9 mass 0.555, dense 2.89), 43=15.78 (top-1 31.1+31.3 = 108/200, dense 3.65), 44=9.49 (top-1 31.6 mass 0.310, dense 3.38). Trivial always-median 4.02. Pre-fix family band 11.37 -> 6.29 (45% narrower). Preregistered magnitude bar band<6.0 NARROWLY FAILED (6.29). Row identity verified across all three captures.
- `g3-no-best-seed-promotion`: PASS — Seed 44 scored best (9.49 < 11.54) and was NOT promoted; v7noatom seed 42 remains the named baseline checkpoint. Honored by design.

**Verdict:** supports

## Notes
POST-FIX BAND MEASURED: 6.29 bpm (seeds 42/43/44 -> 11.54/15.78/9.49 supabase hr_raw MAE; trivial 4.02). All gates PASS. Floor structurally dead on all seeds (0/200 exact-30 everywhere) — the baseline designation stands. Band narrowed 11.37 -> 6.29 (45%) but MISSED the preregistered <6.0 bar: direction held, magnitude claim failed. Mechanism finding (per-seed forensics): the prototype-crystallization account is CONFIRMED and sharpened — every seed still invents low attractors, and seeds 43/44 crystallized theirs at 31.1/31.3/31.6 bpm, i.e. just above the old 30 floor. Emissions <45 bpm occur on 33-60% of rows across seeds while deployment truth is NEVER <45. New CPU probe (scratch/band_supply_demand.py, commit 744bbd7): post-fix target law still places P(hr_raw target < 45) = 0.0729 (almost all from augmentation: E[P(aug<45)]=0.1774, natural only 0.0032), while band-of-interest supply/demand is 0.2017 vs 0.8290 (ratio 0.243). The augmentation's low tail is now the DOMINANT remaining source of sub-45 emissions and seed instability. E1 (rb-deployment-band-mass-20260813) should therefore not just ADD [45,65] mass but REMOVE/reweight the sub-45 augment tail — both edits to _sample_hr_augment_peak's draw law, no new atom. Artifacts: outputs/probes/v7noatom_s43/_s44_emissions_{supabase,n128}.json, scratch/score_v7noatom_seed_band.py, logs/v7noatom_seed_band.log.

#deployment-distribution #baseline #control #optihealth #collapse
