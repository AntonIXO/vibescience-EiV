---
created_at: '2026-08-14T09:21:38+00:00'
former_ids:
- arm-numeric-scalar-regression-heads-off-layer-34-hidden-state-bypass-lm-head-dec
gates: []
hypothesis_id: n-of-1-head-calibration-linear-ridgecv-a45392
id: arm-numeric-scalar-regression-heads-off-cbe381
notes: []
priority: 15
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
status: done
topic_tags:
- readout
- deployment-distribution
- medgemma
updated_at: '2026-08-14T12:44:20+00:00'
---

## Prompt
ARM: Numeric scalar regression heads off layer-34 hidden state (bypass lm_head decode for numeric slots).

CONTEXT (self-contained): Repo /root/optiHealth-EiV, branch fix/soft-token-norm-scale, baseline recipe lora_proxy_12ch_wl_band04 (checkpoint outputs/checkpoints/v9band04_s42/phase_c, MedGemma-1.5-4b-it, 34 LM layers, d_llm 2560). The E6-E10 probe family (2026-08-14, vibescience experiments exp-e6/e7/e8/e10-*) localized ALL numeric-slot failures to the frozen lm_head/decode path: supply is proven at every upstream stage (raw per-patch peak incr 0.485 -> encoder 0.500 -> projector soft tokens 0.526 -> LM final-layer hidden state +0.503 incr beyond level for hr_peak; prefix R2 0.969 / MAE 2.77ms vs trivial 17.64 for hrv while emissions only reach corr 0.60). A scalar head reads that supply directly.

DESIGN:
1. MLP head per numeric slot (hr_raw, hr_peak, hrv_raw): 2560 -> 256 -> 1, input = final-layer hidden state at the slot's value-emitting position (position machinery already in scratch/diag_e24_lm_hidden_peak.py from E8). Categorical slots stay on lm_head text path.
2. Arm A (cheap, do FIRST): train heads OFFLINE on captured hidden states (no LLM backward, CPU/small-GPU, minutes). Only if A shows signal, arm B trains heads in-pipeline.
3. Launch discipline: systemd-run --scope -p MemoryMax=<N>G (OOM rule in optihealth-eiv skill); captures via scratch/run_e9_lmhead.sh pattern; score with scratch/e9_score_gates.py --tag <tag>.

GATES (preregister in vibescience BEFORE launch):
- g1 BLOCKING: dense-128 hr_peak MAE beats strongest trivial (always-median AND hr_raw+median-excursion derived) — positive skill (v9band04 reference: skill -2.88, i.e. currently LOSES to trivial).
- g2 BLOCKING: supa hr_raw MAE <= 3.45, dense <= 4.73 (hr axes preserved).
- g3: hrv supa MAE < 8.31 trivial AND dense corr > 0.60.
- g4: VRAM <= 12.2 GiB on RTX 5070.
Priors: E8 final-layer R2 0.544 (peak beyond level) implies dense peak MAE ~11-12 bpm vs trivial ~17.8 — expected skill +5-6 bpm.

DEPLOYMENT FLAG for Anton: this changes eiv annotate emission from pure text to text+head merge (heads override numeric text slots). Architecture change, not recipe change — needs his sign-off before baseline designation.

## Preconditions
E9 (exp-e9-lmhead-lora-20260814, v11lmhead_s42) has been scored and FAILED a blocking gate (dense peak sign-flip or hr preservation). If E9 passed, this arm is unnecessary — do not run.

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Run as [[n-of-1-head-calibration-linear-ridgecv-a45392]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#readout #deployment-distribution #medgemma
