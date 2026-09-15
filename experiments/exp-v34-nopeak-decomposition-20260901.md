---
artifacts:
- outputs/adapters/v34nopeak_c
- logs/v34_driver.log
- logs/v34_caption_verify.log
- logs/v34_geometry.json
- logs/emission_v34nopeak_supa_summary.json
- logs/emission_v34nopeak_dense_summary.json
closed: true
config_note: 'v34nopeak = v33dispersion + exactly one key: caption_style hr_dispersion
  -> hr_dispersion_nopeak (state_peak=False). DECOMPOSITION arm: v33 changed two things
  at once -- it supplied within-day SPREAD and it literally named the scored target
  (''HR peak 119.1 bpm'' in 398/400 captions). v34 keeps the spread and removes the
  naming, including any clause the peak is algebraically recoverable from. Pre-flight
  scratch/_verify_h4b_caption.py -> logs/v34_caption_verify.log: hr_dispersion byte-identical
  to v33 on 400/400, 0/400 peak mentions, 0 windows reconstructing the peak by value
  or difference. Driver scratch/run_v34_nopeak.sh.'
cost:
  gpu_hours: 1.6
  wall_hours: 0.0
crash_reason: ''
created_at: '2026-08-31T21:33:13+00:00'
diagnostics_measured:
- after: 0.488
  before: 0.515
  diagnostic_id: eiv_gen_peak_corr
- after: 2.095
  before: 2.142
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
- after: 3.919
  before: 3.076
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
- after: 5.414
  before: 8.637
  diagnostic_id: eiv_effective_rank
do_not_retry:
- Do NOT add per-target naming clauses to captions as a general technique. v34 removed
  the literal peak value and hr_peak got BETTER (skill +1.011 vs v33 +0.695, distinct
  25 vs 23), so the v33 win was never a labelling recipe. Naming the scored slot is
  not the lever; supplying dispersion is.
- Do NOT read v34's eff_rank 5.414 as a broken encoder. It sits below the 7.0 collapse
  threshold that killed v29, yet this arm posts the best supabase hr_peak skill on
  record (+1.011) and the best hr_raw MAE (2.095). This is the fifth and strongest
  disconfirmation that eff_rank is a quality metric -- treat it as sanity only on
  the caption branch.
- 'Do NOT promote v34 (or v33) while the JSON termination defect persists: 40/128
  dense emissions did not close natively (v33 47/128, v28 1/128). The fix belongs
  in Phase C decoding, not the caption.'
- Do NOT quote v34's dense hr_raw as a regression without the caveat that it is measured
  on a draw where 40 of 128 emissions needed prefix recovery; the paired CI vs v33
  is [+0.353,+1.355] on recovered rows.
external_run: ''
former_ids: []
gate_results: []
git_ref: 6fa2e69
hypothesis_id: the-hr-peak-native-emission-deficit-264f4f
id: exp-v34-nopeak-decomposition-20260901
interpretation:
  at: '2026-08-31T21:33:54+00:00'
  failure_mode: claim_false
  lesson: 'DISPERSION IS THE MECHANISM; NAMING THE TARGET IS NOT. Removing the literal
    ''HR peak X bpm'' clause from the v33 caption left the win intact and slightly
    better on the deployment draw (supa hr_peak skill +1.011 vs v33 +0.695 vs incumbent
    -4.422; hr_raw MAE 2.095 vs 2.142 vs 3.030). The competing ''v33 just labelled
    its own target'' account is therefore refuted, and the effect is a representation
    change that should transfer to any slot whose target is spread-like -- which is
    exactly what v35wide now tests on hrv/rr/temp. Second and larger lesson: eff_rank
    5.414 on this arm is BELOW the 7.0 threshold that condemned v29 as collapsed,
    yet it posts the campaign''s best supabase numbers. Combined with v31 (rank 10.106,
    deployment lost), rank is now disconfirmed five ways and is not even a reliable
    veto on the caption branch -- report it as sanity, never as a criterion, and never
    gate a caption arm on it. Cost side, recorded honestly: dense hr_raw regresses
    to 3.919 (paired CI vs v33 [+0.353,+1.355]), so the arm trades dense accuracy
    for sparse-draw peak skill, and the JSON termination defect (88/128 closed natively)
    still blocks promotion of the whole caption branch.'
  transfers_to:
  - h4-caption-content-not-architecture-the-3306b2
  - the-hr-peak-native-emission-deficit-264f4f
