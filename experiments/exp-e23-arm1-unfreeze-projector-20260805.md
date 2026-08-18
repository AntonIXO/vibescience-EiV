---
artifacts: []
closed: true
config_note: Arm 1 of the cheap-lever list = lever (c) unfreeze the projector. Profile
  lora_proxy_12ch_attnfix, encoder checkpoint outputs/checkpoints/12ch_v1/phase_b,
  2600 steps, final loss 3.658, adapter outputs/adapters/medgemma_12ch_arm1_attnfix.
  Evaluated label-stratified n=12/label (96 samples), split at HR coverage 0.5, against
  gates eiv_gen_pred_bucket_entropy>=0.50 and eiv_gen_peak_corr>=0.30. Companion run
  E23N re-tested the RESCUED +0.418 nat swap effect on the e23v2 baseline with 6 donors/sample
  and a dose-response control.
cost: null
crash_reason: ''
created_at: '2026-08-05T14:13:49+00:00'
diagnostics_measured:
- after: 0.3703
  before: 0.0
  diagnostic_id: eiv_gen_pred_bucket_entropy
- after: 0.6036
  before: 0.1587
  diagnostic_id: eiv_gen_peak_corr
- after: 27.76
  before: 2.6
  diagnostic_id: eiv_gen_between_sample_std
- after: 0.9
  before: 0.5
  diagnostic_id: eiv_prefix_swap_bootstrap_p
- after: 2241.35
  before: 2241.35
  diagnostic_id: eiv_training_runtime_sec
do_not_retry: []
external_run: ''
former_ids: []
gate_results:
- evidence: ''
  gate_id: gate-entropy-recovered
  passed: false
- evidence: ''
  gate_id: gate-peak-corr-honest
  passed: true
- evidence: ''
  gate_id: gate-no-hr-raw-regression-2
  passed: true
- evidence: ''
  gate_id: gate-effect-size-gain
  passed: false
git_ref: ''
hypothesis_id: hyp-eiv-boost-categorical-slot-signal-20260805
id: exp-e23-arm1-unfreeze-projector-20260805
interpretation:
  at: '2026-08-18T21:08:40+00:00'
  failure_mode: mechanism_wrong
  lesson: 'Unfreezing the projector is a real win for the NUMERIC slots and a refutation
    of the categorical-conditioning story it was proposed for: every generation metric
    rose (bucket entropy 0.0->0.3703, peak_corr 0.1587->0.6036, between-sample std
    2.6->27.76, hr_raw corr full 0.604->0.740 / sparse 0.909->0.965), yet gate-entropy-recovered
    FAILED at 0.370 vs the 0.50 bar and gate-effect-size-gain FAILED on a sign inversion,
    with the full-coverage regime still 47/48 tachycardic (entropy 0.073) at n=12/label.
    The decisive control is the mechanism, not the metrics: re-running the paired
    swap with identical seed/samples/donors flipped swap_delta_mean +0.418 -> -1.128
    and null_delta_mean -1.750 -> +1.090, i.e. a norm-matched RANDOM gaussian prefix
    now scores the true label BETTER than the real encoder output, while the bucket
    dose-response died (r=+0.204 p=9.7e-05 -> r=-0.073 p=0.166, CI [-1.327,+0.187]
    now includes 0) and the hr_raw dose slope became significant instead (r=+0.113
    p=0.033, CI [+0.057,+0.502], Wilcoxon p=0.0053). Read this as signal REALLOCATION
    from the categorical to the numeric slot, keep the arm for hr_raw, and require
    every future arm to report null_delta SIGN and the dose slope — generation metrics
    alone would have scored this a success. Corollary that kills a planned method:
    teacher-forced swap-delta does not rank slots by generation quality (on e23v2
    the working hr_raw slot had the weaker, non-significant slope), so it cannot be
    used as a conditioning surrogate.'
  transfers_to:
  - hyp-eiv-label-consistent-sampler-20260805
  - the-hr-peak-failure-is-a-readout-965f58
  - hyp-e7-projector-bottleneck-20260814
  - hyp-e9-lmhead-lora-20260814
key_numbers: {}
observed_effects:
- delta: 0.3703
  diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: up
- delta: 0.4449
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: 25.16
  diagnostic_id: eiv_gen_between_sample_std
  direction: up
- delta: 0.4
  diagnostic_id: eiv_prefix_swap_bootstrap_p
  direction: up
