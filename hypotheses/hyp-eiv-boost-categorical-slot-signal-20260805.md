---
created_at: '2026-08-05T11:21:10+00:00'
gates:
- blocking: true
  description: 'eiv_gen_pred_bucket_entropy >= 0.50 normalized, label-stratified,
    coverage-split at 0.5. Current 0.000 (91/91 tachycardic). This is the behavioural
    bar: the model must actually emit different buckets.'
  id: gate-entropy-recovered
- blocking: true
  description: Paired prefix-swap delta at the bucket slot must rise from +0.418 to
    >= +2.5 nats (mean, length-controlled, n>=40). This is the MECHANISM bar and is
    checkable per-arm without a full eval - it directly tests whether the intervention
    delivered the required signal gain. Prior spread to beat is ~5.08 nats.
  id: gate-effect-size-gain
- blocking: true
  description: eiv_gen_peak_corr >= 0.30 overall, reported split by HR coverage at
    0.5, on the zero-HR-filtered corpus so the metric cannot be inflated by a bimodal
    0-vs-120 distribution. Current honest value +0.159.
  id: gate-peak-corr-honest
- blocking: false
  description: hr_raw full-coverage generation corr must not fall below +0.55 (current
    +0.604). Guards against buying bucket conditioning by damaging the slot that already
    works.
  id: gate-no-hr-raw-regression-2
id: hyp-eiv-boost-categorical-slot-signal-20260805
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: up
  magnitude_note: PRIMARY. From 0.000 to >= 0.50 normalized. Requires the paired-swap
    delta to rise from +0.418 toward the ~5.08 nat prior spread; partial gains may
    raise entropy above 0 without clearing 0.50, which would itself be informative
    about how much headroom each lever supplies.
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: +0.159 -> >= +0.30, measured on the zero-HR-filtered corpus and
    split by coverage so it cannot be inflated by a bimodal true distribution.
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: inconclusive
supersedes: hyp-eiv-label-consistent-sampler-20260805
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The Phase C categorical-slot collapse is a SIGNAL-STRENGTH deficit, not a dead pathway and not a structural impossibility. The bucket slot demonstrably reads the prefix (paired length-controlled swap: +0.418 nats mean, 72.5% positive, Wilcoxon p=0.045, n=40) but the effect is ~12.2x smaller than the ~5.08 nat template/positional prior that decides the emitted token. Therefore at least one of the following cheap interventions - unfreezing the projector (lora_proxy_12ch_attnfix), concentrating value_loss_weight on the bucket token, adding the LM head / final layers to the LoRA targets, or reordering the JSON schema so the bucket is not slot 1 - will raise the paired-swap delta by an order of magnitude and recover a non-degenerate predicted-bucket distribution, WITHOUT changing the conditioning architecture. If none of them moves the delta materially, that is the measured evidence that justifies gated cross-attention.

## Rationale
Grounded in measurement, not inference. The paired length-controlled prefix swap gives +0.4181 nats (median +0.5384, 72.5% positive, Wilcoxon p=4.51e-02, n=40) while the prior spread that must be overcome to change the argmax is 5.080 nats mean / 6.428 median. Ratio 0.0823, so ~12.2x gain is needed. Because the pathway is demonstrably live and correctly signed, amplification is a coherent objective - unlike the marginal-rebalancing line, which was refuted twice (exp-e23-label-consistent-sampler-run1 directional_only, exp-e23v2-three-fixes refutes).

The norm-matched random-prefix control is the key structural clue: a random prefix costs 1.750 nats, 4.2x MORE than swapping another real sample's prefix. The learned prefix subspace therefore carries mostly sample-INVARIANT 'this is well-formed sensor input' mass with only a small per-sample component. Interventions that increase the per-sample variance share (unfreezing the projector, weighting the loss on the discriminative token) target exactly that imbalance, whereas anything that merely re-weights training examples cannot.

A supporting asymmetry: hr_raw generation already works (+0.604 full, +0.909 sparse) through the SAME prefix in the SAME forward pass. Whatever carries the mean to a continuous slot exists; the categorical slot needs more of it. This is why I am not treating the ICLR 2024 additive-prefix bound as decisive here - that bound explains why a fixed additive bias struggles to steer a discrete choice, but it does not establish that the achievable per-sample component is capped below what a 4-way decision needs, and that quantity has never been measured under an unfrozen projector.

## Plan
Run the cheap arms first, in ascending cost, scoring the effect-size gate after each so a winner can be identified without a full generation eval every time:

ARM 1 (config only, profile already exists): lora_proxy_12ch_attnfix - unfreeze projector + log_scale. Never run against these gates.
ARM 2 (config only): value_loss_weight concentrated on the bucket token; requires checking whether prepare_training_inputs can weight one slot rather than all @@-values, otherwise a small change there.
ARM 3 (config only): reorder the json_slots schema so heart_rate_bucket is emitted AFTER hr_peak/hr_raw. Cheapest possible test of the template-prior hypothesis, and it also lets the categorical slot condition on the model's own numeric output. Note hr_peak generation is itself collapsed, so this may not suffice alone.
ARM 4 (small code change): add the LM head / final N layers to the LoRA target modules.

For each arm: 2600 steps, frozen encoder unless the arm says otherwise, E22-corrected mask, E23 sampler + zero-HR filter held constant. Score gate-effect-size-gain first (n>=40, ~5 min), and only run the full label-stratified generation eval on arms that clear it. If no arm reaches +2.5 nats, that is the evidence that justifies the architecture change - and it will be a measured justification rather than an inferred one.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_gen_pred_bucket_entropy]] (up).
Predicts [[eiv_gen_peak_corr]] (up).
Gate `gate-entropy-recovered` (blocking): eiv_gen_pred_bucket_entropy >= 0.50 normalized, label-stratified, coverage-split at 0.5. Current 0.000 (91/91 tachycardic). This is the behavioural bar: the model must actually emit different buckets.
Gate `gate-effect-size-gain` (blocking): Paired prefix-swap delta at the bucket slot must rise from +0.418 to >= +2.5 nats (mean, length-controlled, n>=40). This is the MECHANISM bar and is checkable per-arm without a full eval - it directly tests whether the intervention delivered the required signal gain. Prior spread to beat is ~5.08 nats.
Gate `gate-peak-corr-honest` (blocking): eiv_gen_peak_corr >= 0.30 overall, reported split by HR coverage at 0.5, on the zero-HR-filtered corpus so the metric cannot be inflated by a bimodal 0-vs-120 distribution. Current honest value +0.159.
Gate `gate-no-hr-raw-regression-2` (advisory): hr_raw full-coverage generation corr must not fall below +0.55 (current +0.604). Guards against buying bucket conditioning by damaging the slot that already works.
Supersedes [[hyp-eiv-label-consistent-sampler-20260805]].

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
