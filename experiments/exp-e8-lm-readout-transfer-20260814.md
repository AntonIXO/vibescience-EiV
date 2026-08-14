---
artifacts: []
closed: true
config_note: 'E8 = diag_e24_lm_hidden_peak.py unchanged, --adapter outputs/checkpoints/v9band04_s42/phase_c,
  -n 900, --out outputs/probes/e8_lm_hidden_peak_v9band04.json. 4-bit load, layers
  {0,8,17,25,34}, strict-clean train-corpus pool. systemd-run scope MemoryMax=14G.
  Gates: g0 hr_raw R2>=0.60, g1 final-layer increment >+0.25, g2 |shuffle|<0.05.'
crash_reason: ''
created_at: '2026-08-14T08:31:28+00:00'
diagnostics_measured:
- after: 0.5032
  before: 0.7476
  diagnostic_id: eiv_lm_hidden_peak_r2
external_run: ''
gate_results:
- evidence: PASS. hr_raw-position final-layer R2 = 0.9981 >= 0.60. Apparatus sound;
    calibrates what 'enough to drive generation' looks like at this position (hr_raw
    DOES condition at generation on this recipe).
  gate_id: g0-hr-raw-positive-control
  passed: true
- evidence: 'PASS. Final-layer increment +0.5032 > +0.25 (R2 0.5440, level control
    0.0408). Layer profile flat-high from L8 (0.4909) to L34 (0.5032): nothing decays
    in-flight. Readout-failure verdict TRANSFERS to v9band04_s42.'
  gate_id: g1-final-layer-increment
  passed: true
- evidence: PASS. Shuffled-target R2 = -0.0041, |.| < 0.05.
  gate_id: g2-shuffle-null
  passed: true
git_ref: ''
hypothesis_id: hyp-e8-lm-readout-transfer-20260814
id: exp-e8-lm-readout-transfer-20260814
observed_effects:
- delta: -0.2444
  diagnostic_id: eiv_lm_hidden_peak_r2
  direction: down
parent_experiment_id: exp-e7-projector-bottleneck-20260814
prediction_match:
  overall: false
  per_diagnostic:
    eiv_lm_hidden_peak_r2: false
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-e8-lm-readout-transfer-20260814]].
Branches from [[exp-e7-projector-bottleneck-20260814]].

## Measurements
- [[eiv_lm_hidden_peak_r2]]: before 0.7476 → after 0.5032  (Δ -0.2444, down ✗ predicted none)

## Gates
- `g0-hr-raw-positive-control`: PASS — PASS. hr_raw-position final-layer R2 = 0.9981 >= 0.60. Apparatus sound; calibrates what 'enough to drive generation' looks like at this position (hr_raw DOES condition at generation on this recipe).
- `g1-final-layer-increment`: PASS — PASS. Final-layer increment +0.5032 > +0.25 (R2 0.5440, level control 0.0408). Layer profile flat-high from L8 (0.4909) to L34 (0.5032): nothing decays in-flight. Readout-failure verdict TRANSFERS to v9band04_s42.
- `g2-shuffle-null`: PASS — PASS. Shuffled-target R2 = -0.0041, |.| < 0.05.

**Verdict:** refutes

## Notes
ALL THREE PREREGISTERED GATES PASS: the readout-failure localization TRANSFERS to the current baseline. Final-layer increment +0.5032 (R2 0.5440, level control 0.0408), hr_raw positive control 0.9981, shuffle null -0.0041, n=900 strict-clean, 0 skipped, ~3.5 min GPU. CAVEAT ON THE RECORDED DELTA: 'before' was set to E24's e23v2 value (+0.7476) for cross-adapter reference, so the observed_effects direction reads 'down' — that 0.24 drop is a real cross-adapter difference (v9band04 carries less peak surplus than e23v2 did) but the preregistered question was whether the increment stays above +0.25, and it does with 2x margin. THE CHAIN IS NOW CLOSED ON ONE RECIPE, all same-family measurements: raw supply 0.4847 -> encoder+fusion 0.5004 -> projector 0.5263 -> LM final layer at emission position 0.5032 -> generated output corr 0.17-0.35, negative skill. ~100% of the encoder's peak surplus arrives at the token that emits the first digit; the frozen lm_head/decode path is the sole failure stage. DECISION: rb-e4-lora-lm-head is now precisely targeted and is the next GPU arm — with the E26 scoping honored (expect ~4 bpm coarse magnitude correction, NOT accurate peaks; teacher-forcing trap; untie lm_head from input embeddings BEFORE attaching LoRA, assert sensor-sentinel embeddings bit-identical). The alternative (numeric scalar head) remains the architecture escape if E4's ~4 bpm ceiling is confirmed. Layer profile note: increment is already 0.49 at L8 and flat through L34 — the signal neither builds nor decays in-flight, consistent with the prefix being copied forward unread by the decode path.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