- delta: 0.0
  diagnostic_id: eiv_training_runtime_sec
  direction: none
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_peak_corr: true
    eiv_gen_pred_bucket_entropy: true
provenance: {}
recipe_fingerprint: {}
verdict: directional_only
---

## Summary
Tests hypothesis [[hyp-eiv-boost-categorical-slot-signal-20260805]].

## Measurements
- [[eiv_gen_pred_bucket_entropy]]: before 0.0 → after 0.3703  (Δ +0.3703, up ✓ predicted up)
- [[eiv_gen_peak_corr]]: before 0.1587 → after 0.6036  (Δ +0.4449, up ✓ predicted up)
- [[eiv_gen_between_sample_std]]: before 2.6 → after 27.76  (Δ +25.16, up)
- [[eiv_prefix_swap_bootstrap_p]]: before 0.5 → after 0.9  (Δ +0.4, up)
- [[eiv_training_runtime_sec]]: before 2241.35 → after 2241.35  (Δ +0, flat)

## Gates
- `gate-entropy-recovered`: **FAIL**
- `gate-peak-corr-honest`: PASS
- `gate-no-hr-raw-regression-2`: PASS
- `gate-effect-size-gain`: **FAIL**

**Verdict:** directional_only

> Direction matched, but a preregistered blocking gate did NOT pass. This does not establish the claim.

## Interpretation
**Failure mode:** `mechanism_wrong`

Unfreezing the projector is a real win for the NUMERIC slots and a refutation of the categorical-conditioning story it was proposed for: every generation metric rose (bucket entropy 0.0->0.3703, peak_corr 0.1587->0.6036, between-sample std 2.6->27.76, hr_raw corr full 0.604->0.740 / sparse 0.909->0.965), yet gate-entropy-recovered FAILED at 0.370 vs the 0.50 bar and gate-effect-size-gain FAILED on a sign inversion, with the full-coverage regime still 47/48 tachycardic (entropy 0.073) at n=12/label. The decisive control is the mechanism, not the metrics: re-running the paired swap with identical seed/samples/donors flipped swap_delta_mean +0.418 -> -1.128 and null_delta_mean -1.750 -> +1.090, i.e. a norm-matched RANDOM gaussian prefix now scores the true label BETTER than the real encoder output, while the bucket dose-response died (r=+0.204 p=9.7e-05 -> r=-0.073 p=0.166, CI [-1.327,+0.187] now includes 0) and the hr_raw dose slope became significant instead (r=+0.113 p=0.033, CI [+0.057,+0.502], Wilcoxon p=0.0053). Read this as signal REALLOCATION from the categorical to the numeric slot, keep the arm for hr_raw, and require every future arm to report null_delta SIGN and the dose slope — generation metrics alone would have scored this a success. Corollary that kills a planned method: teacher-forced swap-delta does not rank slots by generation quality (on e23v2 the working hr_raw slot had the weaker, non-significant slope), so it cannot be used as a conditioning surrogate.

Transfers to: [[hyp-eiv-label-consistent-sampler-20260805]], [[the-hr-peak-failure-is-a-readout-965f58]], [[hyp-e7-projector-bottleneck-20260814]], [[hyp-e9-lmhead-lora-20260814]]

## Notes
ARM 1 (unfreeze projector) = REFUTED on its primary claim. 2/4 preregistered gates pass, and the two that fail are the ones that carry the hypothesis.

GATES: gate-peak-corr-honest PASS (+0.604 vs 0.30) | gate-no-hr-raw-regression-2 PASS (improved) | gate-entropy-recovered FAIL (0.370 vs 0.50) | gate-effect-size-gain FAIL (sign inversion, not a gain).

GENERATION METRICS ALL IMPROVED (label-stratified n=12/label, split at HR coverage 0.5):
  pred_bucket_entropy   ~0 (91/91 tachy) -> 0.370
  peak_corr             +0.159 -> +0.604
  peak pred_std         2.6 -> 27.8 sparse / 7.0 full
  hr_raw corr FULL      +0.604 -> +0.740 (MAE 6.9 -> 5.9)
  hr_raw corr SPARSE    +0.909 -> +0.965 (MAE 17.7 -> 8.7)
  bucket_acc            0.253 -> 0.354
Regime split: SPARSE entropy 0.537 (would pass alone), FULL 0.073 with 47/48 still tachycardic.

BUT THE MECHANISM IS NOT THE HYPOTHESISED ONE. E23M/E23N re-run on arm1 with identical seed, samples and donors as the baseline:
  swap_delta_mean  +0.418 -> -1.128   SIGN FLIP
  null_delta_mean  -1.750 -> +1.090   SIGN FLIP
  ratio            +0.082 -> -0.399
  bucket dose-response  r=+0.204 p=9.7e-05 -> r=-0.073 p=0.166 (flat, non-monotone: near -0.698, mid +0.037, far -1.030)
  bucket bootstrap CI   [+0.021,+0.605] excl. 0 -> [-1.327,+0.187] INCLUDES 0, Cohen d -0.191
  hr_raw dose-response  r=+0.073 p=0.165 -> r=+0.113 p=0.033 (now significant, monotone: +0.129/+0.237/+0.478)
  hr_raw bootstrap CI   -> [+0.057,+0.502] EXCLUDES 0, Wilcoxon p=0.0053

The two slots swapped roles. On the baseline the CATEGORICAL slot carried the significant HR-graded dose slope and hr_raw did not; on arm1 that is exactly reversed. Unfreezing the projector did not amplify categorical conditioning -- the evidence is consistent with it REALLOCATING per-sample sensor signal to the numeric slot, which is where every generation gain showed up.

MOST IMPORTANT SINGLE NUMBER: null_delta_mean is now POSITIVE (+1.090). A norm-matched random gaussian prefix scores the true label BETTER than the real encoder output. That is not a weak-conditioning regime, it is a MIS-SIGNED one, and it forbids attributing the entropy/peak_corr gains to improved sensor conditioning at the bucket slot. Improved generation metrics + inverted swap/null signs is the signature of the LM leaning on something other than per-sample sensor content.

BASELINE RESULT STANDS AND IS NOW BETTER SUPPORTED: on e23v2 the rescued +0.418 nat effect survived its own decisive control. 60 samples x 6 donors gave bucket delta +0.313, bootstrap 95% CI [+0.021,+0.605] (excludes 0; the old n=40 CI [-0.034,+0.855] did not), bootstrap one-sided p=0.0191, and a monotone dose-response r=+0.204 p=9.7e-05 (+0.078/+0.208/+0.642 by donor distance). So the categorical slot on e23v2 genuinely reads heart rate. Arm 1 destroyed that property while improving the surface metrics.

METHOD RESULT THAT INVALIDATES A PLANNED APPROACH: teacher-forced paired-swap delta does NOT rank slots by generation quality. On e23v2 the WORKING slot (hr_raw, generation corr +0.604/+0.909) had the weaker delta and a NON-significant dose slope, while the DEGENERATE bucket slot had the stronger, significant one. Measured per-slot gap 0.51x baseline / -0.50x arm1. Optimising swap-delta as a surrogate for generation conditioning is therefore unsound, and the 'calibrate against a known-working slot' step cannot supply the intended benchmark.

SCRIPT BUG FOUND AND FIXED: diag_e23M_effect_vs_prior.py computed required_gain_x = prior/max(effect,1e-6). With the effect negative the denominator clamped to 1e-6 and it printed '~2825027.1x gain required'. Pure divide-by-epsilon artifact, no meaning, must not be cited. Patched to return None unless the effect exceeds 1e-3.

CORRECTION TO MY OWN EARLIER ENTRY IN THIS EXPERIMENT: I recorded eiv_prefix_swap_bootstrap_p = 0.0191 before the arm1 measurement existed -- that value is the BASELINE's. Corrected to 0.9 (arm1's mean delta is negative, so the one-sided p for a positive effect is far above chance).

WHAT THIS MEANS FOR THE HYPOTHESIS: lever (c) is refuted as a route to categorical conditioning, though it is a genuine win for the numeric slots and should be kept for that. The signal-strength framing is NOT rescued by this arm; it survives only on the baseline evidence. Levers (a) bucket-token loss weight, (b) LoRA on LM head/final layers, (e) schema reorder remain untested. Any future arm MUST report null_delta sign and the dose slope, since generation metrics alone would have scored arm1 a success.

Artifacts: outputs/probes/arm1_gates.json, e23N_baseline.json, e23N_arm1.json, e23M_arm1.json, e23k_arm1_derived.json, logs/arm1_attnfix.log.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
