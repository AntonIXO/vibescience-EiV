---
created_at: '2026-08-05T22:11:08+00:00'
former_ids: []
gates:
- gate-grad-norm-peak-vs-raw
- gate-grad-alignment-trajectory
- gate-donor-prefix-null
- gate-scale-free-reporting
- gate-vram-ceiling
hypothesis_id: the-template-is-already-learned-so-eb13d1
id: rb-e33-gradient-attribution-20260805
notes: []
priority: 50
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: done
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
updated_at: '2026-08-07T12:35:40+00:00'
---

## Prompt
E33 -- GRADIENT ATTRIBUTION. Does the sensor pathway actually RECEIVE usable gradient at the hr_peak token positions, measured against the hr_raw slot in the same target that demonstrably learns?

=== WHY THIS IS THE ONLY SURVIVING HANDLE ===
The problem: the optiHealth-EiV model emits an hr_peak value with NEGATIVE skill, -5.88 bpm vs always-predict-median (-6.48 on the clean sparse population). Experiments E24 through E32 have eliminated BOTH static sides.

REPRESENTATION SIDE, all measured and all clean:
  * signal present in the soft-token prefix: R2 0.8084, increment +0.7211 over true daily mean
  * survives all 34 frozen layers, linearly present at the exact emitting token (R2 0.6850, donor null -0.0014)
  * decodes at 7.605 bpm from the PROJECTOR OUTPUT vs always-predict-median 18.492
  * that 7.605 is a genuine +10.835 bpm increment over the true-daily-mean confound floor of 18.440 (exp-e32; corr(daily mean, daily peak) is only 0.2986, mean explains 0.1028 of peak variance)
  * sits INSIDE the LM's native embedding subspace: projecting onto the top-256 vocab principal directions and discarding everything orthogonal gives 7.604 vs 7.605 unprojected, with a RANDOM subspace of equal rank tracking within 1 bpm (exp-e31)
  * sane scale: effective soft-token/real-token norm ratio 0.581 once Gemma3's sqrt(2560) embed_scale is accounted for (exp-e31)
  * NOT rank-limited: the prefix-capability theorem arXiv:2310.19698 does not bind; k=3 PCA gives 18.407 vs full-dim 10.270 (exp-e29)
  * readout function class is NOT the limit: non-linear LOSES to linear Ridge, CI95 (nonlin-lin) [+0.574, +2.441] (exp-e28)

TARGET SIDE, all measured and all clean (exp-e32):
  * NOT prior-dominated: hr_peak's rendered target carries MORE character entropy than hr_raw's, 11.3474 vs 8.4353 bits -- and hr_raw is the slot that WORKS
  * NOT place-value-scrambled: fixed-width re-render REDUCES per-slot MI, 5.2115 -> 4.2699 bits, while the hr_raw control moves exactly 0.0000
  * hr_peak carries MORE per-slot MI than the succeeding slot (5.2115 vs 3.7626 bits) and still fails

So the representation permits it and the target encodes it, yet the model does not emit it. EVERY NUMBER ABOVE IS EITHER A FITTED PROBE ON FROZEN FEATURES OR A PROPERTY OF THE TARGET DISTRIBUTION. Nothing has measured what the OPTIMISER does. That is the remaining gap.

=== THE INSTRUMENT: A BUILT-IN A/B ===
hr_raw and hr_peak are two numeric slots in the SAME JSON target, SAME objective, SAME architecture, SAME prefix, SAME forward pass. hr_raw LEARNS (R2 +0.830, skill +4.27 bpm); hr_peak COLLAPSES (R2 +0.198, skill -6.48 bpm). Every static explanation for that asymmetry is eliminated, so the difference must appear in the GRADIENT. Any statistic that does not distinguish these two slots is not diagnostic -- compute everything for BOTH, paired, on the same batch and same step.

=== WHAT TO MEASURE ===
Instrument a SHORT training run -- a few hundred steps of the existing lora_proxy_12ch_attnfix profile is enough; this is MEASUREMENT, not a training arm. Per slot type record:

1. GRADIENT NORM reaching the sensor pathway (projector output / soft tokens) attributable to hr_peak value-token positions vs hr_raw value-token positions. Use per-position loss masking to isolate each slot: backward on the hr_peak value-token positions only, record grad norm at the projector; repeat for hr_raw. Same batch, same step, paired.

2. GRADIENT ALIGNMENT over training: cosine between the per-slot gradient at the projector and the direction a fitted Ridge readout says WOULD decode hr_peak (available cheaply from cached features at outputs/probes/e26_digit_ceiling_features.npz). A near-zero or falling cosine for hr_peak while hr_raw's stays positive is the signature of the optimiser never finding the usable direction.

3. LOSS SHARE: fraction of total token loss contributed by each slot's value tokens and how it evolves. hr_peak has HIGHER entropy so it SHOULD contribute more loss. If it contributes more loss but yields less usable gradient at the sensor pathway, that localises the failure to the pathway rather than to objective weighting.

=== MANDATORY CONTROLS ===
This problem has retracted several of its own headline numbers for want of controls. Required:
  * hr_raw as the paired WORKING sibling in every single measurement -- that is the entire point.
  * DONOR PREFIX ARM: recompute gradients with a mismatched sensor prefix. Any statistic that fails to separate real from donor is measuring the text stream, not the conditioning.
  * SHUFFLED-TARGET ARM for the alignment metric.
  * SCALE-FREE REPORTING: report gradient norms relative to the parameter/activation norm at the same site. Raw grad norms at different sites are NOT comparable; quoting them as such would repeat exactly the pre-scaling-vs-post-scaling error that produced a bogus '38x norm mismatch' in exp-e31.

