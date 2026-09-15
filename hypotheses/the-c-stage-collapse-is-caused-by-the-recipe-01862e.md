---
ambition_class: mechanism
created_at: '2026-08-30T12:29:20+00:00'
former_ids: []
gap_closure_estimate: 0.6
gates:
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr >= 0.60. Collapsed arms on this
    corpus read -0.097..0.130; the healthy reference (same instrument, same bundle)
    reads +0.760. Either outcome of this gate is decisive: PASS exonerates the corpus
    and localises the cause inside {masked_avg, fit256 prompt, seed 43}; FAIL exonerates
    the ENTIRE recipe and localises the cause in the new bundle as C-stage training
    data.'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-exact-recipe-healthy-on-new-corpus
  mde: 0.1
- blocking: true
  description: 'g2 BLOCKING (deployment): supabase hr_raw MAE beats the trivial 3.954
    (skill > 0). Healthy family recorded 2.836 (v10conly median) on the old corpus
    and 2.692 on this bundle at inference.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-beats-trivial
  mde: 0.8
- blocking: false
  description: 'g3 advisory: dense-128 hr_raw MAE < 8.0 (trivial 7.774). Non-blocking
    because the healthy family''s dense numbers were recorded on the old corpus (v10conly
    median 3.573) and no exact-recipe dense reference exists on this bundle.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-advisory
  mde: 1.0
id: the-c-stage-collapse-is-caused-by-the-recipe-01862e
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'I predict FAIL-side (corr stays < 0.60, i.e. the corpus is the
    cause): five recipe-side levers died in a row, every knob of the collapsed recipe
    has an individually-healthy old-corpus precedent (w6msk corr 0.74 with masked_avg;
    jsonfit_s42 corr 0.81 with fit256), and no fresh C arm trained on a post-rebuild
    bundle has EVER been healthy (ab_ctrl, ab_wl, c_patch120 x2, baseline_sync, v11full,
    v12, v13, v14). Stated with my 0.408 calibration and 0/5 record on this problem:
    the gates decide, and this arm is informative in BOTH directions by construction.'
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags:
- matched-baseline
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- deployment-distribution
- collapse
- baseline
- sensor-text
- epoch-resolution
---

## Statement
The C-stage collapse is caused by the RECIPE-KNOB BLOCK {patch_pool_mode masked_avg, fit256 prompt, seed 43} and not by the new corpus: training the byte-identical healthy band04 recipe (avg pooling, default truncated prompt, seed 42, 6-ds filter) C-only off the frozen v11 encoder on the current [14092] bundle produces a healthy adapter -- supabase hr_raw corr >= 0.60 and skill > 0.

## Rationale
AFTER FIVE ELIMINATIONS THE HYPOTHESIS SPACE IS A CLEAN DISJUNCTION. Collapsed arms differ from healthy arms in exactly two blocks: (A) recipe knobs {patch_pool_mode masked_avg, prompt fit256, seed 43} and (B) the corpus the C stage TRAINS on. Every knob in (A) has a healthy precedent on the old corpus (w6msk s43/44/45: masked_avg, corr 0.741-0.745; jsonfit_s42: fit256 prompt, corr 0.809 skill +1.109) but never in this combination; (B) has NO healthy precedent at all -- all nine fresh post-rebuild C arms collapsed, across three corpora versions and three rosters, while the healthy adapters only ever READ the new bundle. One arm running recipe (A)=healthy-exact on corpus (B)=new separates the blocks completely. The free probes already narrowed (B): targets are perfectly coupled to the prefix (0% > 2bpm over 300 draws) and the marginal is not the constant, so if (B) wins it is not a target-construction bug -- it is something the new bundle does to LEARNING (e.g. the prefix distribution itself).

## Plan
SINGLE SEED (the profile's own 42), C-ONLY off the frozen v11 encoder (already 4x-exonerated). Runner scratch/run_v15_exact.sh: profile lora_proxy_12ch_wl_band04 UNTOUCHED -- the exact recipe with recorded healthy results (6-ds filter, avg pooling default, truncated default prompt, seed 42) -- trained on the CURRENT [14092] bundle. Scoring identical to v11-v14 (supabase n=200 batch1 rng0 + dense-128); pre-check asserts the manifest really shows 6 datasets/avg/default prompt.

DELIBERATELY COMPOUND vs v14 (prompt, pooling, seed all flip at once): after five single-variable refutations the disjunction 'recipe knobs vs corpus' is worth more than any single knob, and this arm resolves it either way in one GPU-hour. If PASS: bisect {masked_avg, fit256, seed} next, one arm, starting with patch_pool_mode per the v14 fork. If FAIL: the cause is the new bundle as TRAINING data for C, while the same bundle is fine as INFERENCE data for a healthy adapter -- next probes are corpus-side (population_stats change, window anchoring change, supabase block content) with the coupling probe already ruling out target/prefix decoupling.

MEASURED CONTEXT feeding this design: scratch/_target_coupling_probe.py (logs/v14_target_coupling_probe.json) -- 300 training draws from the collapsed v14 recipe show |json target - prefix-derived| p50 0.025 bpm, max 0.05, 0% over 2 bpm, i.e. targets are PERFECTLY coupled to the augmented prefix; and the target marginal's mode is 70-75 bpm with only 12% mass within 3 bpm of the emitted 55.9 constant -- so both 'decoupled targets' and 'LM learned the marginal mode' are dead. The constant is not the data's mode; it is a learned prefix-ignoring attractor.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-exact-recipe-healthy-on-new-corpus` (blocking): g1 BLOCKING: supabase hr_raw corr >= 0.60. Collapsed arms on this corpus read -0.097..0.130; the healthy reference (same instrument, same bundle) reads +0.760. Either outcome of this gate is decisive: PASS exonerates the corpus and localises the cause inside {masked_avg, fit256 prompt, seed 43}; FAIL exonerates the ENTIRE recipe and localises the cause in the new bundle as C-stage training data.
Gate `g2-beats-trivial` (blocking): g2 BLOCKING (deployment): supabase hr_raw MAE beats the trivial 3.954 (skill > 0). Healthy family recorded 2.836 (v10conly median) on the old corpus and 2.692 on this bundle at inference.
Gate `g3-dense-advisory` (advisory): g3 advisory: dense-128 hr_raw MAE < 8.0 (trivial 7.774). Non-blocking because the healthy family's dense numbers were recorded on the old corpus (v10conly median 3.573) and no exact-recipe dense reference exists on this bundle.

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
