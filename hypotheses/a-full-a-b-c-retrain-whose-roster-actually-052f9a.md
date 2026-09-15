---
ambition_class: reframe
created_at: '2026-08-30T07:08:28+00:00'
former_ids: []
gap_closure_estimate: 0.3
gates:
- blocking: true
  description: 'g1 BLOCKING (deployment, the only gate that decides deployability):
    supabase hr_raw MAE on the standard 200-row batch-1 draw must beat the trivial
    baseline, i.e. skill > 0. This chain retrains A AND B, so it is priced against
    the FULL-CHAIN re-seed band of 3.19 bpm (exp-052), NOT the 0.80 C-only band (exp-054).
    A median inside 3.19 of v10conly''s 2.84 is NOT a regression and must not be reported
    as one; a value WORSE than the trivial is a failure regardless of band, because
    baseline_sync already did that (-1.066) and that is the symptom this chain exists
    to fix.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g1-supabase-beats-trivial
  mde: 3.19
- blocking: true
  description: 'g2 BLOCKING (the corr collapse is the real symptom): supabase hr_raw
    corr must be >= 0.60. baseline_sync collapsed to 0.307 against the v10conly family''s
    0.739-0.792 while every stage reported success -- an axis the 3.19 bpm MAE band
    does not describe at all. If MAE lands in-band but corr stays near 0.3, the new-corpus
    chain is still broken and the roster was not the cause.'
  diagnostic_id: eiv_gen_peak_corr
  id: g2-corr-not-collapsed
  mde: 0.1
- blocking: true
  description: 'g3 BLOCKING (supervision reach, verified before the run and re-asserted
    after): the trained C adapter''s manifest must record a 20-dataset filter, and
    the admitted pool must carry >= 90% of corpus label-epochs. Measured pre-run:
    94.7% vs band04''s 44.2%, with six channels moving from ZERO reach to full. If
    the shipped manifest disagrees with this, the profile did not do what its notes
    claim and every number is void.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g3-supervision-actually-reaches
  mde: 3.19
- blocking: false
  description: 'g4 (non-blocking, dense no-harm): dense-128 hr_raw MAE should not
    regress beyond 1.0 bpm against v10conly''s 3.57 median. Non-blocking because the
    dense seed band (1.483 avg / 2.261 masked, exp-w6b) is itself larger than the
    effect this would detect, so it can only catch a gross regression.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g4-dense-no-harm
  mde: 1.0
id: a-full-a-b-c-retrain-whose-roster-actually-052f9a
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'PRIMARY. baseline_sync (the only prior full chain on a new corpus)
    scored 5.021 bpm against a 3.954 trivial -- skill -1.066, i.e. WORSE than always
    predicting the median, with corr collapsing to 0.307. I predict the v11 chain
    lands between 2.8 and 4.0 bpm, i.e. at or better than the trivial. CALIBRATION
    WARNING: I am 3/11 (0.27) on this exact diagnostic and 0.411 overall, and the
    vault''s base rate for ''a roster change fixes deployment'' is poor (the allowlist
    lever is itself a closed FAIL). The blocking gates decide this, not the prediction.'
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: Secondary but arguably the sharper test. baseline_sync's corr fell
    to 0.307 vs the v10conly family's 0.739-0.792 -- a collapse the 3.19 bpm MAE band
    does not cover. I predict >= 0.60.
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags:
- matched-baseline
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- deployment-distribution
- wearables
- baseline
- sensor-text
- epoch-resolution
- collapse
---

## Statement
A full A->B->C retrain whose roster actually covers the corpus produces a deployable Phase C on the 2026-08-30 bundle [14092]: supabase hr_raw beats the trivial baseline (skill > 0) and its correlation does not collapse (corr >= 0.60), unlike baseline_sync which scored MAE 5.021 vs trivial 3.954 at corr 0.307 while its C roster saw only 53.9% of windows and 44.2% of label-epochs.

## Rationale
THE PROBLEM THIS TESTS IS ALREADY ON THE RECORD, AND ITS PRIME SUSPECT IS NOW MEASURED. Every fresh A->B->C chain on a rebuilt corpus has produced a deployment-broken Phase C, most recently baseline_sync at MAE 5.021 vs trivial 3.954 (skill -1.066, corr 0.307) while every stage reported success. Four variables differed at once there, so it was recorded as directional only.

