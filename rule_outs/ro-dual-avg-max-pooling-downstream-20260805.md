---
created_at: '2026-08-05T17:51:01+00:00'
id: ro-dual-avg-max-pooling-downstream-20260805
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T17:51:01+00:00'
topic_tags:
- pooling
- cross-modal
- sensor-text
---

## Claim
The E1 premise: the DOWNSTREAM avg-pooling readers (cross_modal.py:260 adaptive_avg_pool2d, correlation_head.py:107 x.mean(dim=2)) destroy hr_peak information, and adding a max-pooled branch alongside the average (intervention eiv-dual-avg-max-patching) will materially raise eiv_peak_excursion_increment_r2 and is worth a ~2241s training run. ELIMINATED. Measured at the ACTUAL production pooling site the incumbent avg-pool is NOT peak-blind: it carries hr_peak at R2=0.8084 with increment +0.7211 over knowing the true mean. The max branch adds +0.0090 and the intervention is REFUTED as a lever. Also eliminated as a corollary: the recorded framing that peak information is 'irrecoverably destroyed' or 'already destroyed upstream at patch construction' -- it is present and linearly decodable at the exact tensor that becomes the soft-token prefix.

## Method
Three CPU probes, no GPU, on the real trained checkpoint outputs/checkpoints/12ch_v1/phase_b (encoder + channel_fusion + projector, log_scale forced to 1.0 as Phase C's projector_output_scale_override does).

KEY METHOD DIFFERENCE FROM STEP 1 (this is why the answer changed): the prior probe scratch/probe_e1_dual_patching.py pooled the RAW ENCODER output to ONE vector per channel (E.mean(dim=2)) and NEVER RAN THE PROJECTOR. Production differs in three ways that each matter: (1) pooling happens AFTER query_proj + cross_attention + RMSNorm (d_model 192 -> d_llm 2560), (2) it pools to k=4 slots PER CHANNEL (sensor_slots=96, C=24), not k=1, (3) the rmsnorm_scale_v3 tail re-normalises each slot to unit RMS, removing any magnitude a max-pool introduces. scratch/probe_e1_step2_sitegate.py replicates cross_modal.py:218-281 exactly and swaps ONLY the pooling operator; scratch/probe_e1_step2_deconfound.py sweeps k=1/2/4/8 at BOTH stages on two populations; scratch/probe_e1_step2_controls.py runs the falsification controls. RidgeCV (24 alphas), 70/30 split seed 0, features reduced by a fixed seeded random projection d_llm->48 to keep p/n sane. Every raw R2->max is reported with the mandated control R2(max | TRUE MEAN alone, 1 feature). A SECOND, INDEPENDENT DEFECT was found in the recorded probe population (see evidence section 2).

## Evidence
=== 1. SITE GATE (outputs/probes/e1_step2_sitegate.json, n=4010, the recorded population) ===
arm                                     R2->max  increment  R2->exc  exc incr
1 avg k=4  (INCUMBENT, 96 slots)         0.8966   +0.4067   0.8271   +0.6832
2 max k=4  (96 slots)                    0.8810   +0.3911   0.8045   +0.6606
3 avg k=2 + max k=2 (INTERVENTION)       0.9056   +0.4157   0.8411   +0.6973
4 avg k=4 + max k=4 (192 slots, 2x VRAM) 0.8925   +0.4026   0.8206   +0.6767
GATE bar: increment > +0.0090 AND > incumbent + 0.01. Observed delta = +0.0090. FAIL.
The intervention is within noise of the incumbent, and the DOUBLE-WIDTH arm 4 is WORSE than arm 3 -- no monotone benefit to adding max capacity. Max-only (arm 2) is WORSE than avg-only (arm 1), the opposite of the premise's prediction.

=== 2. THE RECORDED PROBE POPULATION IS ZERO-CONTAMINATED (outputs/probes/e1_step2_deconfound.json) ===
The filter used by every prior probe in this problem (coverage>0.9 on the MASK, mean>20 bpm) does NOT remove days that are partly zero-valued, because M_mask marks zeros as PRESENT (problem canonical-mask-marks-degenerate-zero-channels-as-present-20260805).
  n=4010; samples with >=1 zero-bpm 'present' step: 2369 (59.1%)
  mean zero fraction within present steps: 0.334
  population hr_mean 44.7 bpm  <- not physiology; a real day is ~70
  'excursion' (max-mean) 57.8 bpm <- inflated; the clean value is 50.4
  corr(zero_fraction, excursion) = +0.3848
So on the recorded population 'excursion' is partly 'what fraction of this day is zero', which any pooling reads off trivially. STRICT clean population (no zero-bpm steps at all, mean>40): n=1641, hr_mean 69.9 +- 8.2, hr_max 120.3 +- 23.1, excursion 50.4 +- 22.2 -- physiological.

=== 3. STAGE x k SWEEP, BOTH POPULATIONS (the decisive contrast) ===
STRICT CLEAN n=1641, control R2(max|true mean) = 0.0873, increments:
  stage        k=1      k=2      k=4      k=8
  encoder    +0.0077  +0.1430  +0.1694  +0.1386
  PRODUCTION +0.7157  +0.7140  +0.7211  +0.7085
CONTAMINATED n=4010, control 0.4899:
  encoder    -0.0269  +0.0424  +0.0751  +0.0674
  PRODUCTION +0.4019  +0.4059  +0.4067  +0.4024
TWO CONCLUSIONS. (a) k (slot count) is NOT the variable: it moves the increment by <0.02 at either stage, INDEPENDENTLY CONFIRMING rule-out ro-sensor-slot-granularity-20260805 on a different population and a different metric. (b) THE STAGE IS THE VARIABLE: the projector (query_proj + cross-attention + RMSNorm) raises the increment from +0.1694 to +0.7211 at matched k=4 on the clean population -- 4.3x. Step 1's -0.0482 was measured at the encoder stage at k=1, the single worst cell in this table; it is not evidence about the production representation.

=== 4. FALSIFICATION CONTROLS (outputs/probes/e1_step2_controls.json, strict clean n=1641, train 1148 / test 493) ===
arm                                R2->max  increment  R2->exc  exc incr
TRAINED (incumbent production)      0.8084   +0.7211   0.7926   +0.7912
TRAINED, presence channels zeroed   0.8135   +0.7261   0.7986   +0.7972
RANDOM-INIT weights                 0.6802   +0.5929   0.6521   +0.6507
SHUFFLED TARGET                    -0.0315      --        --       --
  C1 overfitting: shuffled target R2 = -0.0315 => CLEAN; the split is not leaking despite p=4608 > n_train=1148.
  C2 training: trained +0.7211 vs random-init +0.5929 => training contributes +0.128, it MATTERS here. (Contrast Step 1, where random-init MATCHED trained 0.9139 vs 0.9061 -- that arm measured a near-tautological property of max being associative; this one does not.)
  C3 shortcut: zeroing the 12 presence channels does NOT degrade it (+0.7261 vs +0.7211) => the peak is read from the VALUE channels, not the missingness pattern.

=== CONSEQUENCE ===
The representation is not the bottleneck for hr_peak, so no pooling-operator change can be the fix. Combined with the E0 finding that hr_peak has NEGATIVE generation skill vs an always-predict-median constant (-5.88 bpm FULL, -6.48 bpm clean SPARSE), the entire gap is between a representation that HAS the peak at R2=0.81 and a generation path that cannot USE it -- the 'probe R2 proves presence, never usability' pitfall. The next arm must target the READOUT, not the representation.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

#pooling #cross-modal #sensor-text
