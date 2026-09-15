---
ambition_class: mechanism
created_at: '2026-09-02T20:56:44+00:00'
former_ids: []
gap_closure_estimate: 0.3
gates:
- blocking: true
  description: 'the three rungs are measured on the IDENTICAL 128 rows (t1b''s row
    index) and reported as one table: token head / hidden-state ridge / emitted, each
    with always-median 6.876 beside it'
  diagnostic_id: eiv_lm_hidden_hr_raw_ridge_mae_bpm
  id: g0
  mde: null
- blocking: true
  description: hidden-state ridge beats BOTH the donor-prefix and zeroed-prefix nulls
    by > 3 bpm (the prefix, not the prompt, is what the probe reads)
  diagnostic_id: eiv_lm_hidden_hr_raw_ridge_mae_bpm
  id: g1
  mde: 0.5
- blocking: false
  description: 'median-of-8 decode on the unchanged adapter: paired CI95 vs the shipped
    sampler''s 2.843 entirely below zero AND scale in [0.85,1.35]'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g2
  mde: 0.3
- blocking: true
  description: the FORK is read from the hidden-state number ONLY (<=1.6 emission
    / >=2.3 forward-pass / between = both), never from the decode variants
  diagnostic_id: eiv_lm_hidden_hr_raw_ridge_mae_bpm
  id: g3
  mde: null
id: a-the-llm-keeps-hr-raw-in-its-residual-stream-and-loses-it-at-emission
interventions: []
override_reason: ''
paper_refs:
- song-2025-decoding-based-regression-2501-19383
- chen-2025-rl-decoding-regression-2512-06533
- tan-2024-llm-useful-timeseries-2406-16964
predicted_effects:
- diagnostic_id: eiv_lm_hidden_hr_raw_ridge_mae_bpm
  direction: down
  magnitude_note: 1.2-1.7 bpm on v41rebased_c, 128 split-honest rows; vs emitted 2.843
    (rung below) and token head 0.812 (rung above); donor/zeroed nulls > 6.0; always-median
    6.876.
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'median-of-8 estimator on the unchanged adapter: 2.843 -> <= 2.3
    (paired on 128 rows). Greedy and constrained each reported; constrained expected
    small (<0.2) because the JSON scaffold, not invalid tokens, is the departure.'
- diagnostic_id: eiv_gen_between_sample_std
  direction: none
  magnitude_note: scale must stay in [0.85,1.35] under every decode variant; a variant
    that buys MAE by collapsing dispersion is reported as failed.
problem_id: is-the-frozen-llm-load-bearing-or-a-formatter
problem_tags:
- optihealth-eiv
- matched-baseline
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags:
- optihealth
- readout
- medgemma
- target-style
---

## Statement
TRACK A. Of the 2.03 bpm that MedGemma+LoRA loses between the projector tokens (attention head 0.812) and its emitted hr_raw (2.843) on the 128 split-honest rows, MORE THAN HALF is lost at EMISSION, not in the forward pass: a RidgeCV probe on the frozen final-layer residual stream at the first hr_raw digit position (E36's STAGE_3 instrument, same 128 rows, subject-grouped folds) reads eiv_lm_hidden_hr_raw_ridge_mae_bpm between 1.2 and 1.7 bpm -- i.e. the hidden state keeps ~55-70% of the head's skill over the LLM's emission -- with donor-prefix and zeroed-prefix nulls above 6.0 (E36 on the e33 adapter read 1.48 / 6.98 / 6.94). AND, without any retraining, the emission side itself moves: on the SAME adapter and rows, (a) greedy decoding, (b) the MAE-matched estimator (median of K=8 samples at T=1), and (c) digit-constrained decoding after the hr_raw key each change eiv_dense128_hr_raw_mae_bpm, with (b) predicted to be the largest single gain, 2.843 -> <= 2.3 (exp-037's pmf-median recovered ~3.8 bpm of hr_peak skill on the old adapter). Pre-registered fork on the hidden-state number: <= 1.6 -> the loss is at emission and Track D-a is launched; >= 2.3 -> the loss is in the forward pass, D-a is NOT launched and the interface (Tracks E/G) is the next arm; in between -> report the split and run D-a anyway as the cheaper arm.

## Rationale
Problem record: 'WHERE in the LLM the information is lost' is the stated open question, and the E28-style hidden-state readout on the SAME pool as the token head has never been run. Prior art in this vault, all on OLD adapters and mostly hr_peak: E36 (2026-08-06, medgemma_12ch_e33.seed_1) hr_raw hidden-state ridge 1.48 vs donor 6.98; exp-e25 hr_peak linearly present at the emission position (R2 +0.51, MAE 11.8 vs 20.1 median); exp-e26 the digit function class carries coarse magnitude only (14.26 vs 18.49); exp-037 pmf-median instead of argmax took hr_peak skill -1.91 -> +1.86 with no retrain -- the vault's own lesson: separate decode rule from representation before concluding anything. Literature: Song & Bahri 2025 (arXiv:2501.19383) show digit decoding matches a pointwise head under constrained decoding + loss-matched estimator + no interleaved language, and name our departures as the expected losses; Chen 2025 (arXiv:2512.06533) says token-CE never sees magnitude. Tan 2024 is what exp-t1 replicated. Cost: ~15 GPU-min, no retrain, same 128 rows -> paired against every other tower number. Every downstream arm (D vs E vs G, and the product architecture) forks on this number.

## Plan
scratch/run_track_a.sh with the seed-family GPU-wait loop, queued NOW behind the family. (1) probe_e36 adapted: --adapter outputs/adapters/v41rebased_c --rows logs/t1b_heads_v41_tower.json idx --split heldout; STAGE_3 RidgeCV + donor + zeroed nulls -> logs/a_e36_v41.json. (2) emission_diversity_audit.py --decode {sampler|greedy|median8|constrained} (default sampler, flag off by default) on the same 128 rows -> logs/emission_v41rebased_heldout_{variant}.json. (3) paired table script -> logs/a_tower_v41.json. ~15 GPU-min total. record_diagnostics + close on the record before D-a is touched.

## Links
Tests problem [[is-the-frozen-llm-load-bearing-or-a-formatter]].
Predicts [[eiv_lm_hidden_hr_raw_ridge_mae_bpm]] (down).
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (down).
Predicts [[eiv_gen_between_sample_std]] (none).
Gate `g0` (blocking): the three rungs are measured on the IDENTICAL 128 rows (t1b's row index) and reported as one table: token head / hidden-state ridge / emitted, each with always-median 6.876 beside it
Gate `g1` (blocking): hidden-state ridge beats BOTH the donor-prefix and zeroed-prefix nulls by > 3 bpm (the prefix, not the prompt, is what the probe reads)
Gate `g2` (advisory): median-of-8 decode on the unchanged adapter: paired CI95 vs the shipped sampler's 2.843 entirely below zero AND scale in [0.85,1.35]
Gate `g3` (blocking): the FORK is read from the hidden-state number ONLY (<=1.6 emission / >=2.3 forward-pass / between = both), never from the decode variants
Cites [[song-2025-decoding-based-regression-2501-19383]], [[chen-2025-rl-decoding-regression-2512-06533]], [[tan-2024-llm-useful-timeseries-2406-16964]].

#optihealth #readout #medgemma #target-style #optihealth-eiv #matched-baseline