WHAT IS NEW: a fifth variable nobody had priced. Measured today on the synced bundle, the designated baseline C profile (lora_proxy_12ch_wl_band04) admits 7,592 of 14,092 windows and drops 48.4% of ALL label-epochs, with SIX channels at exactly ZERO reach -- valence 0/40,893, arousal 0/6,457, protocol_condition, exam_session_code, stress_raw, stimulus_class. Its roster predates POPANE s5/s6/s7, DAPPER and K-EmoCon entirely. Retraining on the new corpus with that roster trains on the old half of it while the manifest advertises the new corpus, which is precisely the class of silent mismatch this vault has paid for three times (W10's patch_presence fallback, E22's mask inversion, and today's two registry drops).

So the honest framing is not 'more data should help'. It is: the previous full-chain failures were measured through a roster that could not see most of the corpus, and that confound is removable. This chain removes it and keeps every other measured decision fixed.

## Plan
FULL CHAIN A -> B -> C, runner scratch/run_v11_chain.sh, on corpus [14092] sha b659855.

PROFILES (configs/training.yaml): phase_a_12ch_v11full -> phase_b_12ch_v11full -> lora_proxy_12ch_v11full, plus phase_a_12ch_v11affect as a separate arm (single variable affect_weight 0.0 -> 0.3) that is NOT part of this chain.

ROSTER 19 datasets for A/B, 20 for C. EXCLUDED, both evidence-led:
  dryad_ifh_affect (A/B and C) -- its hr_peak mode collapse (19 -> 6 distinct emitted values, top-1 mass 0.271 -> 0.604) is measured on RAW TEXT and survived the retraction of the hrv half of ro admitting-dryad...de51ab. Also 4,511 windows at HR density 0.113.
  nurse_stress (A/B only, KEPT in C) -- shift-schedule-carried state labels, clock-alone AUC 0.6358, and it softened the within-window null in ro that-the-full-corpus...d55a19. Phase C is json-slot supervised, not state-scored, so the exclusion does not apply there.

UPSAMPLING priced pre-run (scratch/_roster_pricing.py): short lab cohorts 10.3% -> 29.2% of the sampler, valence sources 17.6% -> 37.8%, lifesnaps 50.8% -> 36.2%. Capped at 7x because CASE and K-EmoCon are 30 and 31 windows.

CARRIED FROM MEASURED RESULTS: patch_pool_mode masked_avg at C (exp-w6b end-to-end, exp-w12/w13 out-of-sample +0.2412); hr_coverage_denominator recording on ALL THREE stages (a denominator differing between stages silently changes the population mid-chain); prompt_path json_slots_prompt_fit256.md (the shipped prompt is 327 tokens against a 256 budget and is silently truncated); band04 augmentation law unchanged.

VERIFIED BEFORE LAUNCH, by execution: label-epoch reach 44.2% -> 94.7%, EDA reach 33.5% -> 100%, six label channels from ZERO to full (valence 0 -> 40,893, arousal 0 -> 6,457); admitted pool carries state on 59.4% of windows and affect on 16.8% from 7 sources (was 2); 3-step wiring smoke RC=0 with loss 1.286 -> 0.700; suite 848 passed / 0 failed after updating three guard allowlists.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-supabase-beats-trivial` (blocking): g1 BLOCKING (deployment, the only gate that decides deployability): supabase hr_raw MAE on the standard 200-row batch-1 draw must beat the trivial baseline, i.e. skill > 0. This chain retrains A AND B, so it is priced against the FULL-CHAIN re-seed band of 3.19 bpm (exp-052), NOT the 0.80 C-only band (exp-054). A median inside 3.19 of v10conly's 2.84 is NOT a regression and must not be reported as one; a value WORSE than the trivial is a failure regardless of band, because baseline_sync already did that (-1.066) and that is the symptom this chain exists to fix.
Gate `g2-corr-not-collapsed` (blocking): g2 BLOCKING (the corr collapse is the real symptom): supabase hr_raw corr must be >= 0.60. baseline_sync collapsed to 0.307 against the v10conly family's 0.739-0.792 while every stage reported success -- an axis the 3.19 bpm MAE band does not describe at all. If MAE lands in-band but corr stays near 0.3, the new-corpus chain is still broken and the roster was not the cause.
Gate `g3-supervision-actually-reaches` (blocking): g3 BLOCKING (supervision reach, verified before the run and re-asserted after): the trained C adapter's manifest must record a 20-dataset filter, and the admitted pool must carry >= 90% of corpus label-epochs. Measured pre-run: 94.7% vs band04's 44.2%, with six channels moving from ZERO reach to full. If the shipped manifest disagrees with this, the profile did not do what its notes claim and every number is void.
Gate `g4-dense-no-harm` (advisory): g4 (non-blocking, dense no-harm): dense-128 hr_raw MAE should not regress beyond 1.0 bpm against v10conly's 3.57 median. Non-blocking because the dense seed band (1.483 avg / 2.261 masked, exp-w6b) is itself larger than the effect this would detect, so it can only catch a gross regression.

#deployment-distribution #wearables #baseline #sensor-text #epoch-resolution #collapse #matched-baseline #optihealth-eiv
