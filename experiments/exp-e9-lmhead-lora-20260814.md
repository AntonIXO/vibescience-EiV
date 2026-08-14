---
artifacts: []
closed: true
config_note: 'E9 = v11lmhead_s42: profile lora_proxy_12ch_wl_band04_lmhead (v9band04
  recipe + lm_head in lora_target_modules; yaml-diff verified only lora_target_modules+notes).
  lm_head untied via weight clone before attach; input-embedding bit-identity asserted
  after attach. Same phase_b (v5c), seed 42, 2600 steps, 4-bit. Pipeline scratch/run_e9_lmhead.sh
  -> logs/e9_lmhead.log. Captures: supabase-200 + dense-128, batch 1. Gates g1 sign-flip
  / g2 hr-preserved / g3 vram on hypothesis.'
crash_reason: ''
created_at: '2026-08-14T08:45:28+00:00'
diagnostics_measured:
- after: -3.92
  before: -2.88
  diagnostic_id: eiv_gen_peak_skill_bpm
- after: 2.87
  before: 2.85
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 3.62
  before: 3.08
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1-peak-sign-flip
  passed: false
- evidence: ''
  gate_id: g2-hr-axes-preserved
  passed: true
- evidence: ''
  gate_id: g3-vram-ceiling
  passed: true
- evidence: ''
  gate_id: g4-deployment-and-attractors
  passed: false
git_ref: ''
hypothesis_id: hyp-e9-lmhead-lora-20260814
id: exp-e9-lmhead-lora-20260814
key_numbers: {}
observed_effects:
- delta: -1.04
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: down
- delta: 0.02
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
- delta: 0.54
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
parent_experiment_id: exp-e8-lm-readout-transfer-20260814
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_skill_bpm: false
    eiv_peak_vram_gib: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-e9-lmhead-lora-20260814]].
Branches from [[exp-e8-lm-readout-transfer-20260814]].

## Measurements
- [[eiv_gen_peak_skill_bpm]]: before -2.88 → after -3.92  (Δ -1.04, down ✗ predicted up)
- [[eiv_supabase_hr_raw_mae_bpm]]: before 2.85 → after 2.87  (Δ +0.02, up)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 3.08 → after 3.62  (Δ +0.54, up)

## Gates
- `g1-peak-sign-flip`: **FAIL**
- `g2-hr-axes-preserved`: PASS
- `g3-vram-ceiling`: PASS
- `g4-deployment-and-attractors`: **FAIL**

**Verdict:** refutes

## Notes
REFUTED — g1 BLOCKING FAIL, and the E10-derived hrv intervention read (g4) failed too. lm_head LoRA (untied head, bit-identity verified; healthy training loss 5.26->0.57, 68.1M trainable) did NOT teach the readout to use the peak/hrv supply that E8/E10 proved present: dense peak skill WORSENED -2.88->-3.92 (MAE 20.86 vs median 16.95; new 161.1 bpm attractor at 44/128), supa peak std_ratio halved to 0.488, hrv dense corr 0.592 (vs 0.611 baseline), supa hrv attractor top1 mass 0.827. hr axes preserved (2.87/3.62 within bars) — the arm is safe, just useless. Donor-gap control (required control of eiv_gen_peak_skill_bpm) intentionally NOT run: it is preregistered as a prerequisite for POSITIVE claims only, and the arm failed its blocking gate — no conditioning claim is being made, so the control has nothing to separate. MECHANISM READ: the failure stage is now narrower than 'the readout'. The supply sits in layer-34 hidden states (R2 0.54 peak-beyond-level, hrv 0.97 in prefix), a full-rank-over-digit-rows linear map on lm_head could express more (E26 digit-readout ceiling ~4.2 bpm better-than-median), yet CLM training of exactly that map plus rank-32 adapters on the transformer did not find it. The gradient signal through next-token CE on 2-3 digit tokens per slot per sample is evidently too weak/miscalibrated to steer the head against the template prior in 650 updates. Two arms survive: (a) numeric scalar heads off layer-34 h, trained with a REGRESSION loss on the value — sidesteps the CE-through-digits bottleneck entirely (runbook arm-numeric-scalar-regression-heads-*, priority 15, preconditions now MET); (b) heavier/longer lm_head-only training with digit-focused loss reweighting — weaker prior, more GPU. Choosing (a). Baseline remains v9band04; v11lmhead checkpoint should NOT be promoted (safe but strictly worse on dense hr_raw and peak).

#medgemma #collapse #cross-modal #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
