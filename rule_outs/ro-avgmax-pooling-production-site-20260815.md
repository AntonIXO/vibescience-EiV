---
created_at: '2026-08-15T10:04:05+00:00'
former_ids: []
id: ro-avgmax-pooling-production-site-20260815
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-15T10:04:05+00:00'
topic_tags:
- pooling
- cross-modal
- control
---

## Claim
The rb-w4 premise: swapping cross_modal.py's cross-patch pooling from avg to avgmax (or max) is the largest encoder-side lever on record, worth ~+0.40 R2 on the daily-HR-excursion increment, and justifies an end-to-end Phase A/B/C GPU arm at >=3 seeds. ELIMINATED. The +0.4042 effect was measured at the WRONG SITE. exp-e19/e19b pooled the TEMPORAL ENCODER's patch tokens [B,C,23,d_model=192]; cross_modal.py pools the POST-CROSS-ATTENTION soft tokens, after query_proj + 128-prototype cross-attention has mapped 192 -> d_llm=2560. Re-measured at the real production site the gap is +0.0235 (v5c) / +0.0447 (v5d), a 9.1x shrinkage, below this project's +0.05 decision bar on both checkpoints. Also eliminated as a corollary: the mechanism story that 'avg-pooling discards the within-day extremum the encoder computed' -- at the production site avg ALREADY carries +0.48 of increment (vs +0.2807 at the encoder site), because the cross-attention recovers the excursion before pooling happens; all three operators land within 0.045 of a ~+0.51 ceiling. This independently reconfirms ro-dual-avg-max-pooling-downstream-20260805, which eliminated the same intervention at delta +0.0090.

## Method
Four CPU/GPU probes on REAL trained checkpoints, no training. All weights loaded with an explicit refusal-to-proceed if any key is missing.

scratch/e19c_production_site_pooling.py — rebuilds the exact production stack (TemporalEncoder + ChannelFusionTransformer + CrossModalProjector with sensor_slots=96, n_input_channels=24, rmsnorm_scale_v3), reproduces the production channel_mask construction from pipeline.py:3395-3399, and reads the excursion target off the projector's OWN OUTPUT under each patch_pool_mode. Dimension confound controlled: soft tokens are [B,96,2560]=245760 features, so every arm passes through the SAME fixed seeded Gaussian random projection to 4608 dims (= E19's arm width); `max` is additionally an exact equal-slot comparator for `avg`. RidgeCV(13 alphas), GroupKFold(5) BY SUBJECT, scored as increment over R2(excursion | true daily mean) per the eiv_peak_excursion_increment_r2 contract. Shuffle control per arm.

scratch/e19e_site_control.py — THE DECISIVE CONTROL. Measures BOTH sites in ONE script with the same pool, same folds, same RidgeCV, same checkpoint (v5d/phase_b, the one E19 used), varying ONLY where pooling is applied. This is what separates 'the site differs' from 'the new harness is broken'.

scratch/e19d_mechanism_and_replication.py — tests the RMS-renorm hypothesis by surgically disabling the post-pool per-token RMS renormalisation (moving projector_format_version out of the log-scale family, leaving weights and pooling untouched), and replicates on two checkpoints.

scratch/e19f_strict_clean_production_site.py — re-runs on the STRICT CLEAN population (no zero-bpm present step, mean>40 bpm) to defuse the zero-contamination defect that ro-dual-avg-max-pooling-downstream-20260805 documented in this exact filter.

## Evidence
=== SITE CONTROL, one script, one pool, one checkpoint (v5d/phase_b), n=4000, 77 subjects, control R2 +0.0649 (logs/e19e_site_control_n4000.json) ===
                       avg      max      avgmax   gap(avg->avgmax)
  encoder-token site  +0.2807  +0.6771  +0.6849      +0.4041
  production site     +0.4639  +0.4980  +0.5083      +0.0443
The encoder-site arm reproduces E19's PUBLISHED numbers to four decimals (+0.2807/+0.6771/+0.6849, gap +0.4041 vs published +0.4042). The harness is therefore validated against the prior result, and the 9.1x shrinkage is a property of the site, not the instrument.

=== STRICT-CLEAN POPULATION, n=4254, 77 subjects, control R2 +0.0570 (logs/e19f_strict_clean_production_site.json) ===
  v5c/phase_b (the init the BASELINE recipe uses):
      avg +0.4880   max +0.5125   avgmax +0.5116   gap +0.0235   FAIL vs +0.05 bar
  v5d/phase_b (the checkpoint E19 used):
      avg +0.4765   max +0.5113   avgmax +0.5212   gap +0.0447   FAIL vs +0.05 bar
  Shuffle controls dead on every arm: -0.0995 .. -0.1577.
CONTAMINATION THREAT IS MOOT ON THE CURRENT CORPUS: the E19 filter and the strict filter now select the SAME 4254 days, 0 carrying a zero-bpm present step, pool hr_mean 74.4 +- 8.2 bpm (the 2026-08-05 record had 59.1% contaminated days and hr_mean 44.7). The M_mask defect was repaired in a later rebuild.

=== MECHANISM: THE RMS RENORM IS NOT THE BLOCKER (logs/e19d_mechanism_and_replication_n4000.json) ===
Re-ran every arm with the post-pool per-token RMS renormalisation surgically disabled:
  v5c  with_rms +0.0252 -> rms_DISABLED +0.0263   (unchanged)
  v5d  with_rms +0.0443 -> rms_DISABLED +0.0443   (identical)
So the magnitude-destroying-normaliser hypothesis is REFUTED. The real reason is visible in the avg column: cross-attention lifts avg from +0.2807 (encoder site) to +0.4639-+0.4880 (production site). E19 measured headroom that is already consumed by the time pooling runs.

=== TWO STRUCTURAL FACTS THAT MAKE THE RUNBOOK AS WRITTEN UNRUNNABLE (verified by direct execution) ===
1. patch_pool_mode IS NOT PLUMBED: it appears only in src/projectors/cross_modal.py and tests/test_patch_pool_mode.py. It is absent from configs/training.yaml, src/cli_train.py and src/training/pipeline.py. Setting it in a profile today silently trains 'avg' and would have produced a FAKE NULL.
2. IT IS A NO-OP IN PHASE A/B: those sites construct CrossModalProjector WITHOUT sensor_slots (pipeline.py:1376, :2324), and the pooling block is gated on sensor_slots being set. Executed proof: in the Phase A/B construction avg vs avgmax outputs are bit-identical (max|diff| = 0.000e+00); in the Phase C construction they differ (max|diff| = 5.755). Phase C — the only phase where the operator acts — FREEZES the projector (pipeline.py:3133-3134), so it could never adapt to a new operator. 'Phase A/B/C with avgmax, >=3 seeds' was not a runnable plan.

COST AVOIDED: ~12 h GPU (6 runs) on an arm whose lever is 9x smaller than recorded and under the decision bar.

## Links
Rules out for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].

#pooling #cross-modal #control
