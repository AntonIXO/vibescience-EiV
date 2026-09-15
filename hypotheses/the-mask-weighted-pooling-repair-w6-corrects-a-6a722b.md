---
ambition_class: mechanism
created_at: '2026-08-29T21:56:53+00:00'
former_ids: []
gap_closure_estimate: 0.2
gates:
- blocking: true
  description: 'g1 BLOCKING (out-of-sample generalisation): on the HELD-OUT cohorts
    popane_s5 + popane_s6 alone, masked_avg''s variance-compression ratio must exceed
    avg''s by >= +0.05. MDE: W6''s recorded within-cohort deltas ranged +0.049 to
    +0.225 with a mean of +0.1267, so a >= +0.05 bar is comfortably inside what this
    instrument resolves. If the delta lands under +0.05 this is a POWERED NULL against
    out-of-sample generalisation of the repair.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g1-repair-generalises-to-unseen-cohorts
  mde: 0.05
- blocking: true
  description: 'g2 BLOCKING (no long-cohort harm): the LONG cohorts (lifesnaps, pmdata,
    big_ideas_glycemic, optihealth_supabase) must not lose more than 0.05 of ratio
    under masked_avg. masked_avg is arithmetically near-inert at high coverage, so
    a real regression here would mean the operator is doing something other than what
    W6 established.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g2-long-cohorts-no-harm
  mde: 0.05
- blocking: false
  description: 'g3 (non-blocking, calibration): do the SEEN short cohorts on this
    encoder reproduce W6''s qualitative pattern (all short cohorts improve)? W6 measured
    7/7 on a DIFFERENT encoder (ab_ctrl_s43) and a different corpus, so this is a
    consistency read, not a reproduction: absolute values are not comparable across
    encoder and corpus.'
  diagnostic_id: eiv_extent_variance_compression_ratio
  id: g3-seen-cohorts-consistent-with-w6
  mde: 0.05
id: the-mask-weighted-pooling-repair-w6-corrects-a-6a722b
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_extent_variance_compression_ratio
  direction: up
  magnitude_note: 'Primary, measured on popane_s5 + popane_s6 ONLY. W6''s seen-cohort
    short mean went 0.5630 -> 0.6897 (+0.1267) on a different encoder and corpus.
    I predict the held-out cohorts show the same sign with a delta of +0.08 to +0.15,
    and that their AVG-arm ratio starts LOW (0.45-0.65) because they are 27-34 measured-HR-minute
    sessions inside a 1440-epoch window -- the exact geometry the contraction identity
    describes. Calibration note: I am 2/5 (0.40) on this specific diagnostic and 0.411
    overall, so the blocking gates carry this, not the prediction.'
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
problem_tags:
- matched-baseline
- subject-heldout
- optihealth-eiv
- data-contract-violation
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- extent-heterogeneity
- pooling
- wearables
- control
- baseline
- denominator
- state-supervision
- deployment-distribution
---

## Statement
The mask-weighted pooling repair (W6) corrects a GEOMETRIC property of short recordings, not a memorised property of the specific cohorts in the training pool. Therefore it must generalise to short lab cohorts the encoder has never seen: on popane_s5 and popane_s6 -- absent from the 13,518-window corpus baseline_sync was trained on -- masked_avg raises the extent variance-compression ratio over avg by >= +0.05, while long free-living cohorts move < 0.05.

## Rationale
THE CORPUS REBUILD OF 2026-08-30 CREATED A TEST THAT WAS NOT PREVIOUSLY POSSIBLE. data/canonical went [13518] -> [13879] (+361 windows, merge git_sha 56bb0b5) by adding POPANE study 5 (199 windows / 199 subjects) and study 6 (140/140), plus dapper +19 and k_emocon +3. Measured extent of the new corpora: popane_s5 median 27 measured-HR minutes [17-38], popane_s6 median 34 [19-44]. Both are SHORT LAB SESSIONS inside the 1440-epoch window -- the exact population the contraction identity naive = median + c*(present - median) was derived on, and the short-cohort count goes 524 -> 863 windows (+65%).

