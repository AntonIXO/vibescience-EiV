---
created_at: '2026-08-14T22:28:04+00:00'
former_ids: []
gates:
- blocking: true
  description: PRIMARY. On the REAL trained Phase-B encoder's own patch tokens, the
    best non-avg cross-patch pooling operator must exceed avg pooling on eiv_peak_excursion_increment_r2
    by >= +0.05, same rows, same subject-grouped split, same control R2(excursion
    | true daily mean). MDE 0.05 inherited from E17. A gap under +0.05 means the encoder
    has already discarded the extremum (the InfoNCE-on-caption-means failure mode)
    and no downstream pooling change can recover it — which would REFUTE the transfer
    and stop the projector change.
  diagnostic_id: eiv_peak_excursion_increment_r2
  id: g1_encoder_operator_beats_avg
  mde: 0.05
- blocking: false
  description: 'TRANSFER DIRECTION. Informational: is the encoder-token gap larger
    or smaller than the raw-patch gap E17 measured (+0.3515)? A LARGER gap means the
    encoder concentrates excursion information that avg-pooling then discards, making
    the projector fix more valuable than the raw-patch bound suggested. A SMALLER
    gap means partial destruction upstream and caps the achievable gain.'
  diagnostic_id: eiv_peak_excursion_increment_r2
  id: g2_gap_vs_raw_patch_bound
  mde: null
id: hyp-e19-encoder-pooling-transfer-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: On the real trained encoder's patch tokens, replacing avg cross-patch
    pooling with max/avgmax/lse raises the excursion increment by >= +0.05 over avg.
    E17's raw-patch bound was avg +0.0090 -> avgmax +0.3605 (+0.3515). The honest
    risk is that InfoNCE trained against captions made of six channel MEANS has already
    destroyed the within-window extremum, in which case the encoder-token gap collapses
    toward zero and the projector fix is NOT justified.
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
status: confirmed
supersedes: null
topic_tags:
- pooling
- cross-modal
- epoch-resolution
- control
- dataset-whitelist
- wearables
- baseline
---

## Statement
H-E19 (encoder-token transfer). The E17 pooling result was measured on RAW canonical patches and therefore establishes only an information BOUND. This arm asks whether it transfers to the REAL TRAINED Phase-B encoder: do the encoder's own patch tokens still carry the within-day excursion, such that swapping the cross-patch pooling operator (avg -> max / avgmax / LSE) recovers it?

PREDICTION: on encoder tokens, the best non-avg operator beats avg pooling on eiv_peak_excursion_increment_r2 by >= +0.05, on the same rows and the same subject-grouped split.

IF CONFIRMED: cross_modal.py's adaptive_avg_pool2d is actively destroying information the encoder already computed, and changing that operator is a targeted, cheap, high-value fix.
IF REFUTED: InfoNCE against mean-only captions has already destroyed the extremum upstream, no downstream pooling change can recover it, and the lever moves to the Phase-A objective / caption content instead. Either outcome redirects the next GPU spend.

## Rationale
E17 established an INFORMATION BOUND on raw canonical patches, not a property of the trained encoder. Those are different claims, and this project has been burned by exactly that conflation before: exp-e31 showed a linear probe recovering signal after projection does not establish the LM can USE it, and the H4b mechanism note argues contrastive alignment discards whatever is not shared with the text view — the captions being six channel MEANS, spread is precisely what InfoNCE had no reason to preserve.

So before recommending a change to cross_modal.py, the question must be asked of the actual trained weights: do the encoder's own patch tokens still carry the within-day excursion, and is avg-pooling across patches what discards it? A null here would be as informative as a pass — it would redirect the lever from the projector to the Phase-A objective/caption content.

## Plan
scratch/e19_encoder_pooling.py — GPU (RTX 5070), runs in parallel with the CPU N-of-1 arm.

Load the REAL Phase-B temporal_encoder from outputs/checkpoints/v5d/phase_b/sensor_modules/temporal_encoder.pt (encoder_config: input_dim=24, d_model=192, patch_len=120, stride=60 -> 23 tokens/channel, hidden_dim=64). Refuse to proceed if any weight fails to load (strict check on missing keys) — a silently partially-initialised encoder would manufacture a result.

Input assembly matches encoder_format_version with_presence_v1: concatenate the 12 canonical channels with their 12 presence flags to reach input_dim=24.

SAME clean pool and SAME filter as E17 task_a (lifesnaps+pmdata, HR coverage>0.9, mean>20 bpm, peak>=40, n<=4000, seed 0) so the two runs are directly comparable. SAME control: R2(excursion | true daily mean, 1 feature), subject-grouped GroupKFold(5), RidgeCV.

Pool ACROSS the patch axis N (exactly where cross_modal.py:260 pools) under four operators: avg (incumbent), max, avgmax concat, lse. Score every arm as increment over the mean-only control; raw R2 is inadmissible per the diagnostic contract.

Output: logs/e19_encoder_pooling.json

## Links
Tests problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Gate `g1_encoder_operator_beats_avg` (blocking): PRIMARY. On the REAL trained Phase-B encoder's own patch tokens, the best non-avg cross-patch pooling operator must exceed avg pooling on eiv_peak_excursion_increment_r2 by >= +0.05, same rows, same subject-grouped split, same control R2(excursion | true daily mean). MDE 0.05 inherited from E17. A gap under +0.05 means the encoder has already discarded the extremum (the InfoNCE-on-caption-means failure mode) and no downstream pooling change can recover it — which would REFUTE the transfer and stop the projector change.
Gate `g2_gap_vs_raw_patch_bound` (advisory): TRANSFER DIRECTION. Informational: is the encoder-token gap larger or smaller than the raw-patch gap E17 measured (+0.3515)? A LARGER gap means the encoder concentrates excursion information that avg-pooling then discards, making the projector fix more valuable than the raw-patch bound suggested. A SMALLER gap means partial destruction upstream and caps the achievable gain.

#pooling #cross-modal #epoch-resolution #control #dataset-whitelist #wearables #baseline #optihealth-eiv #matched-baseline #subject-heldout
