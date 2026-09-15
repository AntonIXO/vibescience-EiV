---
ambition_class: mechanism
created_at: '2026-08-30T09:56:14+00:00'
former_ids: []
gap_closure_estimate: 0.5
gates:
- blocking: true
  description: 'g1 BLOCKING (the fork): supabase hr_raw corr >= 0.60. v12conly (identical
    profile, upsampled roster) read -0.097; the same-instrument healthy reference
    on this exact eval bundle reads +0.760 (ab_ctrl_s43, measured today). Single seed
    suffices: the C-only band is 0.80 bpm and the gap under test is a corr swing of
    ~0.86, not a within-band difference.'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-flat-weights-restore-correlation
  mde: 0.1
- blocking: true
  description: 'g2 BLOCKING (deployment, absolute): supabase hr_raw MAE must beat
    the trivial 3.954, i.e. skill > 0. v12conly scored 4.384 (skill -0.429) and v11full
    5.490 (-1.535). The healthy reference on the same bundle is 2.692 (+1.262).'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-beats-trivial
  mde: 0.8
- blocking: true
  description: 'g3 BLOCKING (dense is the sharper axis): dense-128 hr_raw MAE must
    come in under 8.0 (the trivial is 7.774). v11full and v12conly both sat at ~21.3-21.6
    -- a 13.8 bpm deficit that did NOT move when the encoder was frozen. If flat weights
    leave dense above the trivial, upsampling is not the cause either and the next
    suspect is the C pool''s own admission (InstructionDataset gates on min_coverage
    ALONE -- it accepts neither min_hr_coverage nor hr_coverage_denominator, so the
    C pool is a different population from the A/B pool even under an identical roster).'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-recovers
  mde: 1.0
id: the-c-stage-collapse-is-caused-by-upsampling-186858
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'PRIMARY. Measured: v12''s weights give short lab cohorts 26.9%
    of sampled batches against a natural 11.3%, with a 19.5x weight spread. Those
    cohorts are 27-41 measured HR minutes inside a 1440-epoch window, so on ~27% of
    batches the prefix is nearly empty and emitting a constant is locally optimal.
    Flat weights drop that to 11.3%. I predict corr >= 0.60. CALIBRATION, stated because
    it should discount this: I have now been wrong on this exact problem three times
    running (roster, then frozen-encoder, both refuted), and my supabase-MAE calibration
    is 3/11. The gates decide.'
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
- collapse
- baseline
- sensor-text
- epoch-resolution
---

## Statement
The C-stage collapse is caused by UPSAMPLING short lab cohorts, not by the corpus or the roster. Setting every dataset_filter weight to 1.0 -- identical profile otherwise, same frozen encoder, same 20-dataset roster, single seed -- restores supabase hr_raw corr to >= 0.60 and brings dense-128 MAE back under the 7.774 trivial, versus v12conly's corr -0.097 and dense MAE 21.585.

## Rationale
THE ELIMINATIONS ARE NOW EXTENSIVE, AND THEY POINT HERE. Ruled out by measurement today: the encoder (production-stage HR R2 0.7593 vs healthy 0.7715, corr 0.8793 vs 0.8791, soft-token norms 50.59 vs 50.61; freezing it moved dense MAE 0.308 bpm out of 13.8); Phase-A hyperparameters (all 20 keys byte-identical); caption collisions (99.7% unique); the text cache (1500/1500 content-addressed hits); and the instrument itself (the healthy ab_ctrl_s43 adapter reads the NEW eval bundle at MAE 2.692 / corr 0.760, BETTER than its recorded 2.836 / 0.739).

WHAT IS LEFT is what the new C recipe TEACHES, and the sampler is the part of it I changed most aggressively. Measured on the realised weights rather than the nominal ones: short lab cohorts take 26.9% of sampled batches against a natural 11.3%, and per-sample weights span 0.768-14.994 (19.5x). `sampler_weight_cap` bounds only the HR-bucket balancing term; a 7.0 dataset weight passes through uncapped.

WHY THAT COULD PRODUCE EXACTLY THIS FAILURE: those cohorts hold 27-41 measured HR minutes inside a 1440-epoch window. On roughly a quarter of batches the model sees a nearly-empty prefix paired with a target it cannot derive from it, and a constant emission is the locally optimal response. That is the shape observed -- top1_mass 0.805 (v11) and 0.470 with distinct 6 (v12), corr at or below zero on BOTH the sparse and dense populations.

No healthy C arm on record used upsampling: v9band04 and v10conly both used flat 1.0 weights on 3-6 datasets. So 'upsampling' has been perfectly confounded with 'new corpus' in every collapsed arm, and this breaks it.

## Plan
SINGLE SEED, C-ONLY, one variable. Profile lora_proxy_12ch_v13flat = lora_proxy_12ch_v12conly with every dataset_filter weight set to 1.0. Same frozen encoder (outputs/checkpoints/v11full/phase_b), same 20-dataset roster, same band04 law, same masked_avg pooling, same fit256 prompt, same seed 43. Verified by dict diff before launch: only `dataset_filter` and `notes` differ.

Runner scratch/run_v13_flat.sh (clone of run_v12_conly.sh with the profile swapped), scoring by the same emission_diversity_audit path at n=200 batch 1 rng seed 0, plus dense-128.

MEASURED BEFORE THE RUN (scratch/_sampler_composition.py, logs/v12_sampler_composition.json): under v12's weights the SHORT-cohort share of sampled batches is 26.9% against a natural 11.3%, and the per-sample weight range is 0.768 to 14.994 -- a 19.5x spread. `sampler_weight_cap` clips only the HR-bucket term, so the 7.0 dataset weights are NOT capped.

DECISION RULE, preregistered:
  * g1+g2+g3 pass -> upsampling short lab sessions is the cause; the fix is a weight policy, and the follow-up is to find the largest short-cohort share that still trains.
  * g1 fails with dense still ~20 bpm -> upsampling is NOT the cause either. Then the remaining difference between every healthy and every collapsed C arm is the POOL ITSELF, and the next probe is free: compare the admitted C pools (v10conly's 3-dataset filter on the current corpus vs this 20-dataset one) on window count, HR-coverage distribution and target-value distribution. No training needed to run it.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-flat-weights-restore-correlation` (blocking): g1 BLOCKING (the fork): supabase hr_raw corr >= 0.60. v12conly (identical profile, upsampled roster) read -0.097; the same-instrument healthy reference on this exact eval bundle reads +0.760 (ab_ctrl_s43, measured today). Single seed suffices: the C-only band is 0.80 bpm and the gap under test is a corr swing of ~0.86, not a within-band difference.
Gate `g2-beats-trivial` (blocking): g2 BLOCKING (deployment, absolute): supabase hr_raw MAE must beat the trivial 3.954, i.e. skill > 0. v12conly scored 4.384 (skill -0.429) and v11full 5.490 (-1.535). The healthy reference on the same bundle is 2.692 (+1.262).
Gate `g3-dense-recovers` (blocking): g3 BLOCKING (dense is the sharper axis): dense-128 hr_raw MAE must come in under 8.0 (the trivial is 7.774). v11full and v12conly both sat at ~21.3-21.6 -- a 13.8 bpm deficit that did NOT move when the encoder was frozen. If flat weights leave dense above the trivial, upsampling is not the cause either and the next suspect is the C pool's own admission (InstructionDataset gates on min_coverage ALONE -- it accepts neither min_hr_coverage nor hr_coverage_denominator, so the C pool is a different population from the A/B pool even under an identical roster).

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
