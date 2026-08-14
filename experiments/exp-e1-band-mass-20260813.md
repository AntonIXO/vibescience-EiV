---
artifacts: []
closed: true
config_note: 'Profiles lora_proxy_12ch_wl_band{,_s43,_s44} (v9band, seeds 42/43/44);
  yaml-diff vs lora_proxy_12ch_wl_noatom = {hr_augment_prob 0.4->0.8, hr_augment_band_prob
  0.5, hr_augment_min_mean_bpm 45.0, seed, notes} only (verified via yaml load). Init
  outputs/checkpoints/v5c/phase_b, Phase C 2600 steps. Scoring: frozen 200-row supabase
  draw (default_rng(0)/223 pool) + dense 128, scratch/emission_diversity_audit.py.
  Pipeline scratch/run_e1_band.sh -> logs/e1_band.log, per-stage *_RC= markers (wrapper
  exit codes lie). CPU supply gate GO: P(band) 0.2039->0.5510, P(<45) 0.0719->0.0007
  (scratch/e1_supply_gate.py).'
crash_reason: ''
created_at: '2026-08-13T10:50:52+00:00'
diagnostics_measured:
- after: 3.89
  before: 11.54
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 1.31
  before: 6.29
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
- after: 0.0
  before: 0.0
  diagnostic_id: eiv_supabase_floor_emission_rate
external_run: ''
gate_results:
- evidence: 'Per-seed supabase MAE 42/43/44 = 4.16/2.86/3.89, median 3.89 < 9.49.
    Row identity 200/200 vs the v7noatom capture on all three seeds. Paired sign test
    seed-42 vs v7noatom: band wins 156/200, p=3.4e-16. Two seeds (2.86, 3.89) beat
    the trivial always-median 4.02 — first arms in project history to do so; median
    3.89 also beats it.'
  gate_id: g1-median-mae-beats-best-baseline-seed
  passed: true
- evidence: Exact-30 emissions 0/200 on all three seeds. Floor rate 0.000 everywhere;
    atom not resurrected.
  gate_id: g2-floor-below-0.05-all-seeds
  passed: true
- evidence: 'FAIL. Dense-128 MAE 4.49/5.65/4.46 vs bar <=3.89 (2.89+1). Regression
    concentrated in upper terciles: v7noatom tercile MAE [4.15,1.94,2.58] -> seed
    43 [3.60,5.15,8.27]; on truth>80 rows signed bias +1.15/+9.57/+3.98 bpm (band
    arm pulls high days UP, oddly — overestimation, not band-ward shrinkage; n=26).
    Cost of the reshaped target law is real dense-population accuracy loss, worst
    on seed 43 (which is also the best supabase seed — the trade-off is seed-anticorrelated).'
  gate_id: g3-dense128-within-1bpm
  passed: false
- evidence: 'P(emit<45) = 0.000/0.000/0.000 on the frozen supabase draw (baseline
    family: 0.575/0.595/0.335). The fictional sub-45 region is gone from behavior,
    not just from the target law.'
  gate_id: g4-sub45-emission-below-0.10-all-seeds
  passed: true
- evidence: 'Band = 1.31 bpm (4.16/2.86/3.89), down from 6.29 — 4.8x narrower, and
    below the shared-root-cause bar. Crystallization moved INSIDE the band and weakened:
    top-1 mass 0.435@52.6 / 0.195@58.6 / 0.295@54.6 (baseline: 0.555@38.9 / 0.540@31.1+31.3
    / 0.310@31.6), distinct values 17/22/24. Seed lottery and band starvation are
    CONFIRMED as sharing the target-law root.'
  gate_id: g5-band-reported
  passed: true
git_ref: 14e2ee4
hypothesis_id: e1-band-mass-starvation-drives-supabase-mae-and-seed-band
id: exp-e1-band-mass-20260813
observed_effects:
- delta: -7.65
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
- delta: -4.98
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
- delta: 0.0
  diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_supabase_floor_emission_rate: true
    eiv_supabase_hr_raw_mae_bpm: true
    eiv_supabase_seed_band_mae_bpm: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[e1-band-mass-starvation-drives-supabase-mae-and-seed-band]].
At commit `14e2ee4`.

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 11.54 → after 3.89  (Δ -7.65, down ✓ predicted down)
- [[eiv_supabase_seed_band_mae_bpm]]: before 6.29 → after 1.31  (Δ -4.98, down ✓ predicted down)
- [[eiv_supabase_floor_emission_rate]]: before 0.0 → after 0.0  (Δ +0, flat ✓ predicted none)

