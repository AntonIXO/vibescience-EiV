---
artifacts: []
closed: true
config_note: 'v25bigbatch: B = phase_b_12ch_v25bigbatch (= phase_b_12ch_v11full with
  batch_size 25->92, dict-diff verified single variable; anchor parity with the strat-healed
  A) off v20strat A; C = byte-untouched band04; same scoring. Runner scratch/run_v25_bigbatch.sh.'
cost: null
crash_reason: 'CUDA OOM at Phase B batch_size 92: tried to allocate 1.45 GiB with
  827 MiB free (11.53 GiB total; B holds encoder+fusion+projector with grads on 2560-d
  tokens, effective batch = 2x92=184 pairs). Phase C never ran (adapter manifest missing,
  scorer aborted cleanly). No measurement -- no evidence for or against the anchor-parity
  law.'
created_at: '2026-08-31T01:16:40+00:00'
diagnostics_measured: []
do_not_retry: []
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: anchor-parity-heals-b-the-unified-small-anchor-c0e5ae
id: exp-v25-b-anchor-parity-20260831
interpretation: null
key_numbers: {}
observed_effects: []
parent_experiment_id: exp-v24-b-triplet-off-20260831
prediction_match: null
provenance: {}
recipe_fingerprint: {}
verdict: crashed
---

## Summary
Tests hypothesis [[anchor-parity-heals-b-the-unified-small-anchor-c0e5ae]].
Branches from [[exp-v24-b-triplet-off-20260831]].

**Verdict:** crashed

**Crash:** CUDA OOM at Phase B batch_size 92: tried to allocate 1.45 GiB with 827 MiB free (11.53 GiB total; B holds encoder+fusion+projector with grads on 2560-d tokens, effective batch = 2x92=184 pairs). Phase C never ran (adapter manifest missing, scorer aborted cleanly). No measurement -- no evidence for or against the anchor-parity law.

## Notes
Retry at the largest fitting batch: 48 (96 effective pairs, ~anchor parity with A's 92) or fall back 40. VRAM arithmetic from this crash: 92 needed ~1.45 GiB more; 48 should clear by ~2x margin.

#collapse #contrastive #deployment-distribution #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