key_numbers:
  dense_hr_raw_mae: 3.919
  dense_hr_raw_std_ratio: 1.017
  dense_parse_native: 88.0
  dmae_dense_vs_v33_ci_hi: 1.355
  dmae_dense_vs_v33_ci_lo: 0.353
  dmae_dense_vs_v33_hr_raw: 0.843
  eff_rank: 5.414
  supa_hr_peak_corr: 0.488
  supa_hr_peak_distinct: 25.0
  supa_hr_peak_skill: 1.011
  supa_hr_raw_corr: 0.867
  supa_hr_raw_mae: 2.095
observed_effects:
- delta: -0.027
  diagnostic_id: eiv_gen_peak_corr
  direction: down
- delta: -0.047
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
- delta: 0.843
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
- delta: -3.223
  diagnostic_id: eiv_effective_rank
  direction: down
parent_experiment_id: exp-v33-dispersion-captions-20260831
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
    eiv_gen_peak_donor_gap_bpm: false
    eiv_gen_peak_skill_bpm: false
    eiv_gen_pred_bucket_entropy: false
provenance: {}
recipe_fingerprint:
  git_ref: 6fa2e69
verdict: inconclusive
---

## Summary
Tests hypothesis [[the-hr-peak-native-emission-deficit-264f4f]].
Branches from [[exp-v33-dispersion-captions-20260831]].
At commit `6fa2e69`.

