---
artifacts: []
closed: true
config_note: 'Scientific v2 after engineering-smoke-only integrity fix. Frozen validation
  pilot: paired seeds 17,23,29; arms dcl and multi_positive_dcl; 500 steps, bf16,
  batch102, RTX 5070; population stats disabled. Manifest SHA256 a8c45395db640ffdfb2c5bfb5b2ae50d7b64f8f6e5bcb5b11cb4c439902b3a61;
  protocol SHA256 4329c98f72312aaf2b8e6630861c8f2acbe05e41329d45bd4fa75bf9f371b4db;
  campaign implementation SHA256 3575d43805a25f99fb8fd182b8502999f1b0f7b0d5cd49e47ca3d99ff9d1aeba;
  corrected mask SHA256 31f0620e135192349a0add2756d624d0b0e1fa5a519bc776cb8b9e288964e289;
  exact caption predicate spec SHA256 46d57eecd32d0db7bdbe96e0434072204d90167b0a555a87511644179459cede.
  Undefined per-domain R2 for domains with one validation window is explicitly non-gating;
  all preregistered aggregate/native metrics must be finite. Test is locked.'
crash_reason: ''
created_at: '2026-07-28T08:46:59+00:00'
diagnostics_measured:
- after: 0.09975062344139651
  before: 0.09559434746467164
  diagnostic_id: eiv_duplicate_recall5
- after: 4.707040748763497
  before: 4.626994046473621
  diagnostic_id: eiv_effective_rank
- after: 40.666666666666664
  before: 41.666666666666664
  diagnostic_id: eiv_crossmodal_median_rank
- after: 0.0
  before: 1.168
  diagnostic_id: eiv_false_negative_pairs_per_batch
- after: 0.15596114357128782
  before: 0.1231137265087977
  diagnostic_id: eiv_sensor_median_cosine
- after: 0.08559141945862525
  before: 0.08806345060439189
  diagnostic_id: eiv_crossmodal_alignment_gap
- after: -1.5609140698040056
  before: 0.953219392832034
  diagnostic_id: eiv_heldout_hr_r2
- after: 5.205078125
  before: 5.205078125
  diagnostic_id: eiv_peak_vram_gib
- after: 113.69205976633143
  before: 113.71233136666706
  diagnostic_id: eiv_training_runtime_sec
do_not_retry: []
external_run: /root/optiHealth-EiV/outputs/experiments/multi_positive_dcl_20260728_v2
former_ids: []
gate_results: []
git_ref: fix/soft-token-norm-scale@955c1b5+dirty-manifest-a8c45395db64
hypothesis_id: hyp-eiv-multipositive-dcl-20260728
id: exp-eiv-multipositive-dcl-pilot-v2-20260728
key_numbers: {}
observed_effects:
- delta: 0.004156275977
  diagnostic_id: eiv_duplicate_recall5
  direction: up
- delta: 0.08004670229
  diagnostic_id: eiv_effective_rank
  direction: up
- delta: -1.0
  diagnostic_id: eiv_crossmodal_median_rank
  direction: down
- delta: -1.168
  diagnostic_id: eiv_false_negative_pairs_per_batch
  direction: down
- delta: 0.032847417062
  diagnostic_id: eiv_sensor_median_cosine
  direction: up
- delta: -0.002472031146
  diagnostic_id: eiv_crossmodal_alignment_gap
  direction: down
- delta: -2.514133462636
  diagnostic_id: eiv_heldout_hr_r2
  direction: down
- delta: 0.0
  diagnostic_id: eiv_peak_vram_gib
  direction: none
- delta: -0.020271600336
  diagnostic_id: eiv_training_runtime_sec
  direction: down
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_crossmodal_median_rank: true
    eiv_duplicate_recall5: true
    eiv_effective_rank: true
    eiv_false_negative_pairs_per_batch: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[hyp-eiv-multipositive-dcl-20260728]].
At commit `fix/soft-token-norm-scale@955c1b5+dirty-manifest-a8c45395db64`.
External run: /root/optiHealth-EiV/outputs/experiments/multi_positive_dcl_20260728_v2

## Measurements
- [[eiv_duplicate_recall5]]: before 0.09559434746467164 → after 0.09975062344139651  (Δ +0.00415628, up ✓ predicted up)
- [[eiv_effective_rank]]: before 4.626994046473621 → after 4.707040748763497  (Δ +0.0800467, up ✓ predicted up)
- [[eiv_crossmodal_median_rank]]: before 41.666666666666664 → after 40.666666666666664  (Δ -1, down ✓ predicted down)
- [[eiv_false_negative_pairs_per_batch]]: before 1.168 → after 0.0  (Δ -1.168, down ✓ predicted down)
- [[eiv_sensor_median_cosine]]: before 0.1231137265087977 → after 0.15596114357128782  (Δ +0.0328474, up)
- [[eiv_crossmodal_alignment_gap]]: before 0.08806345060439189 → after 0.08559141945862525  (Δ -0.00247203, down)
- [[eiv_heldout_hr_r2]]: before 0.953219392832034 → after -1.5609140698040056  (Δ -2.51413, down)
- [[eiv_peak_vram_gib]]: before 5.205078125 → after 5.205078125  (Δ +0, flat)
- [[eiv_training_runtime_sec]]: before 113.71233136666706 → after 113.69205976633143  (Δ -0.0202716, down)

**Verdict:** supports

## Notes
Direction-only primary prediction matched: mean duplicate-aware Recall@5 rose 0.09559435 to 0.09975062 (delta +0.00415628). The preregistered pilot study gate FAILED: required delta was >=+0.005 and observed treatment effective ranks 4.380875/4.849925/4.890322 all remained below the unchanged absolute rank>=5 gate. Per-seed Recall deltas were 0,+0.00498753,+0.00748130; median-rank deltas -1,-4,+2. Mechanism succeeded exactly: DCL used 584/566/602 caption-equivalent false-negative directed pairs in 45.0%/44.0%/45.6% of batches; treatment used zero with matched batches and initializations. Median cosine remained healthy but worsened on average 0.123114 to 0.155961; alignment gap decreased 0.0880635 to 0.0855914. Secondary saturated Ridge was numerically unstable: seed23 treatment R2=-6.594 and MAE=26.02 bpm with singular/ill-conditioned warnings, driving mean R2 down; this was preserved and was not a selection gate. All integrity/resource gates passed, 5.205 GiB peak, no OOM/nonfinite. pilot_summary SHA256 232b98d94328c01b8d96f233b82ff103823a43aeeff0db6ff7779ade454faf46. selected=null; no confirmation_lock; no test_unlock; zero confirmation/test files. Direction matched; preregistered magnitude/collapse gates failed; no confirmation/deployment/test.

#dcl #multi-positive #contrastive #sensor-text #false-negatives #cross-modal #false-negatives #representation-collapse #subject-heldout #optihealth-eiv
