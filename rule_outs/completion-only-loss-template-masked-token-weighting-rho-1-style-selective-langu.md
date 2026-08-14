---
created_at: '2026-08-06T19:29:02+00:00'
id: completion-only-loss-template-masked-token-weighting-rho-1-style-selective-langu
problem_id: phase-c-annotation-target-is-86pct-template-20260806
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-06T19:29:02+00:00'
topic_tags: []
---

## Claim
Completion-only loss / template-masked token weighting (RHO-1 style selective language modelling on the sensor-dependent spans) is a high-value cheap intervention worth ~7.2x effective gradient on the varying characters. ELIMINATED as a MAGNITUDE claim. Also eliminated: quoting '86.2% of the target is fixed template' as if it were a statement about where the GRADIENT goes.

## Method
exp-e39 / runbook rb-e33-gradient-attribution. n=32 strict-clean, adapter medgemma_12ch_e33.seed_1, teacher-forced on the production training path (_diag_common.prep -> prepare_training_inputs, so the same value_loss_weight marking the @@ spans that training uses). Per-token cross-entropy split into VALUE tokens (loss weight >= VALUE_WEIGHT) and TEMPLATE tokens, then torch.autograd.grad of each summed term taken w.r.t. the input-embedding slice carrying the soft-token prefix, and the gradient norm restricted to that slice. Hypothesis committed to the 'template is already learned' branch BEFORE running; smoke at n=6 first.

## Evidence
value tokens 17.8 vs template 42.0 per response -> value tokens are 29.7% of the response, not the 13.8% the character count implied. CE per token: value 0.7695 vs template 0.0127, a 60.4x ratio. Summed CE mass: value 0.962 / template 0.038. Gradient norm into the sensor block: value 1.0110 vs template 0.5125, value share 0.659 (IQR 0.591-0.771). So the template contributes 3.8% of the loss and 34% of the gradient norm; masking it caps out at 1.04x on loss mass and 1.52x on gradient norm, not 7.2x. The 97.5% parse rate was already telling us the template was solved; I priced a solved sub-problem as if it were competing for capacity. WHAT SURVIVES: the 86.2% character figure is still correct as a description of the TARGET, and the CLM-vs-auxiliary imbalance (CLM = 3.4-4.8% of total loss) is untouched by this and is now the primary loss-side lever. WHAT IS CORRECTED: the bottleneck is not the within-CLM token balance. GENERAL LESSON: character count is not token count is not loss mass is not gradient. Three conversions, each of which moved the number by 2-15x here.

## Links
Rules out for problem [[phase-c-annotation-target-is-86pct-template-20260806]].
