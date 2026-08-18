---
artifacts: []
closed: true
config_note: 'E7 localization probe, frozen v9band04_s42 encoder+fusion+projector
  (rmsnorm_scale_v3, 96 slots, d_llm 2560). Same pool/seed as E6: strict-clean eval_v3_1min
  n=4000, GroupKFold(5) by subject, RidgeCV. Arms: pooled soft tokens (masked-mean),
  slot mean+max concat, fold-fit PCA-256 of flattened slots. Encoder-side same-pool
  reference: increment 0.5004 (E6). Gate g1 two-sided: <0.15 confirms projector-lossy,
  >=0.35 refutes (LM-side lever).'
cost: null
crash_reason: ''
created_at: '2026-08-14T08:01:33+00:00'
diagnostics_measured:
- after: 0.5263
  before: 0.5004
  diagnostic_id: eiv_peak_excursion_increment_r2
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: REFUTE zone. Best soft-token readout increment 0.5263 >= 0.35 (confirm
    required < 0.15). The projector output carries AT LEAST as much peak-excursion
    information as its input (encoder-side 0.5004 on the same pool/folds). The projector
    is NOT the lossy stage; the loss to generation is downstream, inside the LM forward/readout.
  gate_id: g1-projector-lossy
  passed: false
- evidence: 'Pooled (masked-mean over all 552 soft tokens) 0.4730 vs per-slot mean+max
    0.5263 vs RP-4096 0.5100: even the fully POOLED vector retains 0.47, so neither
    the cross-attention nor the pooling destroys the peak. Note: the LM actually receives
    ALL 552 soft tokens (pooling is only used by the aux contrastive path), so the
    relevant number for the LM input is the per-slot one, 0.5263.'
  gate_id: g2-pool-vs-slots
  passed: true
git_ref: ''
hypothesis_id: hyp-e7-projector-bottleneck-20260814
id: exp-e7-projector-bottleneck-20260814
interpretation:
  at: '2026-08-18T21:08:44+00:00'
  failure_mode: claim_false
  lesson: 'The CrossModalProjector is NOT the stage that destroys daily peak-excursion
    information, so projector surgery (max-preserving pooling, excursion-dedicated
    slot) is the wrong lever: the best soft-token readout at the projector OUTPUT
    scored increment R2 0.5263, landing in the preregistered REFUTE zone (>=0.35)
    rather than the confirm zone (<0.15), and actually exceeding its own input (encoder+fusion
    0.5004 on the identical strict-clean n=4000 GroupKFold(5)-by-subject pool). Even
    the fully pooled masked-mean vector retained 0.4730 (per-slot mean+max 0.5263,
    sparse RP-4096 0.5100), so neither cross-attention nor pooling is lossy. This
    closes the localization chain at raw supply 0.4847 -> encoder 0.5004 -> projector
    0.5263 while generation-side hr_peak dense corr is only 0.17-0.35 with negative
    skill: the loss is entirely downstream inside the frozen LM forward/readout. Two
    record corrections to carry forward: the projector emits 552 soft tokens (24x23),
    not 96 slots, and any probe materializing >1GB of features must stream to a disk
    memmap under systemd-run MemoryMax (v1 OOM-killed the host 3x).'
  transfers_to:
  - hyp-e6-peak-maxpool-readout-20260814
  - hyp-e8-lm-readout-transfer-20260814
  - hyp-e17-unified-readout-operator-20260815
  - the-hr-peak-signal-at-the-projector-4aa472
  - the-hr-peak-failure-is-a-readout-965f58
key_numbers: {}
observed_effects:
- delta: 0.0259
  diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
parent_experiment_id: exp-e6-peak-maxpool-20260814
prediction_match:
  overall: false
  per_diagnostic:
    eiv_peak_excursion_increment_r2: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[hyp-e7-projector-bottleneck-20260814]].
Branches from [[exp-e6-peak-maxpool-20260814]].

## Measurements
- [[eiv_peak_excursion_increment_r2]]: before 0.5004 → after 0.5263  (Δ +0.0259, up ✗ predicted down)

