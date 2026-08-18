---
artifacts: []
closed: true
config_note: 'Frozen validation pilot: paired seeds 17,23,29; arms dcl and multi_positive_dcl;
  500 steps, bf16, batch102, RTX 5070; population stats disabled; exact caption predicate
  spec SHA256 46d57eecd32d0db7bdbe96e0434072204d90167b0a555a87511644179459cede; protocol
  SHA256 4329c98f72312aaf2b8e6630861c8f2acbe05e41329d45bd4fa75bf9f371b4db; corrected
  mask SHA256 31f0620e135192349a0add2756d624d0b0e1fa5a519bc776cb8b9e288964e289. Smokes
  are engineering-only and excluded. Test is locked.'
cost: null
crash_reason: nonfinite loss — both smoke arms (dcl, multi_positive_dcl) returned
  nonfinite loss; no scientific run was ever produced for this pilot
created_at: '2026-07-28T08:39:49+00:00'
diagnostics_measured: []
do_not_retry: []
external_run: /root/optiHealth-EiV/outputs/experiments/multi_positive_dcl_20260728
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5+dirty-manifest-4329c98f7231
hypothesis_id: hyp-eiv-multipositive-dcl-20260728
id: exp-eiv-multipositive-dcl-pilot-20260728
interpretation:
  at: '2026-08-18T21:08:45+00:00'
  failure_mode: execution_bug
  lesson: 'No evidence either way about exact-caption multi-positive DCL from this
    record: BOTH smoke arms (dcl and multi_positive_dcl) returned nonfinite loss,
    so zero measurements exist and no scientific run was ever produced. Per run-cross-modal-loss-studies
    section 6 a smoke run is engineering evidence only, which is why this is logged
    as crashed rather than inconclusive — the hypothesis stays fully retryable and
    this record must not be cited as a negative result. The integrity-fixed re-run
    exp-eiv-multipositive-dcl-pilot-v2-20260728 (same protocol SHA 4329c98f7231, same
    corrected mask 31f0620e...) is the record that carries actual numbers; read that
    one instead.'
  transfers_to:
  - hyp-eiv-multipositive-spectral-entropy-20260728
key_numbers: {}
observed_effects: []
parent_experiment_id: null
prediction_match: null
provenance:
  arm: multi_positive_dcl
  config_sha256: 677634a64fa8dafff4da2bdf777f231c70039f1156ded3069fc196964e17d74d
  corrected_mask_sha256: 31f0620e135192349a0add2756d624d0b0e1fa5a519bc776cb8b9e288964e289
  environment_sha256: a28c900f2a09ce09f8116b37e4d3689541c0a18084e64342c8a511c46d9ee37b
  protocol_sha256: 4329c98f72312aaf2b8e6630861c8f2acbe05e41329d45bd4fa75bf9f371b4db
  report_path: /root/optiHealth-EiV/outputs/experiments/multi_positive_dcl_20260728/runs/smoke/multi_positive_dcl/9c9e81996eff/seed_17/report.json
  split_sha256: 43dcafef4a0a4a0e4cae3c6bca0b067599ae61b35d1ea1983e4a16ab4ac2f2f5
recipe_fingerprint: {}
verdict: crashed
---

## Summary
Tests hypothesis [[hyp-eiv-multipositive-dcl-20260728]].
At commit `fix/soft-token-norm-scale@955c1b5+dirty-manifest-4329c98f7231`.
External run: /root/optiHealth-EiV/outputs/experiments/multi_positive_dcl_20260728

**Verdict:** crashed

**Crash:** nonfinite loss — both smoke arms (dcl, multi_positive_dcl) returned nonfinite loss; no scientific run was ever produced for this pilot

## Interpretation
**Failure mode:** `execution_bug`

No evidence either way about exact-caption multi-positive DCL from this record: BOTH smoke arms (dcl and multi_positive_dcl) returned nonfinite loss, so zero measurements exist and no scientific run was ever produced. Per run-cross-modal-loss-studies section 6 a smoke run is engineering evidence only, which is why this is logged as crashed rather than inconclusive — the hypothesis stays fully retryable and this record must not be cited as a negative result. The integrity-fixed re-run exp-eiv-multipositive-dcl-pilot-v2-20260728 (same protocol SHA 4329c98f7231, same corrected mask 31f0620e...) is the record that carries actual numbers; read that one instead.

Transfers to: [[hyp-eiv-multipositive-spectral-entropy-20260728]]

## Provenance
- `arm`: `multi_positive_dcl`
- `config_sha256`: `677634a64fa8dafff4da2bdf777f231c70039f1156ded3069fc196964e17d74d`
- `corrected_mask_sha256`: `31f0620e135192349a0add2756d624d0b0e1fa5a519bc776cb8b9e288964e289`
- `environment_sha256`: `a28c900f2a09ce09f8116b37e4d3689541c0a18084e64342c8a511c46d9ee37b`
- `protocol_sha256`: `4329c98f72312aaf2b8e6630861c8f2acbe05e41329d45bd4fa75bf9f371b4db`
- `report_path`: `/root/optiHealth-EiV/outputs/experiments/multi_positive_dcl_20260728/runs/smoke/multi_positive_dcl/9c9e81996eff/seed_17/report.json`
- `split_sha256`: `43dcafef4a0a4a0e4cae3c6bca0b067599ae61b35d1ea1983e4a16ab4ac2f2f5`

## Notes
Closed during the 2026-08-01 backfill. Open since 2026-07-28 with zero measurements. Per run-cross-modal-loss-studies §6 a smoke run is engineering evidence only, so this is a crashed run, not an inconclusive result: it contributes no evidence and the hypothesis stays retryable.

#dcl #multi-positive #contrastive #sensor-text #false-negatives #cross-modal #false-negatives #representation-collapse #subject-heldout #optihealth-eiv