## Gates
- `g1-median-mae-beats-best-baseline-seed`: PASS — Per-seed supabase MAE 42/43/44 = 4.16/2.86/3.89, median 3.89 < 9.49. Row identity 200/200 vs the v7noatom capture on all three seeds. Paired sign test seed-42 vs v7noatom: band wins 156/200, p=3.4e-16. Two seeds (2.86, 3.89) beat the trivial always-median 4.02 — first arms in project history to do so; median 3.89 also beats it.
- `g2-floor-below-0.05-all-seeds`: PASS — Exact-30 emissions 0/200 on all three seeds. Floor rate 0.000 everywhere; atom not resurrected.
- `g3-dense128-within-1bpm`: **FAIL** — FAIL. Dense-128 MAE 4.49/5.65/4.46 vs bar <=3.89 (2.89+1). Regression concentrated in upper terciles: v7noatom tercile MAE [4.15,1.94,2.58] -> seed 43 [3.60,5.15,8.27]; on truth>80 rows signed bias +1.15/+9.57/+3.98 bpm (band arm pulls high days UP, oddly — overestimation, not band-ward shrinkage; n=26). Cost of the reshaped target law is real dense-population accuracy loss, worst on seed 43 (which is also the best supabase seed — the trade-off is seed-anticorrelated).
- `g4-sub45-emission-below-0.10-all-seeds`: PASS — P(emit<45) = 0.000/0.000/0.000 on the frozen supabase draw (baseline family: 0.575/0.595/0.335). The fictional sub-45 region is gone from behavior, not just from the target law.
- `g5-band-reported`: PASS — Band = 1.31 bpm (4.16/2.86/3.89), down from 6.29 — 4.8x narrower, and below the shared-root-cause bar. Crystallization moved INSIDE the band and weakened: top-1 mass 0.435@52.6 / 0.195@58.6 / 0.295@54.6 (baseline: 0.555@38.9 / 0.540@31.1+31.3 / 0.310@31.6), distinct values 17/22/24. Seed lottery and band starvation are CONFIRMED as sharing the target-law root.

**Verdict:** supports

## Notes
HEADLINE: supabase MAE 4.16/2.86/3.89 (median 3.89), seed band 6.29->1.31, floor 0.000, P(emit<45) 0.000 all seeds. Seeds 43/44 BEAT the trivial always-median baseline (4.02) — the first arms in project history with positive deployment skill; paired seed-42 vs v7noatom 156/200 wins, p=3.4e-16. Both blocking gates PASS; predictions P1 (median<8) and P2 (band<6.29) held with large margin. The shared-root-cause claim is CONFIRMED: reshaping the target law simultaneously fixed accuracy AND collapsed the seed lottery (crystallization weakened and relocated inside the band: top-1 mass 0.555->0.195-0.435, now at 52.6/58.6/54.6 — plausible values).

HONEST LIMIT — g3 (dense-128 within 1 bpm of 2.89) FAILED: 4.49/5.65/4.46. The regression sits in the UPPER terciles (seed 43 tercile MAE [3.60,5.15,8.27] vs v7noatom [4.15,1.94,2.58]) with POSITIVE bias on truth>80 rows (+1.15/+9.57/+3.98 bpm, n=26) — the arm OVERestimates high days rather than dragging them band-ward, which is not the naive band-shrinkage failure mode and is unexplained. Confounds declared up front: hr_augment_prob 0.8 vs 0.4 rides along with the band mixture; natural-target share halved (0.6->0.2 of samples), which plausibly cost real-data high-range calibration.

Verdict must reflect the preregistered contract: blocking gates passed and primary predictions held, but a preregistered non-blocking gate failed — the intervention is a deployment win with a measured dense cost, not a free lunch. NEXT (queued, not run): E1b prob-sweep arm (band_prob 0.5 + min_mean 45 at hr_augment_prob 0.4) to isolate the prob-0.8 confound and likely recover dense accuracy; decision rule: if dense recovers within 1 bpm while supabase median stays <4.5, promote E1b recipe as baseline candidate; else accept the trade-off explicitly. Artifacts: outputs/probes/v9band_s4*_emissions_*.json, scratch/{score_e1_band,e1_forensics,e1_supply_gate}.py, logs/e1_band.log, checkpoints outputs/checkpoints/v9band_s4{2,3,4}/phase_c.

#deployment-distribution #optihealth #collapse #baseline
