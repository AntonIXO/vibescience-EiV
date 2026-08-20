---
ambition_class: null
created_at: '2026-08-20T21:14:38+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: STAGE MEASUREMENT IS BLOCKING AND MUST BE REPORTED BEFORE ANY OTHER
    CLAIM. Report eiv_extent_variance_compression_ratio at THREE stages on identical
    rows -- (a) naive 1440-mean (reference, already measured), (b) trained-encoder
    patch tokens pooled as the code pools them, (c) production post-projector soft
    tokens -- for each of the 7 short cohorts and 4 long cohorts, each with its mean
    coverage c alongside. If the encoder-stage ratio on the short cohorts is >= 0.5
    (i.e. the encoder ALREADY largely preserves spread), the contraction does not
    survive to the representation the model actually uses, this hypothesis is REFUTED,
    and no pooling/crop/conditioning GPU arm is funded on contraction grounds.
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g1-three-stage-compression-measured
  mde: 0.5
- blocking: true
  description: RANDOM-INIT CONTROL AT MATCHED GEOMETRY. Run the identical stage-(b)
    measurement through a RANDOM-INIT encoder. Contraction from unweighted pooling
    is an architectural property and must appear in the untrained model too. If trained
    and random-init ratios differ by more than 0.15 on the short cohorts, the effect
    is a property of the LEARNED WEIGHTS rather than of the pooling geometry, the
    mechanism stated here is wrong, and the prescribed fix (mask-weighted / cardinality-invariant
    pooling) does not follow.
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g2-random-init-control
  mde: 0.15
- blocking: true
  description: HEADROOM GATE. On the short cohorts, a mask-weighted pool applied to
    the SAME frozen encoder tokens must lift the ratio to >= 0.60 (from a measured
    base of 0.019-0.28). This is a pure post-hoc re-pooling of already-computed tokens,
    zero training. If masked re-pooling cannot restore spread from the existing tokens,
    the information was destroyed at patch construction rather than at pooling, the
    pooling-site fix is dead, and the honest next object is the encoder input path
    -- not a pooling arm.
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g3-masked-repool-headroom
  mde: 0.6
- blocking: false
  description: SHUFFLE / WIDTH CONTROL. Report the same ratio computed on row-shuffled
    targets and confirm it does not manufacture apparent spread (expected ~0 change).
    Guards against the ratio being an artefact of the readout rather than of the representation.
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g4-shuffle-control
  mde: 0.05
id: h-w5-contraction-survives-into-the-91ad0b
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
  magnitude_note: Short cohorts move from a measured 0.019-0.28 at the naive stage
    to >=0.60 under mask-weighted re-pooling of the SAME frozen tokens; the encoder
    stage itself is predicted to sit BELOW 0.5 on short cohorts (i.e. contraction
    survives into the representation).
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags:
- extent-heterogeneity
- pooling
- padding
- wearables
- denominator
- state-supervision
- deployment-distribution
---

## Statement
H-W5 (CONTRACTION SURVIVES INTO THE REPRESENTATION, AND MASKED RE-POOLING RECOVERS IT). This is the blocking CPU-only gate for the whole extent family; NO GPU arm is justified until it passes.

CLAIM. The (1-c) contraction measured on the naive 1440-mean (W4b: case ratio 0.0285 = c to four decimals, popane_s4 0.0189, wesad 0.0825, vs lifesnaps 0.6992 / pmdata 0.8567) is NOT an artefact of the naive mean being a strawman. It survives through the encoder's own patch geometry into the pooled representation the projector consumes, because every pooling operator on the path -- TemporalPatching's fixed 23-token grid, cross_modal.py:323 adaptive_avg_pool2d, correlation_head.py x.mean(dim=2) -- is PRESENCE-BLIND. cross_modal.py:260-270 states this in the source: the code comment records that presence-weighting was considered and deliberately NOT done. So the short cohorts arrive at the LM contracted onto the corpus median, and no downstream objective can separate subjects that have been mapped onto the same point.

MECHANISM, STATED SO IT CAN BE WRONG. The encoder does not compute the naive 1440-mean; it patches at patch_len=120 / stride=60 into 23 tokens. For a 41-minute CASE session roughly 1 of 23 patches contains any measurement, so the predicted encoder-stage contraction is nearer n_real_patches/23 (~0.04-0.09) than c itself -- similar in magnitude, different in derivation. The prediction is therefore SHARP and falsifiable in both directions: if the encoder stage reads >=0.5 the claim is dead, and if it reads ~c exactly then the patch grid adds nothing beyond the naive mean.

