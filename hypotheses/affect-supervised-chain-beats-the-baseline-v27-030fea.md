---
ambition_class: mechanism
created_at: '2026-08-31T03:46:30+00:00'
former_ids: []
gap_closure_estimate: 0.6
gates:
- blocking: true
  description: 'g1 BLOCKING (beat the baseline): supabase hr_raw MAE <= 2.836 (v10conly
    family median) AND corr >= 0.60. Comparator v25r30 (identical chain, no-head A):
    3.203 / 0.717. PASS -> the affect head buys deployment accuracy on top of the
    healed chain and the utilisation goal has its first beat-baseline artifact.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g1-beats-baseline-median
  mde: 0.8
- blocking: false
  description: 'g2 advisory: within the C-only 0.80 band of v25r30 (MAE 2.40-4.00)
    with corr >= 0.60 -- ''no harm from supervision'' even if the baseline median
    is not beaten.'
  diagnostic_id: eiv_gen_peak_corr
  id: g2-no-harm-vs-v25
  mde: 0.1
id: affect-supervised-chain-beats-the-baseline-v27-030fea
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'Two mechanisms could push below 3.2: the +1.16 rank gain (more
    distinguishable days) and the affect gradient sharpening arousal-linked HR structure.
    Against: label heads have never moved the HR deployment axis in this repo''s record.
    Prior ~40% on the strict 2.836 bar; ~70% on the no-harm band. Calibration 0.408;
    gates decide.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- data-contract-violation
- matched-baseline
- subject-heldout
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- deployment-distribution
- sensor-text
- baseline
- contrastive
- wearables
- epoch-resolution
- control
---

## Statement
AFFECT-SUPERVISED CHAIN BEATS THE BASELINE: v27 (A = v26affect with rank 8.60 -> healed B@30 -> band04 C) reaches supabase hr_raw MAE <= 2.836 (the v10conly baseline median) with corr >= 0.60, improving on the no-head healed chain (3.203/0.717) via the affect head's rank and supervision gains.

## Rationale
exp-v26: every supervision head alone is rank-POSITIVE, affect the best (8.60 > healthy anchor 8.35). The cheapest test of 'labels help deployment' is therefore the affect-A chain on the healed B/C -- one variable vs v25r30. If it beats the baseline median, the utilisation goal produces its first artifact; if it lands in the band, supervision is at least free and the pairwise interaction hunt continues with a deployment-neutral conscience.

## Plan
v27 chain: A = v26affect (affect head 0.3, eff_rank 8.60 -- best fresh-A geometry on record, labels valence/arousal/stress over 6 corpora incl dapper/k_emocon/popane_s5-s7 reaching training for the FIRST time in a healthy chain) -> B = phase_b_12ch_v25bigbatch batch 30 (the healed B) -> C = band04 untouched. ONE variable vs exp-v25r30: the A checkpoint. Runner scratch/run_v27_affectchain.sh. This is the direct execution of Anton's utilisation goal on the healed substrate.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Gate `g1-beats-baseline-median` (blocking): g1 BLOCKING (beat the baseline): supabase hr_raw MAE <= 2.836 (v10conly family median) AND corr >= 0.60. Comparator v25r30 (identical chain, no-head A): 3.203 / 0.717. PASS -> the affect head buys deployment accuracy on top of the healed chain and the utilisation goal has its first beat-baseline artifact.
Gate `g2-no-harm-vs-v25` (advisory): g2 advisory: within the C-only 0.80 band of v25r30 (MAE 2.40-4.00) with corr >= 0.60 -- 'no harm from supervision' even if the baseline median is not beaten.

#deployment-distribution #sensor-text #baseline #contrastive #wearables #epoch-resolution #control #optihealth-eiv #data-contract-violation #matched-baseline #subject-heldout
