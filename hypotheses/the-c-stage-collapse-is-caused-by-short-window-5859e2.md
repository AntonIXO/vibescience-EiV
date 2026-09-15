---
ambition_class: mechanism
created_at: '2026-08-30T11:22:01+00:00'
former_ids: []
gap_closure_estimate: 0.5
gates:
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr >= 0.60. v13flat (identical profile,
    20-ds pool) read 0.130 with top1_mass 0.98; the healthy same-instrument reference
    on this exact bundle reads +0.760 (ab_ctrl_s43).'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-long-pool-restores-correlation
  mde: 0.1
- blocking: true
  description: 'g2 BLOCKING (deployment): supabase hr_raw MAE beats the trivial 3.954,
    skill > 0. v13flat scored 4.658 (skill -0.704), v12conly 4.384 (-0.429), healthy
    2.692 (+1.262).'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-beats-trivial
  mde: 0.8
- blocking: true
  description: 'g3 BLOCKING (dense is the sharper axis): dense-128 hr_raw MAE < 8.0
    (trivial 7.774). v11full 21.277, v12conly 21.585, v13flat 24.611 -- the deficit
    has not moved under any lever yet. If the long-only pool leaves dense above trivial,
    pool composition is eliminated too, and the remaining suspects are the C-recipe
    knobs that differ v13flat-vs-band04: patch_pool_mode masked_avg vs avg-default,
    prompt_path fit256 vs truncated default, seed 43 vs 42 -- each individually testable
    in one arm.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-recovers
  mde: 1.0
id: the-c-stage-collapse-is-caused-by-short-window-5859e2
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'PRIMARY. Mechanism: 11.3% of natural batches in the 20-ds pool
    carry a nearly-empty prefix (27-41 HR minutes in 1440 epochs), vs 2.0% in the
    healthy band04 pool; on those batches a constant emission is locally optimal and
    the gradient teaches prefix-ignoring. Cutting to long cohorts drops the short
    share to ~2.6% without any other change. CALIBRATION, stated plainly: I am 0/4
    on this problem (roster, frozen-encoder, sampler weights, plus exp-051), my overall
    calibration is 0.411, and the target-distribution probe just killed the easiest
    version of this story. I give this ~40% and the gates decide.'
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
The C-stage collapse is caused by SHORT-WINDOW COHORTS IN THE C POOL: cutting dataset_filter to the 7 cohorts with HRmin p50 > 240 (band04's six + dapper) -- identical profile otherwise, same frozen v11 encoder, flat weights, single seed -- restores supabase hr_raw corr to >= 0.60 and brings dense-128 MAE under the 7.774 trivial, versus v13flat's corr 0.130 / dense 24.611.

## Rationale
WHAT SURVIVES FOUR ELIMINATIONS: encoder (4 measurements, production-stage probe R2 0.759 vs 0.772), A-hparams (byte-identical), captions/text-cache (measured clean), instrument (healthy adapter reads the new bundle BETTER than its record), sampler weights (v13flat: maximal flattening, all three gates failed). The one measured difference left between every healthy and every collapsed C arm ON THE POOL AXIS: InstructionDataset gates on min_coverage alone -- it accepts neither min_hr_coverage nor hr_coverage_denominator -- so the 20-ds pool admits 1,989 windows from 14 added cohorts, nine of which are 100% <60 HR-minutes. The healthy band04 pool has p05=330 HR-minutes; mine has 29. The target distributions are near-identical, so if this arm ALSO fails, the pool is eliminated as a whole and the recipe knobs (masked_avg / fit256 / seed) become the last confounded set.

## Plan
SINGLE SEED, C-ONLY, ONE VARIABLE vs lora_proxy_12ch_v13flat: dataset_filter cut from 20 cohorts to the 7 with HRmin p50 > 240 measured on the admitted C pool (dapper 418, dreamt 532, mmash 798, optihealth_supabase 1069, big_ideas_glycemic 1244, lifesnaps 1360, pmdata 1382) -- exactly the preregistered fork from exp-v13 ('temporarily cut the roster to cohorts with HRmin p50 > 240'). All weights 1.0, same frozen encoder outputs/checkpoints/v11full/phase_b, same band04 law, masked_avg, fit256 prompt, seed 43. Verified by dict diff before launch: only dataset_filter and notes differ. Runner cloned from run_v13_flat.sh; scoring by the same emission path, supabase n=200 batch1 rng0 + dense-128.

MEASURED SETUP EVIDENCE (scratch/_pool_admission_diff.py + _pool_target_diff.py, logs/v13_pool_admission.json + v13_pool_target_diff.json): the 20-ds C pool carries HRmin p05=29 vs healthy band04's p05=330, <60min share 9.8% vs 2.0%, with 9 cohorts at 100% short windows; the TARGET distributions are nearly identical (hr_raw p50 75.1 vs 75.9, share within 3bpm of the emitted constant 5.4% vs 4.5%), so a marginal-shift explanation is already dead -- the mechanism under test is the GRADIENT of near-empty prefixes, not the target prior.

DECISION RULE, preregistered:
 * all gates pass -> short-cohort pool admission at C is the cause; follow-up finds the max short share that still trains, and the code fix is min_hr_coverage in InstructionDataset.
 * g1 fails with dense still ~20+ -> pool composition eliminated; the collapsed-vs-healthy delta reduces to the C-recipe knobs (masked_avg, fit256 prompt, seed) and the next arm flips patch_pool_mode to avg FIRST (it is the knob with a documented train/serve parity history, W10).

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-long-pool-restores-correlation` (blocking): g1 BLOCKING: supabase hr_raw corr >= 0.60. v13flat (identical profile, 20-ds pool) read 0.130 with top1_mass 0.98; the healthy same-instrument reference on this exact bundle reads +0.760 (ab_ctrl_s43).
Gate `g2-beats-trivial` (blocking): g2 BLOCKING (deployment): supabase hr_raw MAE beats the trivial 3.954, skill > 0. v13flat scored 4.658 (skill -0.704), v12conly 4.384 (-0.429), healthy 2.692 (+1.262).
Gate `g3-dense-recovers` (blocking): g3 BLOCKING (dense is the sharper axis): dense-128 hr_raw MAE < 8.0 (trivial 7.774). v11full 21.277, v12conly 21.585, v13flat 24.611 -- the deficit has not moved under any lever yet. If the long-only pool leaves dense above trivial, pool composition is eliminated too, and the remaining suspects are the C-recipe knobs that differ v13flat-vs-band04: patch_pool_mode masked_avg vs avg-default, prompt_path fit256 vs truncated default, seed 43 vs 42 -- each individually testable in one arm.

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