## Gates
- `g1-projector-lossy`: **FAIL** — REFUTE zone. Best soft-token readout increment 0.5263 >= 0.35 (confirm required < 0.15). The projector output carries AT LEAST as much peak-excursion information as its input (encoder-side 0.5004 on the same pool/folds). The projector is NOT the lossy stage; the loss to generation is downstream, inside the LM forward/readout.
- `g2-pool-vs-slots`: PASS — Pooled (masked-mean over all 552 soft tokens) 0.4730 vs per-slot mean+max 0.5263 vs RP-4096 0.5100: even the fully POOLED vector retains 0.47, so neither the cross-attention nor the pooling destroys the peak. Note: the LM actually receives ALL 552 soft tokens (pooling is only used by the aux contrastive path), so the relevant number for the LM input is the per-slot one, 0.5263.

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The CrossModalProjector is NOT the stage that destroys daily peak-excursion information, so projector surgery (max-preserving pooling, excursion-dedicated slot) is the wrong lever: the best soft-token readout at the projector OUTPUT scored increment R2 0.5263, landing in the preregistered REFUTE zone (>=0.35) rather than the confirm zone (<0.15), and actually exceeding its own input (encoder+fusion 0.5004 on the identical strict-clean n=4000 GroupKFold(5)-by-subject pool). Even the fully pooled masked-mean vector retained 0.4730 (per-slot mean+max 0.5263, sparse RP-4096 0.5100), so neither cross-attention nor pooling is lossy. This closes the localization chain at raw supply 0.4847 -> encoder 0.5004 -> projector 0.5263 while generation-side hr_peak dense corr is only 0.17-0.35 with negative skill: the loss is entirely downstream inside the frozen LM forward/readout. Two record corrections to carry forward: the projector emits 552 soft tokens (24x23), not 96 slots, and any probe materializing >1GB of features must stream to a disk memmap under systemd-run MemoryMax (v1 OOM-killed the host 3x).

Transfers to: [[hyp-e6-peak-maxpool-readout-20260814]], [[hyp-e8-lm-readout-transfer-20260814]], [[hyp-e17-unified-readout-operator-20260815]], [[the-hr-peak-signal-at-the-projector-4aa472]], [[the-hr-peak-failure-is-a-readout-965f58]]

## Notes
REFUTED, and that is the decisive branch: the CrossModalProjector is NOT the lossy stage. The chain is now fully localized by three same-pool measurements: raw supply 0.4847 -> encoder+fusion 0.5004 -> PROJECTOR OUTPUT 0.5263 (all increments over a level control of -0.0113) -> generation-side hr_peak dense corr 0.17-0.35 with NEGATIVE skill. The entire loss happens INSIDE the frozen LM forward + readout. This vindicates and sharpens the old eiv_lm_hidden_peak_r2 design question one stage earlier than planned: the prefix demonstrably delivers the peak (0.53 beyond level) to the LM's input embedding space; the LM either destroys it in-flight or cannot express it through lm_head. IMPORTANT CORRECTIONS TO THE RECORD: (1) the projector emits 552 soft tokens (24 x 23), not 96 sensor_slots — the '96 slots' in this hypothesis's config_note was wrong, and the LM receives all 552; (2) the PCA-256 arm was replaced by fit-free sparse RP-4096 for memory reasons (documented in gate g1 evidence); (3) OPERATIONAL: v1 of this probe OOM-killed the host 3x and took the hermes gateway down with it (gateway cgroup, OOMPolicy=stop) — rule: any probe materializing >1GB features must stream to disk memmap and run under systemd-run --scope -p MemoryMax. NEXT (E8): eiv_lm_hidden_peak_r2 exactly as its diagnostic prescribes — ridge probe on frozen-LM hidden states at the hr_peak emission position, per layer, with hr_raw position as within-model positive control and donor-prefix null. HIGH final-layer R2 -> readout lever (rb-e4-lora-lm-head, E26 scoping ~4bpm); LOW -> in-flight destruction -> prefix geometry/gated cross-attention levers.

#cross-modal #pooling #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