## Measurements
- [[eiv_gen_peak_corr]]: before 0.515 → after 0.488  (Δ -0.027, down ✗ predicted up)
- [[eiv_supabase_seed_band_mae_bpm]]: before 2.142 → after 2.095  (Δ -0.047, down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 3.076 → after 3.919  (Δ +0.843, up)
- [[eiv_effective_rank]]: before 8.637 → after 5.414  (Δ -3.223, down)

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `claim_false`

DISPERSION IS THE MECHANISM; NAMING THE TARGET IS NOT. Removing the literal 'HR peak X bpm' clause from the v33 caption left the win intact and slightly better on the deployment draw (supa hr_peak skill +1.011 vs v33 +0.695 vs incumbent -4.422; hr_raw MAE 2.095 vs 2.142 vs 3.030). The competing 'v33 just labelled its own target' account is therefore refuted, and the effect is a representation change that should transfer to any slot whose target is spread-like -- which is exactly what v35wide now tests on hrv/rr/temp. Second and larger lesson: eff_rank 5.414 on this arm is BELOW the 7.0 threshold that condemned v29 as collapsed, yet it posts the campaign's best supabase numbers. Combined with v31 (rank 10.106, deployment lost), rank is now disconfirmed five ways and is not even a reliable veto on the caption branch -- report it as sanity, never as a criterion, and never gate a caption arm on it. Cost side, recorded honestly: dense hr_raw regresses to 3.919 (paired CI vs v33 [+0.353,+1.355]), so the arm trades dense accuracy for sparse-draw peak skill, and the JSON termination defect (88/128 closed natively) still blocks promotion of the whole caption branch.

Transfers to: [[h4-caption-content-not-architecture-the-3306b2]], [[the-hr-peak-native-emission-deficit-264f4f]]

## Key numbers
- `dense_hr_raw_mae`: 3.919
- `dense_hr_raw_std_ratio`: 1.017
- `dense_parse_native`: 88
- `dmae_dense_vs_v33_ci_hi`: 1.355
- `dmae_dense_vs_v33_ci_lo`: 0.353
- `dmae_dense_vs_v33_hr_raw`: 0.843
- `eff_rank`: 5.414
- `supa_hr_peak_corr`: 0.488
- `supa_hr_peak_distinct`: 25
- `supa_hr_peak_skill`: 1.011
- `supa_hr_raw_corr`: 0.867
- `supa_hr_raw_mae`: 2.095

## Artifacts
- `outputs/adapters/v34nopeak_c`
- `logs/v34_driver.log`
- `logs/v34_caption_verify.log`
- `logs/v34_geometry.json`
- `logs/emission_v34nopeak_supa_summary.json`
- `logs/emission_v34nopeak_dense_summary.json`

## Do not retry
- Do NOT add per-target naming clauses to captions as a general technique. v34 removed the literal peak value and hr_peak got BETTER (skill +1.011 vs v33 +0.695, distinct 25 vs 23), so the v33 win was never a labelling recipe. Naming the scored slot is not the lever; supplying dispersion is.
- Do NOT read v34's eff_rank 5.414 as a broken encoder. It sits below the 7.0 collapse threshold that killed v29, yet this arm posts the best supabase hr_peak skill on record (+1.011) and the best hr_raw MAE (2.095). This is the fifth and strongest disconfirmation that eff_rank is a quality metric -- treat it as sanity only on the caption branch.
- Do NOT promote v34 (or v33) while the JSON termination defect persists: 40/128 dense emissions did not close natively (v33 47/128, v28 1/128). The fix belongs in Phase C decoding, not the caption.
- Do NOT quote v34's dense hr_raw as a regression without the caveat that it is measured on a draw where 40 of 128 emissions needed prefix recovery; the paired CI vs v33 is [+0.353,+1.355] on recovered rows.

## Notes
THE DECOMPOSITION ANSWERED CLEANLY: THE MECHANISM IS DISPERSION, NOT TARGET-NAMING.

v33 confounded two interventions. v34 removes the naming and keeps the spread, and the win does not merely survive -- on the deployment draw it IMPROVES:
  supa hr_peak skill  +1.011 (v33 +0.695, v28 -4.422)
  supa hr_peak distinct  25  (v33 23, v28 16)
  supa hr_raw MAE      2.095 (v33 2.142, v28 3.030)
  supa hr_raw corr     0.867 (v33 0.893, v28 0.793)
The driver's own preregistered readout printed h1 SPREAD=YES, h2 NAMING=no, h3 no-harm FAIL.

So 'name the scored target in the text view' is REFUTED as the explanation, and 'give the text view within-day spread' is what carries the effect. That matters because the two imply different futures: naming is a per-target labelling recipe that says nothing about representation, while dispersion is a mechanism that should generalise to every slot whose target is a spread-like quantity. The recorded next step follows directly and is now running as v35wide (same dispersion clauses extended to hrv/rr/temp).

WHERE IT COSTS: dense hr_raw 3.919 vs v33 3.076, paired CI [+0.353,+1.355] on shared recovered rows -- v34 is genuinely worse there, and h3 no-harm FAILS. dense hr_peak is not distinguishable (dMAE -0.118, CI [-2.131,+1.819]). So the arm buys peak on the sparse deployment draw and pays on the dense one.

EFF_RANK 5.414 IS THE HEADLINE ANOMALY. That is BELOW the 7.0 threshold whose breach condemned v29 (6.783) as collapsed, yet this arm holds the best supabase numbers in the campaign. Fifth independent disconfirmation that rank is a direction: v31 at the highest rank ever (10.106) lost deployment, v34 at the lowest rank since the collapse wins it. On the caption branch eff_rank is not even a reliable veto and must be reported as sanity only.

DEFECT INHERITED, NOT FIXED: 88/128 dense emissions closed their JSON natively (v33 81, v28 127). Same repetition loop after the wanted fields. Neither v33 nor v34 is promotable until Phase C decoding terminates properly; the incumbent remains v28affect01.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
