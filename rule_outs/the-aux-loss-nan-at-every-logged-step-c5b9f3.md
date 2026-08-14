---
created_at: '2026-08-05T16:36:37+00:00'
former_ids:
- the-aux-loss-nan-at-every-logged-step-of-the-arm-1-run-logs-arm1-attnfix-log-pro
id: the-aux-loss-nan-at-every-logged-step-c5b9f3
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T16:36:37+00:00'
topic_tags: []
---

## Claim
The aux_loss=nan at every logged step of the Arm 1 run (logs/arm1_attnfix.log, profile lora_proxy_12ch_attnfix) is a DISPLAY SENTINEL for a config-disabled loss term, NOT a numerical failure and NOT a silently-dead-but-intended auxiliary objective. Prior results on this profile do NOT need reinterpretation on account of it. Closes the open thread 'aux_loss=nan never investigated' (runbook rb-e5-aux-loss-nan-20260805).

## Method
Read-only static trace (no retrain, no GPU) of the Phase C LoRA-proxy training loop in src/training/pipeline.py, cross-referenced against the profile config in configs/training.yaml and the completed run log logs/arm1_attnfix.log. Located the exact emitting logger call (pipeline.py:3542-3546), traced the aux_loss construction guard (3157-3161) and its two accumulation sites (3427-3428 warmup, 3433-3434 main), read contrastive_aux_weight from the profile, then used the sibling hr_aux field on the SAME log line as a discriminating control for whether the nan is a sentinel or a numerical event.

## Evidence
CODE: pipeline.py:3538  aux_value = float(aux_loss.detach().cpu().item()) if aux_loss is not None else float('nan')  -- nan IS the None-branch. pipeline.py:3157-3161 constructs aux_loss only if (contrastive_aux_weight > 0.0 AND aux_head is not None AND not full_dropout_active). pipeline.py:3433-3434 and 3427-3428 add it to total_loss only under 'if aux_loss is not None'.

CONFIG: configs/training.yaml profile lora_proxy_12ch_attnfix -> contrastive_aux_weight: 0.0 (equal to the signature default at pipeline.py:1019). First guard condition is False at every step => aux_loss is None for all 2600 steps => term never entered the graph.

LOG: 130/130 logged steps show the literal string 'aux_loss=nan', zero numeric variation, present already at step=0 (step=0 loss=2024.0374 llm_loss=2.8643 probe_rmse_norm=44.9889 aux_loss=nan hr_aux=0.3740). A term that diverged numerically would print finite values before becoming nan; nan from step 0 with no variation is a constant sentinel.

DISCRIMINATING CONTROL (same log line, same print idiom, opposite config): hr_aux has hr_regression_weight: 0.1 (ENABLED) and is FINITE at 130/130 steps -- grep -c 'hr_aux=nan' = 0 -- spanning 0.3740 (step 0) / 4.2385 (step 20) / 0.0628 (step 2560) / 0.0250 (step 2580), i.e. decreasing and live. Both fields print through the identical 'x if not None else nan' idiom (lines 3538 and 3539-3541), so an ENABLED aux term prints numbers and a DISABLED one prints nan. This rules out a shared logging/dtype/device fault on that log line.

RUN OUTCOME consistent: 2600 steps completed, final loss 3.658, all other terms finite.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].
