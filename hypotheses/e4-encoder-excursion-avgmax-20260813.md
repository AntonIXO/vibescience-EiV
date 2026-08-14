---
created_at: '2026-08-13T22:09:18+00:00'
former_ids: []
gates:
- blocking: true
  description: 'Stage-0 CPU gate BEFORE any GPU: ridge probe eiv_peak_excursion_increment_r2
    on the new avg+max pooled features >= 0.25 (reference: avg-only +0.0030, avg+max
    concat feature-level 0.2968)'
  diagnostic_id: null
  id: e4-g0-cpu-excursion-increment-ge-0-25
  mde: null
- blocking: false
  description: dense-128 peak corr(emit, truth) > 0.45 (above the 0.348 best observed
    under mean-pooling)
  diagnostic_id: null
  id: e4-g1-dense-peak-corr-above-mean-pool-ceiling
  mde: null
- blocking: true
  description: 'hr axes preserved: supabase hr MAE <= 3.45 AND dense hr MAE <= 4.73
    (worst v9band04 seed values), floor rate 0'
  diagnostic_id: null
  id: e4-g2-BLOCKING-hr-axes-within-v9band04-band
  mde: null
- blocking: false
  description: dense hr_peak skill vs always-median improves from the -2.9..-3.2 band
    to > -1.0
  diagnostic_id: null
  id: e4-g3-dense-peak-skill-above-minus-1
  mde: null
id: e4-encoder-excursion-avgmax-20260813
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: 'CPU stage-0 gate: >=0.25 on new pooled features (avg-only reference
    +0.0030)'
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: none
  magnitude_note: must stay <= 4.73 (worst v9band04 seed)
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: none
  magnitude_note: must stay <= 3.45 (worst v9band04 seed); floor rate 0
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
problem_tags: []
status: proposed
supersedes: null
topic_tags:
- cross-modal
- collapse
- deployment-distribution
- optihealth
- baseline
---

## Statement
hr_peak and hrv_raw fail because the temporal encoder's mean-pooling destroys dispersion/extreme-value information; adding a max-pool (excursion) branch to the patch pooling (avg+max concat) raises the encoder-side ceiling, the necessary precondition for any emission-side gain on these slots

## Rationale
Two independent dissociations converge on the encoder: (1) hr_peak dense corr 0.17-0.35 across all v9band04 seeds while hr_raw reads at ~0.9 (rule-out 2026-08-13: target-law lever dead for peak); (2) E3 refutation: the hrv band law executed exactly as designed (realized marginal matches design; 1ms clamp fires 11%) yet destroyed both hrv axes (dense skill +3.52 -> -11.93) because random mean-shifts decouple a weakly-read channel (dense corr 0.60) from the correlates the model uses. Transferable law: target reshaping only works on strongly-read channels. Both failures are representation-side, matching the caption ladder (dispersion 0.124->0.708) and the excursion probe (avg +0.0030 vs avg+max 0.2968).

## Plan
Stage 0 (CPU): implement avg+max concat pooling behind a config flag in TemporalPatching; run the eiv_peak_excursion_increment_r2 ridge probe on pooled features to confirm the information survives the actual architecture; blocking gate e4-g0. Stage 1 (GPU): Phase A -> B -> C with the new encoder (v5c init unusable: dimension change), seed 42 single seed per codified norm; captures supabase-200 + dense-128 batch_size=1; score with scratch/score_e1b.py, scratch/score_slots_deployment.py, scratch/e4_peak_read_probe.py.

## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (none).
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (none).
Gate `e4-g0-cpu-excursion-increment-ge-0-25` (blocking): Stage-0 CPU gate BEFORE any GPU: ridge probe eiv_peak_excursion_increment_r2 on the new avg+max pooled features >= 0.25 (reference: avg-only +0.0030, avg+max concat feature-level 0.2968)
Gate `e4-g1-dense-peak-corr-above-mean-pool-ceiling` (advisory): dense-128 peak corr(emit, truth) > 0.45 (above the 0.348 best observed under mean-pooling)
Gate `e4-g2-BLOCKING-hr-axes-within-v9band04-band` (blocking): hr axes preserved: supabase hr MAE <= 3.45 AND dense hr MAE <= 4.73 (worst v9band04 seed values), floor rate 0
Gate `e4-g3-dense-peak-skill-above-minus-1` (advisory): dense hr_peak skill vs always-median improves from the -2.9..-3.2 band to > -1.0

#cross-modal #collapse #deployment-distribution #optihealth #baseline