WHY THIS IS A GENUINELY NEW MEASUREMENT AND NOT A REPEAT. Every recorded extent number (W4b 2.85% for CASE, W5 0.1822 -> 0.8159 frozen re-pool, W6 0.5630 -> 0.6897 end-to-end) was measured on cohorts that were IN the training corpus of the encoder doing the measuring. The available encoder baseline_sync/phase_b was trained on the 13,518 bundle, which does not contain popane_s5 or popane_s6 at all. So these two cohorts are OUT-OF-SAMPLE: if masked pooling is really correcting a geometric property of short extents, it must work on a cohort the encoder never saw. If instead W6's gain was partly the encoder having memorised the specific short cohorts in its training pool, it will shrink or vanish here. No previous run could separate those.

WHAT WOULD MAKE ME WRONG, stated up front: if the held-out delta is materially smaller than the seen-cohort delta measured on the SAME encoder in the same run, that is evidence the repair is partly cohort-specific rather than geometric -- and the seen/held-out contrast inside one run is the clean way to see it, because it removes the encoder and corpus as variables.

## Plan
CPU/GPU probe, no training, ~10-20 min. Adapt scratch/w6_primary_ratio.py into scratch/w12_extent_heldout.py with THREE changes and nothing else:
  (1) BCKPT = outputs/checkpoints/baseline_sync/phase_b (the encoder trained on 13,518, which excludes s5/s6).
  (2) cohort lists become DATA-DRIVEN: classify every dataset by median measured-HR minutes (< 240 = SHORT, else LONG) rather than the hardcoded 7-name list, and tag popane_s5/popane_s6 as HELD-OUT.
  (3) report per-cohort avg vs masked_avg ratio AND within-cohort r, exactly as W6 did.
Readout held identical to W5/W6 so the numbers mean the same thing: subject-grouped GroupKFold(5) RidgeCV on production-stage soft tokens (random-projected to 48 dims), predicting the window's PRESENT-ONLY mean HR in bpm; ratio = std(OOF pred)/std(truth) per cohort. Both arms share the identical seeded random projection so the only difference is patch_pool_mode + patch_presence.
WRITE logs/w12_extent_heldout.json. If g1 fails, record a rule-out against out-of-sample generalisation of the pooling repair and do NOT propose a variant.

## Links
Tests problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Predicts [[eiv_extent_variance_compression_ratio]] (up).
Gate `g1-repair-generalises-to-unseen-cohorts` (blocking): g1 BLOCKING (out-of-sample generalisation): on the HELD-OUT cohorts popane_s5 + popane_s6 alone, masked_avg's variance-compression ratio must exceed avg's by >= +0.05. MDE: W6's recorded within-cohort deltas ranged +0.049 to +0.225 with a mean of +0.1267, so a >= +0.05 bar is comfortably inside what this instrument resolves. If the delta lands under +0.05 this is a POWERED NULL against out-of-sample generalisation of the repair.
Gate `g2-long-cohorts-no-harm` (blocking): g2 BLOCKING (no long-cohort harm): the LONG cohorts (lifesnaps, pmdata, big_ideas_glycemic, optihealth_supabase) must not lose more than 0.05 of ratio under masked_avg. masked_avg is arithmetically near-inert at high coverage, so a real regression here would mean the operator is doing something other than what W6 established.
Gate `g3-seen-cohorts-consistent-with-w6` (advisory): g3 (non-blocking, calibration): do the SEEN short cohorts on this encoder reproduce W6's qualitative pattern (all short cohorts improve)? W6 measured 7/7 on a DIFFERENT encoder (ab_ctrl_s43) and a different corpus, so this is a consistency read, not a reproduction: absolute values are not comparable across encoder and corpus.

#extent-heterogeneity #pooling #wearables #control #baseline #denominator #state-supervision #deployment-distribution #matched-baseline #subject-heldout #optihealth-eiv #data-contract-violation
