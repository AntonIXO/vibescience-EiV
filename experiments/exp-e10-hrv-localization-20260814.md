---
artifacts: []
closed: true
config_note: 'E10 CPU-only (CUDA_VISIBLE_DEVICES= while E9 trains on GPU): frozen
  v9band04_s42 encoder+fusion+projector forward on eval_v3_1min rows with >=60 present
  hrv epochs, n<=2500. Target raw daily hrv mean (ms). Arms pooled / slot mean+max,
  RidgeCV GroupKFold(5) by subject; thermometer control R2(hrv|true hr mean); shuffle
  null. systemd scope MemoryMax=10G. Gate g1 two-sided: >=0.35 readout-limited / <0.15
  encoder-limited.'
crash_reason: ''
created_at: '2026-08-14T09:00:42+00:00'
diagnostics_measured:
- after: 0.9692
  before: 0.6
  diagnostic_id: eiv_nonhr_channel_grouped_r2
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: PASS — readout-limited zone, decisively. Best arm slot mean+max OOF R2
    0.9692 >= 0.35 (pooled 0.9007). MAE 2.77 ms vs trivial 17.64 ms. The prefix carries
    daily hrv near-perfectly; the emission path (dense corr 0.60, supabase attractor
    69.1 ms) is where it dies.
  gate_id: g1-hrv-in-prefix
  passed: true
- evidence: 'Measured: increment beyond the hr thermometer +0.7086 (thermometer alone
    0.2605). The prefix hrv signal is NOT hr-mediated. Shuffle null -0.0196 clean.'
  gate_id: g2-beyond-hr-thermometer
  passed: true
git_ref: ''
hypothesis_id: hyp-e10-hrv-localization-20260814
id: exp-e10-hrv-localization-20260814
key_numbers: {}
observed_effects:
- delta: 0.3692
  diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: up
parent_experiment_id: exp-e7-projector-bottleneck-20260814
prediction_match:
  overall: true
  per_diagnostic:
    eiv_nonhr_channel_grouped_r2: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-e10-hrv-localization-20260814]].
Branches from [[exp-e7-projector-bottleneck-20260814]].

## Measurements
- [[eiv_nonhr_channel_grouped_r2]]: before 0.6 → after 0.9692  (Δ +0.3692, up ✓ predicted up)

## Gates
- `g1-hrv-in-prefix`: PASS — PASS — readout-limited zone, decisively. Best arm slot mean+max OOF R2 0.9692 >= 0.35 (pooled 0.9007). MAE 2.77 ms vs trivial 17.64 ms. The prefix carries daily hrv near-perfectly; the emission path (dense corr 0.60, supabase attractor 69.1 ms) is where it dies.
- `g2-beyond-hr-thermometer`: PASS — Measured: increment beyond the hr thermometer +0.7086 (thermometer alone 0.2605). The prefix hrv signal is NOT hr-mediated. Shuffle null -0.0196 clean.

**Verdict:** supports

## Notes
CONFIRMED (both gates pass, prediction direction and zone correct; magnitude even above my committed 0.40-0.65 range — 0.9692 with the slot mean+max readout). hrv joins hr_peak as READOUT-LIMITED: the v9band04 prefix carries daily hrv at R2 0.9692 / MAE 2.77 ms (trivial 17.64), with +0.7086 beyond the hr thermometer and a clean shuffle null — while the emission path manages dense corr 0.60 and a 69.1 ms supabase attractor. Combined with E8 (peak increment +0.5032 at the emitting position) the picture is now uniform across every failing numeric slot: THE ENCODER+PROJECTOR STACK IS ESSENTIALLY SOLVED (hr_mean ~0.97, hrv 0.97, peak-beyond-level 0.53 all linearly readable from the prefix); the frozen lm_head/decode path is the single common failure stage. This retroactively explains why E3's target-law reshaping failed: it tried to fix a readout problem with a data law. NEXT READ: E9's g4 hrv capture is now a direct intervention test — if the lm_head LoRA lifts hrv dense corr materially above 0.60 alongside any peak movement, one arm fixes the common stage; if E9 fails on both, the numeric-head architecture arm (scalar regression head off layer-34 hidden state — which this probe family shows would inherit R2 ~0.5-0.97 supply) becomes the only surviving lever and has strong priors. Superseded framing: the dissociation criterion's 'dense corr HIGH vs LOW' fork used EMISSIONS as the localization instrument; E10 shows emission corr can sit at 0.60 while the prefix holds 0.97 — emissions UNDERCOUNT prefix content whenever the readout is the defect. Prefix probes are the correct instrument for the supply half of that criterion going forward.

#cross-modal #sensor-text #wearables #collapse #deployment-distribution #optihealth #baseline