=== PREREGISTER BEFORE RUNNING ===
Write down the expected direction of each of the three statistics under (a) 'the sensor pathway never receives usable gradient at hr_peak positions' and (b) 'gradient is fine, the failure is in the emission mechanism'. If both hypotheses predict the same thing for a statistic, that statistic is NOT diagnostic and must not be used to decide. Preregister the thresholds too; post-hoc criteria are rejected.

=== COST AND ENVIRONMENT ===
One short instrumented run on the RTX 5070. Stay under the 11.25 GiB ceiling; export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True. Launch with 'timeout <N> uv run python -u <script> > logs/e33_grad_attribution.log 2>&1', background=true, notify_on_complete=true -- NEVER pipe a long script to tail/head (Python block-buffers into a pipe and a timeout kill discards the buffer). SMOKE-TEST AT TINY n FIRST: an n=12 smoke caught a silent position-indexing bug in E30 that would have produced a confident wrong answer at n=1000. When locating sensor positions in the assembled context use token_type_ids (1=sensor, src/training/gemma_runtime.py:373), NEVER the slot mask -- that was the bug.

This is the first experiment in the series requiring actual training, justified because the cheap static levers are now genuinely exhausted rather than merely unpromising.

=== HONEST SCOPE ===
Gradient statistics can show the sensor pathway is starved or misaligned at hr_peak positions; they CANNOT by themselves prove that fixing the gradient fixes the emission. A positive result motivates a targeted intervention (auxiliary regression head on the projector, loss reweighting, or zero-init gated cross-attention arXiv:2303.16199). A NULL result means the failure lives in the emission mechanism itself and the architecture question finally gets its hearing.

=== DO NOT RE-LITIGATE ===
Two rule-outs are recorded on this problem and must not be retried: (i) geometry/scale of the handed-over representation -- projector whitening, vocab-embedding-statistics init, soft-token norm rescaling; (ii) target encoding -- prior-domination, variable-width/place-value rendering, and any 'the target lacks information' framing. Also do not retry: dual avg+max pooling (E1), non-linear readout heads (E28), more/finer output bins (E28), window shortening.

## Preconditions
*** BLOCKED PENDING A POWERED EVALUATION -- DEPRIORITISED 10 -> 50 BY exp-e33-collapse-and-premise-audit-20260805 ***

WHY THIS IS NO LONGER TOP OF QUEUE. This runbook was written on the premise that hr_peak emits at -5.88 bpm skill, treated as a stable property of the system. exp-e33 audited that premise and found it NOT quotable as stated:
  * it comes from n=96 rows containing 8 physiologically IMPOSSIBLE targets (a daily MAXIMUM heart rate recorded as 0.0 bpm) and 25% below 90 bpm vs 5% strict-clean;
  * the point estimate swings -2.37 / -6.15 / -4.64 / -3.45 / -5.33 bpm purely with the filter, on the SAME rows, SAME decode, SAME model;
  * on the strict-clean subset the effect is -3.45 with CI95 [-8.52, +2.02], which CROSSES ZERO at n=34.
The deficit appears real but is SMALLER and FAR LESS CERTAIN than -5.88 conveys. Attributing gradient to a target that may not differ significantly from zero risks explaining measurement noise.

HARD PRECONDITION: a properly powered, STRICT-CLEAN native-emission evaluation must first establish an effect size with a CI that EXCLUDES ZERO. Without that there is no target worth attributing gradient to. The Stage C retrain launched 2026-08-05 (N_SEEDS_C=3, LM-level tripwires, adapter outputs/adapters/medgemma_12ch_e33, driver logs/e33_stage_c_driver.log) is intended to supply exactly that evaluation.

DO NOT RE-DERIVE THESE -- already eliminated by exp-e33:
  * mode collapse: std(emitted)/std(true) = 0.9999, ZERO emissions in the documented 54-67 bpm collapse band, donor swap moves the emission by 22.184 bpm (donor MAE 24.179 vs real 14.263). The adapter landed in the GOOD basin.
  * prior sampling: Gini mean difference 25.609 vs actual 14.263 (11.346 bpm gap); shuffling emissions against truth costs +9.982 bpm.

TWO METHOD ERRORS FROM exp-e33 THIS RUNBOOK MUST AVOID:
  * cached prediction arrays are stored over the TEST SPLIT -- pair against y[te], NEVER y[:n]. That slip turned corr 0.5791 into 0.0870 and nearly launched a false mechanism story.
  * a FITTED-PROBE decode (probe_e26_digit_ceiling.py free_run, documented as 'driven by the fitted linear classifiers') is NOT the model's native JSON emission. Verify what an array IS before comparing it to anything.

--- ORIGINAL PRECONDITIONS ---
Repo /root/optiHealth-EiV. GPU RTX 5070, 12227 MiB, ceiling 11.25 GiB, always export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True. Adapter outputs/adapters/medgemma_12ch_e23v2 (4-bit) and profile lora_proxy_12ch_attnfix (configs/training.yaml:404). Cached features at outputs/probes/e26_digit_ceiling_features.npz give the fitted Ridge readout direction for free. Strict-clean population filter and SEED 0 700/300 split as in E26-E32. Launch pattern is mandatory: never pipe a long script to tail/head; use 'timeout <N> uv run python -u <script> > logs/<name>.log 2>&1' with background=true, notify_on_complete=true. Smoke-test at tiny n first -- an n=12 smoke caught a silent position-indexing bug in E30 that would have produced a confident wrong answer at n=1000.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Run as [[the-template-is-already-learned-so-eb13d1]].
Intended gates: `gate-grad-norm-peak-vs-raw`, `gate-grad-alignment-trajectory`, `gate-donor-prefix-null`, `gate-scale-free-reporting`, `gate-vram-ceiling`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#cross-modal #medgemma #collapse #sensor-text
