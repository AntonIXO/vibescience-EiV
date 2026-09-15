---
artifacts: []
closed: true
config_note: 'v25r48: retry of the crashed exp-v25 (OOM at batch 92). B = phase_b_12ch_v25bigbatch
  with batch_size 48 (96 effective pairs -- anchor parity with A''s 92; VRAM arithmetic
  from the crash: 92 needed +1.45 GiB, 48 clears with ~2x margin) off v20strat A;
  C = band04; same scoring. Runner scratch/run_v25r_48.sh.'
cost: null
crash_reason: 'CUDA OOM in Phase B at batch 48 on the RTX 5070 (12,227 MiB). logs/v25r_driver.log:
  ''PHASE B RC=1'' with 2 CUDA-OOM hits in logs/v25r_phase_b.log; Phase C then had
  no encoder and also RC=1, so scoring aborted with ''outputs/adapters/v25r48_c/manifest.json
  missing''. No measurement was taken at batch 48 — no evidence either way about anchor
  parity above 30.'
created_at: '2026-08-31T01:18:12+00:00'
diagnostics_measured: []
do_not_retry: []
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: anchor-parity-heals-b-the-unified-small-anchor-c0e5ae
id: exp-v25r-b-anchor-parity-48-20260831
interpretation: null
key_numbers: {}
observed_effects: []
parent_experiment_id: exp-v25-b-anchor-parity-20260831
prediction_match: null
provenance: {}
recipe_fingerprint: {}
verdict: crashed
---

## Summary
Tests hypothesis [[anchor-parity-heals-b-the-unified-small-anchor-c0e5ae]].
Branches from [[exp-v25-b-anchor-parity-20260831]].

**Verdict:** crashed

**Crash:** CUDA OOM in Phase B at batch 48 on the RTX 5070 (12,227 MiB). logs/v25r_driver.log: 'PHASE B RC=1' with 2 CUDA-OOM hits in logs/v25r_phase_b.log; Phase C then had no encoder and also RC=1, so scoring aborted with 'outputs/adapters/v25r48_c/manifest.json missing'. No measurement was taken at batch 48 — no evidence either way about anchor parity above 30.

## Notes
Housekeeping close 2026-08-31 11:35 during the v29 GPU run; the arm itself died 04:18. The batch ladder that WAS measured on this hardware: 48 OOM (RC=1), 36 OOM (logs/v25r36_driver.log, RC=1, 3 OOM hits), 30 SUCCEEDED (logs/v25r30_driver.log, RC=0, 736.69 s, and became exp-v25r30 which SUPPORTS the hypothesis). So batch 30 is the empirical VRAM ceiling for Phase B on the 5070, and the anchor-parity hypothesis was answered at 30 rather than at 48. Scaling anchors past 30 needs a batch-independent loss (DHEL/KCL 2405.18045) or grad-cache, not a bigger batch.

#collapse #contrastive #deployment-distribution #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
