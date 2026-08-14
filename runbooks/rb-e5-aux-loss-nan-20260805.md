---
created_at: '2026-08-05T15:47:15+00:00'
gates: []
hypothesis_id: null
id: rb-e5-aux-loss-nan-20260805
priority: 60
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: abandoned
topic_tags: []
updated_at: '2026-08-05T16:37:08+00:00'
---

## Prompt
RESOLVED 2026-08-05 — NOT A DEFECT. Investigated read-only, no retrain.

FINDING: aux_loss=nan is a DISPLAY SENTINEL, not a numerical failure. pipeline.py:3538 reads `aux_value = float(aux_loss.detach().cpu().item()) if aux_loss is not None else float('nan')` — nan IS the None-branch. The term is only constructed if contrastive_aux_weight > 0.0 (guard at pipeline.py:3157-3161), and profile lora_proxy_12ch_attnfix sets contrastive_aux_weight: 0.0. It is added to total_loss only under `if aux_loss is not None` (lines 3427/3433), so it never entered the graph and could not poison the backward.

DISCRIMINATING CONTROL: the sibling field hr_aux on the SAME log line uses the identical None-sentinel idiom but has hr_regression_weight: 0.1 (ENABLED) — finite at 130/130 steps (grep -c 'hr_aux=nan' = 0), decreasing 0.3740 -> 0.0250. An enabled aux term prints numbers; a disabled one prints nan. Rules out a shared logging/dtype/device fault.

CONSEQUENCE: the feared reading ('every arm on this profile trained without an intended auxiliary term') is FALSE — the term was deliberately off by config, not silently broken. NO prior result on lora_proxy_12ch_attnfix needs reinterpretation.

Recorded to vibescience as rule_out id: the-aux-loss-nan-at-every-logged-step-of-the-arm-1-run-logs-arm1-attnfix-log-pro

STATUS NOTE: closed as 'abandoned' rather than 'done' only because it recorded a rule_out and no hypothesis (no preregistration => no calibration weight). The investigation itself is COMPLETE; do not re-open.

--- ORIGINAL PROMPT BELOW ---
In /root/optiHealth-EiV, logs/arm1_attnfix.log shows aux_loss=nan at EVERY logged step of the COMPLETED Arm 1 run, e.g.:
  step=2560 loss=5.5481 llm_loss=0.6668 probe_rmse_norm=2.2079 aux_loss=nan hr_aux=0.0628 full_drop=False

The run completed (2600 steps, final loss 3.658) and other loss terms were finite, so this was never investigated. Determine whether the auxiliary term was silently contributing NOTHING for the whole run. If so, every arm on profile lora_proxy_12ch_attnfix has been training without it, which changes how ALL prior results on that profile should be read.

Profile: lora_proxy_12ch_attnfix. Encoder checkpoint: outputs/checkpoints/12ch_v1/phase_b. If it is a real defect, log it to vibescience as its own problem.

## Preconditions
Read-only investigation of an existing log; no retrain required to diagnose.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
