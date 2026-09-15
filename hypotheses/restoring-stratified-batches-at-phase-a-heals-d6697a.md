---
ambition_class: mechanism
created_at: '2026-08-30T18:47:50+00:00'
former_ids: []
gap_closure_estimate: 1.0
gates:
- blocking: true
  description: 'g0 BLOCKING PRECONDITION (geometry, auto-enforced by the runner):
    v20strat phase_a eff_rank >= 7.0. The chain script aborts before Phase B if this
    fails, so a B/C spend cannot occur on an unrecovered encoder.'
  diagnostic_id: eiv_effective_rank
  id: g0-geometry-gate-passed
  mde: 1.0
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr >= 0.60 (healthy class 0.74-0.81;
    every post-rebuild chain 0.13-0.36).'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-chain-corr-healthy
  mde: 0.1
- blocking: true
  description: 'g2 BLOCKING: supabase hr_raw MAE beats the trivial 3.954 (skill >
    0). Healthy family 2.69-2.84.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-beats-trivial
  mde: 0.8
- blocking: false
  description: 'g3 advisory: dense-128 hr_raw MAE < 8.0 (new-eval trivial 6.986).'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-advisory
  mde: 1.0
id: restoring-stratified-batches-at-phase-a-heals-d6697a
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'PRIMARY deployment readout. If g0 passes (rank recovered), the
    chain should land in the healthy class: MAE 2.7-3.5, corr 0.7+, dense < 7. Every
    piece of the causal path is now individually measured: anchors drift (184k vs
    32k, shipped configs), uniformity loss delta (-6.83 vs -5.41), rank collapse (8.35
    vs 4.47), rank->C-health monotonicity (12.21/9.70/8.71 -> 0.78/0.31/0.13), carrier
    travels with encoder (v17 +0.352, v18 collapse on old data). Prior ~70% conditional
    on g0. Calibration 0.408; my supabase-MAE record is 3/13 -- the gates decide,
    and g0 spends nothing if the premise is wrong.'
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags:
- matched-baseline
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- collapse
- contrastive
- deployment-distribution
- sensor-text
- baseline
- epoch-resolution
---

## Statement
Restoring stratified_batches at Phase A heals the ENTIRE chain on the fixed [14041] bundle: A(v20strat fix) -> B(unchanged profile) -> C(byte-untouched band04) produces a deployment-healthy adapter -- supabase hr_raw corr >= 0.60 AND skill > 0 -- closing the constant-emitter problem with a named root cause (the profile-rewrite whitelist drift that dropped the key and cut InfoNCE anchors 5.75x).

## Rationale
The evidence chain is complete except the last link: (1) v17/v18 double dissociation -- collapse travels with the encoder checkpoint both directions; (2) 5-way geometry probe -- damage enters at Phase A (4.47 vs 8.35), B configs are byte-equivalent; (3) v19 -- pool composition refuted (18% of gap); (4) direct diff of shipped phase_configs -- the healthy lineage ran stratified_batches (92 anchors/batch, 184k anchors), every v11-era profile dropped the key (16/batch, 32k), and the shipped uniformity losses corroborate (-6.83 vs -5.41). Anton's call: 'дело в whitelist' -- the dataset_filter rewrite for the new corpus is exactly WHERE the key was lost (the v11 profiles were written fresh around the new roster and stratified_batches did not survive the rewrite). v20strat tests the fix at A; this chain carries it through B to the deployment number that defines the problem.

## Plan
FULL CHAIN on the fixed [14041] ddb7498 bundle, single seed, runner scratch/run_v21_chain.sh (queued behind v20strat, self-gating):
  A = v20strat (phase_a_12ch_v11full + stratified_batches restored -- THE FIX)
  gate = scratch/_geom_gate_v20.py (eff_rank >= 7.0 or abort with exit 42)
  B = phase_b_12ch_v11full off the v20 A (B needs no fix: shipped healthy-vs-carrier B configs are identical -- batch_size 25, samples 25000, no stratified key ever)
  C = byte-untouched lora_proxy_12ch_wl_band04 off the v21 B
  scoring = same instrument (supabase n=200 batch1 rng0 + dense-128, eval sha 03d62723)
WHY A CHAIN IS JUSTIFIED NOW despite the exp-v11 do-not-retry against unfocused chains: this is not a fishing chain -- it carries a NAMED single-key root-cause fix at the exact stage the 2x2 (v17/v18) and the geometry ladder (5-way probe, v19, v20) localised, with a preregistered geometry gate that kills the spend before B if the fix did not restore the encoder cloud. PASS closes the problem with mechanism: dropped stratified_batches -> 5.75x fewer InfoNCE anchors -> weak uniformity -> rank collapse -> indistinguishable prefixes -> constant-emitting C. FAIL after a passed g0 splits geometry from trainability and reopens with the B stage under suspicion.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Gate `g0-geometry-gate-passed` (blocking): g0 BLOCKING PRECONDITION (geometry, auto-enforced by the runner): v20strat phase_a eff_rank >= 7.0. The chain script aborts before Phase B if this fails, so a B/C spend cannot occur on an unrecovered encoder.
Gate `g1-chain-corr-healthy` (blocking): g1 BLOCKING: supabase hr_raw corr >= 0.60 (healthy class 0.74-0.81; every post-rebuild chain 0.13-0.36).
Gate `g2-beats-trivial` (blocking): g2 BLOCKING: supabase hr_raw MAE beats the trivial 3.954 (skill > 0). Healthy family 2.69-2.84.
Gate `g3-dense-advisory` (advisory): g3 advisory: dense-128 hr_raw MAE < 8.0 (new-eval trivial 6.986).

#collapse #contrastive #deployment-distribution #sensor-text #baseline #epoch-resolution #matched-baseline #optihealth-eiv
