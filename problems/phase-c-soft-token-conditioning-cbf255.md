---
created_at: '2026-08-04T21:55:42+00:00'
former_ids:
- phase-c-soft-token-conditioning-collapses-to-a-constant-output-at-generation-despite-r2-0-99-linear-decodability-of-the-prefix
id: phase-c-soft-token-conditioning-cbf255
paper_refs: []
problem_tags:
- optihealth-eiv
- conditioning-collapse
status: resolved
title: Phase C soft-token conditioning collapses to a constant output at generation
  despite R2=0.99 linear decodability of the prefix
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
updated_at: '2026-08-04T21:55:42+00:00'
updates: []
---

## Description
> **RESOLVED 2026-08-05 — root cause was a train/eval channel_mask semantics inversion, NOT a
> structural limit.** Confirmed by hypothesis
> `the-reported-phase-c-collapse-to-the-same-output-is-not-a-dead-conditioning-path`
> (verdict computed by close_experiment, gates `sparse_corr_recovery` and
> `constant_output_removed` both passed). Fixed at commit `c674cee`.
>
> The "OBSERVED FAILURE" and "KEY PARADOX" text below describes the PRE-FIX world and is
> **no longer true**. Kept verbatim for provenance — do not read it as current state.
>
> Post-fix measurements (adapter `medgemma_12ch_v1`, W&B `tzao0lok`):
> - P2 ablation `output_diff_rate_zero` / `_random`: 0.25 -> **1.00** (pathway causally live)
> - P1 hr_r2 0.639 -> **0.833**; hr_mae 16.9 -> **6.28**
> - hr_raw generation corr **+0.795** full-coverage / **+0.894** sparse
> - shifted-input `frac_responsive` 0.06 -> **0.50** (the "+0.0 bpm shift" below is stale)
>
> **Residual defect tracked separately** as
> `phase-c-hr-peak-saturated-prior-20260805` (hr_peak collapses onto an all-tachy prior,
> overall corr -0.013). That is a DISTINCT failure with training-instability as the leading
> candidate — do not re-open this problem for it.

OptiHealth-EiV Phase C: frozen MedGemma-1.5-4B (4bit QLoRA r=32) conditioned on 96 soft-token prefixes from a temporal encoder + cross-modal projector (Phase A InfoNCE, Phase B triplet). The LM emits a json_slots annotation with numeric HR fields.

OBSERVED FAILURE (PRE-FIX, superseded): generation collapses to a near-constant value (~population mean HR) regardless of sensor input. A +30 bpm input shift produces +0.0 bpm output shift.

KEY PARADOX:
- Ridge probe on mean-pooled soft tokens: HR R2=0.99 in-dist, 0.94 subject-held-out; peak R2=0.81; elevated AUC=0.98. Information IS linearly present.
- Causal attention knockout of 100% sensor attention: only +0.032 CE (~2.5%).
- Full sensor swap holding target fixed: ~4% value-token CE change.
- Sensor attention mass 0.13 vs uniform 0.22.

THREE PRIOR LEVERS ALL FAILED AT GENERATION (E17/E18/E18b):
1. attention-fix (unfreeze projector + log_scale) -> predictions collapse to ~121
2. objective-augmentation (hr_augment_prob 1.0) -> +0.0 bpm shift response
3. swap-contrastive margin (provably prefix-requiring) -> SATISFIED in teacher forcing (train swap gap 0.04->1.1) but +0.0 bpm at generation

The repo's FINAL VERDICT concluded a structural/data limit and recommended abandoning the bridge. This problem re-opens that verdict: all three failed levers assumed the per-sample signal reaching the LM is resolvable. None tested the GEOMETRY of the prefix itself.

NEW EVIDENCE (E19, this session): mean-domination REFUTED. signal/mean=0.556, effective rank 90/191, centered inter-sample cosine 0.005 (near-orthogonal per-sample codes), 2.1x MORE variable than real MedGemma text tokens. So the prefix is richly sample-specific. This forces the next question: is the signal in a direction the frozen LM readout can USE (basis alignment), rather than whether it exists.

RELEVANT LITERATURE: ICLR 2024 'When Do Prompting and Prefix-Tuning Work?' proves a prefix cannot change the RELATIVE attention pattern over content tokens - it can only add a fixed bias to the attention layer output. This is a structural limitation of the additive-prefix architecture, and predicts exactly the observed knockout result.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse
