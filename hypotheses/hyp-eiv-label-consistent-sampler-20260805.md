---
created_at: '2026-08-05T09:29:20+00:00'
gates:
- blocking: true
  description: 'eiv_train_label_marginal_max <= 0.35. The mechanism gate: no single
    HR label may exceed 35% of the sampler-weighted training marginal. Measured on
    the production get_sampler_weights() path before training. Old value 0.500, post-fix
    measured 0.270.'
  id: gate-marginal-not-saturated
- blocking: true
  description: eiv_gen_pred_bucket_entropy >= 0.50 (normalized). Predictions must
    be spread across buckets rather than pinned to one. The prior run was ~0 (every
    true class -> tachycardic). This is the gate that directly falsifies the saturated-prior
    story.
  id: gate-prediction-entropy
- blocking: true
  description: 'eiv_gen_peak_corr >= 0.30 overall, reported SPLIT BY HR COVERAGE AT
    0.5 (never on cand_full alone, per the E22 lesson). Prior run: -0.013 overall.'
  id: gate-peak-corr-positive
- blocking: false
  description: hr_raw generation correlation must not regress below +0.60 full-coverage
    (E22 fresh-adapter baseline was +0.795). Guards against fixing hr_peak by breaking
    what already worked.
  id: gate-no-hr-raw-regression
- blocking: false
  description: P2 ablation output_diff_rate_zero and _random stay >= 0.90 (E22 measured
    1.00 both). Confirms the prefix remains causally live after the sampler change.
  id: gate-p2-conditioning-preserved
id: hyp-eiv-label-consistent-sampler-20260805
interventions:
- eiv-label-consistent-sampler
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: up
  magnitude_note: PRIMARY. From ~0 (every true class -> tachycardic, a single occupied
    bucket) to >= 0.5 normalized. This diagnostic was registered specifically to catch
    the saturated-prior failure that correlation and output-spread both miss, so it
    is the sharpest test of the claim.
- diagnostic_id: eiv_train_label_marginal_max
  direction: down
  magnitude_note: 0.500 -> 0.270, already measured on the real corpus via the production
    code path before any GPU time. This is the intervention's mechanism, not a hoped-for
    outcome.
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: -0.013 -> >= +0.30 overall. Predictions were pinned to [120,161];
    freeing the marginal should let generated peak track truth.
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- training-instability
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The Phase C all-tachycardic hr_peak collapse is not late-training instability, not over-correction on a 'peak-upweighted sampler', and not a conditioning failure. It is a partition mismatch between the stratified sampler and the training target. get_sampler_weights inverse-frequency balanced a 4-way split of the peak axis (<100 / 100-120 / 120-140 / >=140), while _build_json_slots_target collapses everything >=120 into a SINGLE 'tachycardic' label. The sampler's top two bins therefore both map onto that one label and it collected 2/4 of the sampler mass: measured 50.00% tachycardic against a 17.10% natural corpus rate on the live lifesnaps+pmdata filter. 'Always answer tachycardic' is then a genuinely loss-minimising strategy on the training marginal, which explains the observed signature exactly: overall peak corr -0.013, predictions pinned to [120,161], every true class mapping to tachy in the confusion matrix, and per-cell accuracy 1.00 on high140+/tachy arising from saturation rather than skill. Routing both call sites through one shared hr_label_bucket() rule, with balancing softened to strength 0.5, will restore a non-degenerate predicted-bucket distribution and positive peak correlation WITHOUT sacrificing the hr_raw conditioning the E22 channel-mask fix established.

## Rationale
Measured, not inferred. scratch/diag_peak_target_dist.py reconstructs the effective training marginal from the production code path on the real corpus: natural 28.95/25.18/28.78/17.10 (brady/resting/elevated/tachy) becomes 13.37/11.63/25.00/50.00 under the old sampler. The two peak bins >=120 and >=140 hold 933 and 567 samples and each receives an inverse-frequency weight, but they are textually indistinguishable in the target, so the single tachycardic label absorbs both shares. hr_augment compounds it mildly to 56.23% because the augmenter shifts the daily MEAN to U(40,135) while the bucket is defined on the PEAK, which rides along at mean + ~32 bpm mean spread. This is a sufficient explanation for a saturated prior and needs no appeal to optimizer instability. The competing 'late-training instability / over-corrected on the peak-upweighted sampler' story is not discriminating: probe_rmse_norm drift and a late loss spike are equally consistent with a model converging onto a degenerate but loss-optimal constant answer. Note this is a genuinely independent bug from the E22 channel-mask inversion: E22 was a train/eval inconsistency at inference, this is a sampler/target inconsistency at training time, and E22's fix is what made this one visible.

## Plan
1. DONE pre-run: measure the effective marginal on the real corpus through the production path (scratch/diag_peak_target_dist.py) -> old sampler 13.37/11.63/25.00/50.00 vs natural 28.95/25.18/28.78/17.10. 2. DONE: unify the bucket rule (datasets.hr_label_bucket), route both the target and get_sampler_weights through it, add sampler_balance_strength/sampler_weight_cap, wire through pipeline.py + cli_train.py, log the realised marginal before step 0. 3. DONE: 11 regression tests in tests/test_hr_bucket_marginal.py lock sampler/target partition agreement; full suite 233 passed with the same 23 pre-existing failures. 4. DONE: verify on real corpus (scratch/diag_e23_sampler_fix.py) -> strength 0.5 gives 27.04/25.22/26.96/20.78, and a 6-step smoke confirmed the trainer logs tachycardic 0.2078. 5. RUNNING: profile lora_proxy_12ch_e23, 2600 steps, frozen encoder, E22-corrected inference mask from step 0. 6. Then: full P1-P7 probe plus generation-level hr_peak/hr_raw evaluation SPLIT BY HR COVERAGE AT 0.5, and score the preregistered gates.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Applies [[eiv-label-consistent-sampler]].
Predicts [[eiv_gen_pred_bucket_entropy]] (up).
Predicts [[eiv_train_label_marginal_max]] (down).
Predicts [[eiv_gen_peak_corr]] (up).
Gate `gate-marginal-not-saturated` (blocking): eiv_train_label_marginal_max <= 0.35. The mechanism gate: no single HR label may exceed 35% of the sampler-weighted training marginal. Measured on the production get_sampler_weights() path before training. Old value 0.500, post-fix measured 0.270.
Gate `gate-prediction-entropy` (blocking): eiv_gen_pred_bucket_entropy >= 0.50 (normalized). Predictions must be spread across buckets rather than pinned to one. The prior run was ~0 (every true class -> tachycardic). This is the gate that directly falsifies the saturated-prior story.
Gate `gate-peak-corr-positive` (blocking): eiv_gen_peak_corr >= 0.30 overall, reported SPLIT BY HR COVERAGE AT 0.5 (never on cand_full alone, per the E22 lesson). Prior run: -0.013 overall.
Gate `gate-no-hr-raw-regression` (advisory): hr_raw generation correlation must not regress below +0.60 full-coverage (E22 fresh-adapter baseline was +0.795). Guards against fixing hr_peak by breaking what already worked.
Gate `gate-p2-conditioning-preserved` (advisory): P2 ablation output_diff_rate_zero and _random stay >= 0.90 (E22 measured 1.00 both). Confirms the prefix remains causally live after the sampler change.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #training-instability
