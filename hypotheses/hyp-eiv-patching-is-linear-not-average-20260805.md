---
created_at: '2026-08-05T16:49:16+00:00'
gates:
- blocking: true
  description: eiv_peak_excursion_increment_r2 on REAL trained encoder features must
    exceed +0.0090 (3x the recorded +0.0030 avg-pooled-raw figure) AND exceed the
    real-encoder avg-pooled incumbent increment by more than +0.01. PASS => within-patch
    information survives the learned linear projection, E1's upstream intervention
    is mis-aimed, do NOT spend the training run. FAIL => the encoder does discard
    within-patch extremes in practice, the recorded premise stands on outcome, E1
    STEP 2 proceeds.
  id: gate-peak-excursion-increment-real-encoder
- blocking: true
  description: 'SANITY/REIMPLEMENTATION CHECK: arm A (avg-pooled raw 24ch patches)
    must reproduce the recorded control R2(max | true mean alone) = 0.4371 to within
    +/-0.05, and n must be ~4010. If this fails the probe does not reproduce the prior
    measurement and NO conclusion may be drawn from any arm.'
  id: gate-reproduces-recorded-control
id: hyp-eiv-patching-is-linear-not-average-20260805
interventions:
- eiv-dual-avg-max-patching
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: 'On REAL trained encoder features the increment over the true-mean
    control will exceed +0.0090, i.e. more than 3x the +0.0030 recorded on avg-pooled
    raw patches. Rationale: nn.Linear(8 -> 192) is injective for generic weights,
    so within-patch structure is preserved rather than averaged away. If instead it
    lands at or below +0.0030, the prediction is REFUTED and the recorded ''destroyed
    at patch construction'' conclusion stands on outcome despite its stated mechanism
    (averaging) being factually wrong about the code.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
TemporalPatching applies a learned LINEAR map over the 8 raw timesteps, not an average, so within-patch peak information is not destroyed upstream as recorded.

The recorded premise that within-patch peak information is IRRECOVERABLY DESTROYED at patch construction is FALSE AS STATED, because TemporalPatching does not average. src/models/patching.py forward() does x.unfold(size=patch_len=8, step=stride=4) followed by nn.Linear(patch_len -> d_model) -- a LEARNED LINEAR MAP over the 8 raw timesteps, not a mean. A linear map from R^8 to R^192 is injective for generic weights, so the 8 raw timesteps are in principle recoverable from a single patch embedding; the projector's adaptive_avg_pool2d (cross_modal.py:260) then pools ACROSS patches (N->k), not within them. The prior probes that established 'increment +0.0030' used AVG-POOLED RAW PATCHES as a stand-in for the encoder, which builds the averaging assumption into the measurement rather than testing it.

PREDICTION (committed before running): probing the REAL trained TemporalEncoder (outputs/checkpoints/12ch_v1/phase_b) will show a peak-excursion increment MATERIALLY ABOVE the +0.0030 measured on avg-pooled raw patches. Concretely eiv_peak_excursion_increment_r2 on real encoder features will exceed +0.0090.

CONSEQUENCE IF CONFIRMED: the E1 intervention eiv-dual-avg-max-patching is aimed at the wrong stage -- adding max-pooled patches upstream cannot be the fix if upstream never lost the information, and the ~2241s training run should NOT be spent; the bottleneck would instead sit in the downstream avg-pooling readers which DO average across patches. CONSEQUENCE IF REFUTED: the encoder genuinely discards within-patch extremes despite the linear map, the recorded premise stands on its outcome even though its stated mechanism is wrong, and E1 STEP 2 is justified.

## Rationale
CODE EVIDENCE contradicting the recorded premise. The problem record states: 'TemporalPatching (src/encoders/temporal.py, patch_len=8 stride=4 over T=96 -> 23 patches) AVERAGES 8 timesteps = 2 hours; ... Within-patch extremes are destroyed at patch construction and no downstream reader recovers them.'

But src/models/patching.py forward() is:
    x = x.transpose(1, 2)
    patches = x.unfold(dimension=-1, size=self.patch_len, step=self.stride)   # [B, C, N, 8]
    projected = self.projection(patches)                                       # nn.Linear(8 -> d_model)
There is no mean over the patch axis. self.projection = nn.Linear(patch_len, d_model) maps R^8 -> R^192 and is injective for generic weights, so a single patch embedding can in principle carry all 8 raw timesteps including their max.

Where averaging DOES occur is strictly downstream and ACROSS patches, not within them: cross_modal.py:260 adaptive_avg_pool2d over (C, N->k), and correlation_head.py:107 x.mean(dim=2). Averaging across 23 patch embeddings is a different and much less destructive operation than averaging 8 raw timesteps, and the recorded slot-count sweep already showed it costs only ~0.009.

The prior probes that produced 'increment +0.0030' fed AVG-POOLED RAW PATCHES to the ridge, i.e. they measured the averaging assumption instead of testing it against the trained encoder. This hypothesis tests the premise directly. Note the scope correction already in the problem record ('average pooling CANNOT represent a max is too strong -- a nonlinear head could take a max ACROSS the 23 patch means') moves in the same direction; this goes further and disputes that within-patch averaging happens at all.

NOT A RETRY OF A DEAD END: ro-window-shortening (window length) and ro-sensor-slot-granularity (slot count) are both untouched here -- window stays 24h and slot count is not swept. This concerns the patch-construction operator itself.

## Plan
CPU-only RidgeCV probe, no GPU, ~5 min. Population identical to the recorded probes: clean fully-observed days, coverage>0.9, finite targets, true mean>20 bpm to exclude the zero cluster (expected n~4010). 70/30 split at seed 0, RidgeCV over 24 alphas logspace(-3,4), features standardised on train statistics.

Four arms, all same n / same split / same estimator:
  A raw24_avg      - avg-pool the raw 24ch (value+presence) input over each patch. Reproduces the recorded incumbent measurement.
  B raw24_avg+max  - concat avg AND max pooled raw patches. The intervention applied upstream.
  C encoder_real   - the ACTUAL trained TemporalEncoder output (input_dim=24, hidden 64, patch_len 8, stride 4, d_model 192), avg-pooled across patches as downstream readers do.
  D encoder_real avg+max - trained encoder, avg AND max pooled across patches. The intervention applied downstream.

MANDATORY CONTROL on every arm (never quote a bare R2->max): increment = R2(target | features) - R2(target | TRUE MEAN alone, single feature). Targets: hr max, and the pure excursion target (max - mean). Arm A must reproduce the recorded control R2(max|mean)=0.4371 as a correctness check.

Script: scratch/probe_e1_dual_patching.py, output outputs/probes/e1_dual_patching_gate.json.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Applies [[eiv-dual-avg-max-patching]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Gate `gate-peak-excursion-increment-real-encoder` (blocking): eiv_peak_excursion_increment_r2 on REAL trained encoder features must exceed +0.0090 (3x the recorded +0.0030 avg-pooled-raw figure) AND exceed the real-encoder avg-pooled incumbent increment by more than +0.01. PASS => within-patch information survives the learned linear projection, E1's upstream intervention is mis-aimed, do NOT spend the training run. FAIL => the encoder does discard within-patch extremes in practice, the recorded premise stands on outcome, E1 STEP 2 proceeds.
Gate `gate-reproduces-recorded-control` (blocking): SANITY/REIMPLEMENTATION CHECK: arm A (avg-pooled raw 24ch patches) must reproduce the recorded control R2(max | true mean alone) = 0.4371 to within +/-0.05, and n must be ~4010. If this fails the probe does not reproduce the prior measurement and NO conclusion may be drawn from any arm.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
