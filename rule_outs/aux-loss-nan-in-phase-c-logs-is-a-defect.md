---
created_at: '2026-09-01T07:48:11+00:00'
former_ids: []
id: aux-loss-nan-in-phase-c-logs-is-a-defect
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T07:48:11+00:00'
topic_tags: []
---

## Claim
The `aux_loss=nan hr_aux=nan` on every Phase C step line is a defect -- a silently disabled or broken auxiliary loss that should be investigated.

## Method
Read the guard conditions and the logging construction in src/training/pipeline.py (lines 4502-4506, 4554-4556, 4927-4934) and cross-read the weights actually set by the C profile in configs/training.yaml. No GPU.

## Evidence
NOT A DEFECT. It is the correct encoding of 'this term was not computed', and it is exactly the discipline this vault demanded after the p3/p6 incident (a probe that cannot run must emit null, never 0.0).

TRACED IN CODE, src/training/pipeline.py:
  4502  aux_loss: torch.Tensor | None = None
  4503-4506  computed only if contrastive_aux_weight > 0.0 AND aux_head is not None AND not full_dropout_active
  4554  hr_aux_loss: torch.Tensor | None = None
  4556  computed only if hr_head is not None AND hr_regression_weight > 0.0
  4927  aux_value = float(aux_loss.item()) if aux_loss is not None else float('nan')
  4928-4930  same construction for hr_aux_value
So `nan` in the log line means the tensor is None, which means the guard was false.

THE GUARD IS FALSE BY CONFIGURATION, NOT BY ACCIDENT. lora_proxy_12ch_wl_band04 -- the C profile every arm in this campaign uses -- declares contrastive_aux_weight: 0.0 and hr_regression_weight: 0.0. Both terms are therefore deliberately off, and both are correctly reported as not-measured rather than as 0.0.

WHY 0.0 WOULD HAVE BEEN THE BUG: a disabled auxiliary loss logged as 0.0 is indistinguishable from an ENABLED one that has converged to zero, and this repo has already paid for that exact confusion four times (p3 swap 0.0, p6 shuffle_gap 0.0, the p4 scorer, and W&B hr_r2 logging an ungradiented random head at ~-47 while the same checkpoint probed at 0.9713). nan here is the fix, not the symptom.

RAISED BY: the heartbeat cron flagged it on three consecutive fires as 'if the aux head should be active in this config, this is a silently disabled loss'. The conditional was the right instinct; the answer is that it should not be active. Recorded so the next reader does not re-open it.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