WHY THIS IS NOT A BANNED RETRY -- three near-neighbours, each scoped away explicitly.
(1) `ro-window-shortening-20260805` and `shortening-the-analysis-window-to-align-with-0f6d56` both kill SHORTENING W. Nothing here changes W. The window stays 1440 and the patch grid stays 23 tokens; only the WEIGHTING of those tokens changes. The refuted mechanism there was an order-statistic argument (max converges to mean in a short window); a variance ratio has no such convergence.
(2) `ro-dual-avg-max-pooling-downstream-20260805` and `ro-avgmax-pooling-production-site-20260815` kill avg->avgmax/max at the production site (delta +0.0090 / gap +0.0235). Those swap WHICH ORDER STATISTIC is pooled while remaining presence-blind; every one of avg/max/avgmax contracts identically toward the fill value. The intervention here is orthogonal: keep the operator, change the DENOMINATOR.
(3) `that-the-encoder-s-23-token-patch-grid-is-under-bc9aef` kills finer grids. The grid is held FIXED at 120m/23tok here.
Also distinct from `e11-s-supabase-transfer-failure-is-5472c8`, which intervened at the HEAD on frozen L34 features at coverage ~U[0.57,0.95] -- it could not restore spread destroyed upstream, and it never touched the c=0.019-0.18 regime.

WHY THE OBVIOUS INSTRUMENT WOULD HAVE MISSED THIS, recorded because it already cost me a failed prediction: the mean gap |naive-present| was preregistered at >10 bpm (W4/P4) and FAILED 2 of 4 (wesad 5.8, case 4.9) purely because those cohorts' true means sit on the corpus median (74.7 and 71.5 vs 73.0). Cohorts near the median are invisible to a mean-based instrument while being maximally compressed. Any future extent claim must be scored on SPREAD.

PRIOR ART (verified arXiv ids 2026-08-20): ElasticAST arXiv:2407.08691 replaces CLS with mask attention pooling precisely so packed padding never enters the pool, and documents that fixed-length models COLLAPSE off their training length; Set Transformer PMA arXiv:1810.00825 gives cardinality-invariant pooling; LSM-2/AIM arXiv:2506.05321 (40M hours of day-long wearable data) uses learnable mask tokens rather than imputation. The literature treats presence-blind mean pooling as a hygiene BUG, not a design choice. No published work names this failure for median-filled wearable tensors or quantifies it -- the variance-compression framing appears to be ours.

## Rationale


## Plan


## Links
Tests problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Predicts [[eiv_extent_variance_compression_ratio]] (up).
Gate `g1-three-stage-compression-measured` (blocking): STAGE MEASUREMENT IS BLOCKING AND MUST BE REPORTED BEFORE ANY OTHER CLAIM. Report eiv_extent_variance_compression_ratio at THREE stages on identical rows -- (a) naive 1440-mean (reference, already measured), (b) trained-encoder patch tokens pooled as the code pools them, (c) production post-projector soft tokens -- for each of the 7 short cohorts and 4 long cohorts, each with its mean coverage c alongside. If the encoder-stage ratio on the short cohorts is >= 0.5 (i.e. the encoder ALREADY largely preserves spread), the contraction does not survive to the representation the model actually uses, this hypothesis is REFUTED, and no pooling/crop/conditioning GPU arm is funded on contraction grounds.
Gate `g2-random-init-control` (blocking): RANDOM-INIT CONTROL AT MATCHED GEOMETRY. Run the identical stage-(b) measurement through a RANDOM-INIT encoder. Contraction from unweighted pooling is an architectural property and must appear in the untrained model too. If trained and random-init ratios differ by more than 0.15 on the short cohorts, the effect is a property of the LEARNED WEIGHTS rather than of the pooling geometry, the mechanism stated here is wrong, and the prescribed fix (mask-weighted / cardinality-invariant pooling) does not follow.
Gate `g3-masked-repool-headroom` (blocking): HEADROOM GATE. On the short cohorts, a mask-weighted pool applied to the SAME frozen encoder tokens must lift the ratio to >= 0.60 (from a measured base of 0.019-0.28). This is a pure post-hoc re-pooling of already-computed tokens, zero training. If masked re-pooling cannot restore spread from the existing tokens, the information was destroyed at patch construction rather than at pooling, the pooling-site fix is dead, and the honest next object is the encoder input path -- not a pooling arm.
Gate `g4-shuffle-control` (advisory): SHUFFLE / WIDTH CONTROL. Report the same ratio computed on row-shuffled targets and confirm it does not manufacture apparent spread (expected ~0 change). Guards against the ratio being an artefact of the readout rather than of the representation.

#extent-heterogeneity #pooling #padding #wearables #denominator #state-supervision #deployment-distribution #optihealth-eiv #data-contract-violation #matched-baseline
