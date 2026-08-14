---
artifacts: []
closed: true
config_note: 'Arm v10hrv = profile lora_proxy_12ch_wl_band04_hrv, single variable
  vs baseline lora_proxy_12ch_wl_band04 (yaml-diff verified: hrv_augment None->True,
  hrv_augment_prob None->0.4, hrv_augment_band_prob None->0.5). Init v5c/phase_b,
  Phase C 2600 steps, seed 42, single seed per codified E1b norm. Supply check (scratch/e3_supply_check.py,
  n=2018 targets): P(band[55,90]) 0.406->0.509, P(<1ms)=0, top atom 0.0055@72.4 =
  natural corpus density (unaugmented top 0.0045); hr law unchanged (P(45-65)=0.4497,
  P(<45)=0). PREREGISTERED (frozen 200-row supabase draw + dense-128): P1 supabase
  hrv_raw MAE < trivial 8.31 (baseline skill -0.2..-0.5); P2 collapse breaks: hrv
  top1 mass < 0.5 AND std_ratio > 0.3 (baseline 0.63-0.85 / 0.02-0.29); P3 hr preserved:
  supa hr MAE <= 3.45, dense hr MAE <= 4.73 (worst v9band04 seed values); P4 dense
  hrv skill >= +1.5 (baseline +2.8..+3.5).'
crash_reason: ''
created_at: '2026-08-13T21:05:23+00:00'
diagnostics_measured:
- after: 3.67
  before: 2.85
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 5.44
  before: 3.08
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
- after: 0.0
  before: 0.0
  diagnostic_id: eiv_supabase_floor_emission_rate
do_not_retry: []
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: hrv-raw-fails-on-deployment-for-the-624895
id: exp-e3-hrv-band-20260813
key_numbers: {}
observed_effects:
- delta: 0.82
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
- delta: 2.36
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
- delta: 0.0
  diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
parent_experiment_id: exp-e1b-band04-20260813
prediction_match:
  overall: false
  per_diagnostic:
    eiv_supabase_seed_band_mae_bpm: false
provenance: {}
verdict: inconclusive
---

## Summary
Tests hypothesis [[hrv-raw-fails-on-deployment-for-the-624895]].
Branches from [[exp-e1b-band04-20260813]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 2.85 → after 3.67  (Δ +0.82, up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 3.08 → after 5.44  (Δ +2.36, up)
- [[eiv_supabase_floor_emission_rate]]: before 0.0 → after 0.0  (Δ +0, flat)

**Verdict:** inconclusive

## Notes
VERDICT: REFUTED, all 4 preregistered gates FAIL (gates lived on runbook rb-e3-hrv-band-20260813; hypothesis carried none — recording them here). Scored logs/e3_verdict.json (scratch/score_e3.py), captures outputs/probes/v10hrv_s42_emissions_{supabase,n128}.json, all RC=0, batch_size=1.

g1 FAIL: supa hrv MAE 12.64 vs trivial 8.31 (skill -4.33; baseline -0.31 — WORSE than doing nothing).
g2 FAIL: top1 mass did drop 0.853->0.284 but std_ratio 0.133 (<0.3), corr 0.127: one prototype became several quantized prototypes near 84 ms; still not reading the row.
g3 BLOCKING FAIL: supa hr 3.67 (>3.45), dense hr 5.44 (>4.73) — the hrv law also damaged the hr axes. Floor 0.000 preserved.
g4 FAIL: dense hrv skill -11.93 (was +3.52); model emits p50 84.0 against dense truth p50 45.3.

MECHANISM (CPU post-mortem, scratch/e3_postmortem.py): NOT a supply-shape bug — realized law-ON target marginal matches design (p50 63.2 vs designed 63.8; P(>=80) 0.246 vs 0.242; 1ms-floor clamp fires on 11% of draws, +12.3 ms median). The law worked as designed and STILL failed. Interpretation: unlike hr (augment shifts a signal the encoder reads at corr~0.9), the encoder's hrv readout is weak (dense corr 0.60 at best); random mean-shifts of channel 1 DECOUPLE the target from correlates the model was using, so the LM learns 'hrv_raw is a random number near the marginal mode (~84 ms)' — the augment destroyed the training signal instead of rebalancing it. The hr/hrv asymmetry is the finding: BAND-LAW TRANSFER REQUIRES A STRONGLY-READ CHANNEL. hrv needs an encoder-side lever (same conclusion as the hr_peak dissociation rule-out filed today), not a target-law lever.

Deployment impact: NONE — v10hrv rejected, baseline recipe lora_proxy_12ch_wl_band04 unchanged. Predictions P1-P4 all failed (prediction_match=false).

#deployment-distribution #optihealth #collapse #baseline
