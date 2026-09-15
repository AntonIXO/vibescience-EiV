---
artifacts:
- outputs/adapters/v33dispersion_c
- logs/v33_driver.log
- logs/v33_geometry.json
- logs/emission_v33dispersion_supa_summary.json
- logs/emission_v33dispersion_dense_summary.json
- scratch/_v33_honest_rescore.py
closed: true
config_note: 'v33dispersion = incumbent v28affect01 + exactly one key: caption_style
  hr_only -> hr_dispersion (dispersion-bearing captions state spread/peak, not just
  level). Chain A->B@30->band04 C, driver scratch/run_v33_dispersion.sh, launched
  20:18 by a cron session (outside systemd). Caption provenance CONFIRMED live from
  phase_config.json. Caption sanity pre-flight: 398/398 stated peaks match the scored
  emission target exactly, 400/400 distinct.'
cost:
  gpu_hours: 1.66
  wall_hours: 0.0
crash_reason: ''
created_at: '2026-08-31T19:22:36+00:00'
diagnostics_measured:
- after: 0.515
  before: 0.287
  diagnostic_id: eiv_gen_peak_corr
- after: 2.142
  before: 3.03
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
- after: 3.076
  before: 2.851
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
- after: 8.637
  before: 8.599
  diagnostic_id: eiv_effective_rank
do_not_retry:
- 'Do NOT promote v33dispersion to the deployment arm until the JSON non-closure is
  fixed. 47/128 dense emissions never close the object: the model emits a valid prefix
  then loops ''"temp":"normal","state":"flow"'' until the token budget ends. A deployment
  arm that fails to close its JSON on 37% of a draw is not deployable regardless of
  its MAE.'
- Do NOT quote the driver's own dense line (MAE 3.076 skill +3.910 on parse_ok 81)
  as a like-for-like result. That MAE is over 81 parsed rows while mae_trivial 6.986
  is the full 128-row baseline -- the identical 6.986 appearing for both v28 and v33
  is the tell. Use scratch/_v33_honest_rescore.py, which recovers truncated emissions
  by prefix-parsing and recomputes the baseline on the shared row set.
- 'Do NOT attribute the supabase win to a lucky draw without the paired test: it is
  z -4.62 on hr_raw and z -5.01 on hr_peak over 200 shared rows, both far outside
  the registered 0.195 paired sigma.'
- Do NOT re-run more Phase-A supervision-weight or detach variants to chase hr_peak.
  v29/v31/v32 closed that branch; the caption VIEW was the binding constraint, and
  this arm is the evidence.
external_run: ''
former_ids: []
gate_results: []
git_ref: d09559a
hypothesis_id: the-hr-peak-native-emission-deficit-264f4f
id: exp-v33-dispersion-captions-20260831
interpretation:
  at: '2026-09-01T07:37:12+00:00'
  failure_mode: test_invalid
  lesson: "THE CAPTION VIEW WAS THE BINDING CONSTRAINT ON hr_peak, not the encoder.
    One key (caption_style hr_only -> hr_dispersion) took supabase hr_peak from skill
    -4.422 to +0.695 (MAE 21.832 -> 16.715, z -5.01) and hr_raw 3.030 -> 2.142 (z
    -4.62), with dense no-harm and eff_rank FLAT at 8.637 vs 8.599. InfoNCE keeps
    only what the two views share, so a level-only caption teaches level and discards
    spread by construction -- three arms of Phase-A supervision knobs (v29/v31/v32)
    could not fix that because they never changed what the text view says.\n\nUPDATE
    2026-09-01: THE DEFECT IS FIXED AND THE VERDICT SHOULD BE RE-READ. This was closed
    'inconclusive / test_invalid' because 47/128 dense emissions did not close their
    JSON, making that draw a selected subset. Repaired at DECODE time with no retraining.
    The mechanism matters: the loop always begins at the last schema pair and NEVER
    emits a closing brace at all, so a brace-based stop is a silent no-op -- verified,
    the first attempt returned byte-identical output. Stopping at the first REPEATED
    KEY works, because by then every slot has been emitted exactly once. Re-captured
    on the identical adapters:\n  v33dispersion dense  81/128 -> 128/128 closed, text
    240 -> 182 chars\n  v34nopeak     dense  88/128 -> 128/128 closed, text 226 ->
    189 chars\n  hr_raw AND hr_peak identical on 128/128 shared rows in BOTH arms.\nSo
    the emission numbers reported here were never distorted: the audit parser was
    first-wins and recovered exactly the values the model emitted. 'test_invalid'
    applied to the PARSE HEALTH of the artifact, not to the measurement, and the deployment
    result stands as recorded. The fix removes the promotion blocker, not an error
    in the numbers.\n\nTwo reusable rules, unchanged: (1) when a metric is stuck,
    ask what the training VIEW actually contains before tuning weights or gradients
    on it; (2) eff_rank is a veto threshold, never a direction -- this arm won deployment
    with zero rank movement while v31 lost it at the highest rank ever measured."
  transfers_to:
  - h4-caption-content-not-architecture-the-3306b2
  - the-hr-peak-native-emission-deficit-264f4f
key_numbers:
  dense_hr_peak_mae_v28: 16.674
  dense_hr_peak_mae_v33: 15.88
  dense_hr_raw_dmae_z: 0.66
  dense_hr_raw_mae_v33: 3.076
  dense_parse_fail_count: 47.0
  dense_parse_ok_native: 81.0
  eff_rank: 8.637
  supa_hr_peak_corr_v28: 0.287
  supa_hr_peak_corr_v33: 0.515
  supa_hr_peak_dmae_z: -5.01
  supa_hr_peak_mae_v28: 21.832
  supa_hr_peak_mae_v33: 16.715
  supa_hr_peak_skill_v28: -4.422
  supa_hr_peak_skill_v33: 0.695
  supa_hr_raw_dmae_z: -4.62
  supa_hr_raw_mae_v28: 3.03
  supa_hr_raw_mae_v33: 2.142
observed_effects:
- delta: 0.228
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: -0.888
  diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
- delta: 0.225
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
- delta: 0.038
  diagnostic_id: eiv_effective_rank
  direction: up
parent_experiment_id: exp-v32-detachaffect-20260831
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: true
    eiv_gen_peak_donor_gap_bpm: false
    eiv_gen_peak_skill_bpm: false
    eiv_gen_pred_bucket_entropy: false
provenance: {}
recipe_fingerprint:
  git_ref: d09559a
verdict: inconclusive
---

## Summary
Tests hypothesis [[the-hr-peak-native-emission-deficit-264f4f]].
Branches from [[exp-v32-detachaffect-20260831]].
At commit `d09559a`.

## Measurements
- [[eiv_gen_peak_corr]]: before 0.287 → after 0.515  (Δ +0.228, up ✓ predicted up)
- [[eiv_supabase_seed_band_mae_bpm]]: before 3.03 → after 2.142  (Δ -0.888, down)
- [[eiv_dense128_hr_raw_mae_bpm]]: before 2.851 → after 3.076  (Δ +0.225, up)
- [[eiv_effective_rank]]: before 8.599 → after 8.637  (Δ +0.038, up)

**Verdict:** inconclusive

## Interpretation
**Failure mode:** `test_invalid`

THE CAPTION VIEW WAS THE BINDING CONSTRAINT ON hr_peak, not the encoder. One key (caption_style hr_only -> hr_dispersion) took supabase hr_peak from skill -4.422 to +0.695 (MAE 21.832 -> 16.715, z -5.01) and hr_raw 3.030 -> 2.142 (z -4.62), with dense no-harm and eff_rank FLAT at 8.637 vs 8.599. InfoNCE keeps only what the two views share, so a level-only caption teaches level and discards spread by construction -- three arms of Phase-A supervision knobs (v29/v31/v32) could not fix that because they never changed what the text view says.

UPDATE 2026-09-01: THE DEFECT IS FIXED AND THE VERDICT SHOULD BE RE-READ. This was closed 'inconclusive / test_invalid' because 47/128 dense emissions did not close their JSON, making that draw a selected subset. Repaired at DECODE time with no retraining. The mechanism matters: the loop always begins at the last schema pair and NEVER emits a closing brace at all, so a brace-based stop is a silent no-op -- verified, the first attempt returned byte-identical output. Stopping at the first REPEATED KEY works, because by then every slot has been emitted exactly once. Re-captured on the identical adapters:
  v33dispersion dense  81/128 -> 128/128 closed, text 240 -> 182 chars
  v34nopeak     dense  88/128 -> 128/128 closed, text 226 -> 189 chars
  hr_raw AND hr_peak identical on 128/128 shared rows in BOTH arms.
So the emission numbers reported here were never distorted: the audit parser was first-wins and recovered exactly the values the model emitted. 'test_invalid' applied to the PARSE HEALTH of the artifact, not to the measurement, and the deployment result stands as recorded. The fix removes the promotion blocker, not an error in the numbers.

Two reusable rules, unchanged: (1) when a metric is stuck, ask what the training VIEW actually contains before tuning weights or gradients on it; (2) eff_rank is a veto threshold, never a direction -- this arm won deployment with zero rank movement while v31 lost it at the highest rank ever measured.

Transfers to: [[h4-caption-content-not-architecture-the-3306b2]], [[the-hr-peak-native-emission-deficit-264f4f]]

## Key numbers
- `dense_hr_peak_mae_v28`: 16.674
- `dense_hr_peak_mae_v33`: 15.88
- `dense_hr_raw_dmae_z`: 0.66
- `dense_hr_raw_mae_v33`: 3.076
- `dense_parse_fail_count`: 47
- `dense_parse_ok_native`: 81
- `eff_rank`: 8.637
- `supa_hr_peak_corr_v28`: 0.287
- `supa_hr_peak_corr_v33`: 0.515
- `supa_hr_peak_dmae_z`: -5.01
- `supa_hr_peak_mae_v28`: 21.832
- `supa_hr_peak_mae_v33`: 16.715
- `supa_hr_peak_skill_v28`: -4.422
- `supa_hr_peak_skill_v33`: 0.695
- `supa_hr_raw_dmae_z`: -4.62
- `supa_hr_raw_mae_v28`: 3.03
- `supa_hr_raw_mae_v33`: 2.142

## Artifacts
- `outputs/adapters/v33dispersion_c`
- `logs/v33_driver.log`
- `logs/v33_geometry.json`
- `logs/emission_v33dispersion_supa_summary.json`
- `logs/emission_v33dispersion_dense_summary.json`
- `scratch/_v33_honest_rescore.py`

## Do not retry
- Do NOT promote v33dispersion to the deployment arm until the JSON non-closure is fixed. 47/128 dense emissions never close the object: the model emits a valid prefix then loops '"temp":"normal","state":"flow"' until the token budget ends. A deployment arm that fails to close its JSON on 37% of a draw is not deployable regardless of its MAE.
- Do NOT quote the driver's own dense line (MAE 3.076 skill +3.910 on parse_ok 81) as a like-for-like result. That MAE is over 81 parsed rows while mae_trivial 6.986 is the full 128-row baseline -- the identical 6.986 appearing for both v28 and v33 is the tell. Use scratch/_v33_honest_rescore.py, which recovers truncated emissions by prefix-parsing and recomputes the baseline on the shared row set.
- Do NOT attribute the supabase win to a lucky draw without the paired test: it is z -4.62 on hr_raw and z -5.01 on hr_peak over 200 shared rows, both far outside the registered 0.195 paired sigma.
- Do NOT re-run more Phase-A supervision-weight or detach variants to chase hr_peak. v29/v31/v32 closed that branch; the caption VIEW was the binding constraint, and this arm is the evidence.

## Notes
THE FIRST ARM SINCE THE REBUILD TO BEAT THE INCUMBENT ON DEPLOYMENT, AND THE FIRST EVER TO MAKE hr_peak POSITIVE ON SUPABASE. One key changed: caption_style hr_only -> hr_dispersion.

MECHANISM, as preregistered: InfoNCE retains only what the two views share, so a caption stating only LEVEL teaches level and discards spread by construction. hr_raw was already solved (skill +0.925) while hr_peak sat at skill -4.422 -- worse than answering the median. Giving the text view dispersion gives the contrastive objective something to align spread against.

HONEST RESCORING (scratch/_v33_honest_rescore.py, shared rows, baseline recomputed on those same rows, 4000-sample paired bootstrap):
  SUPABASE, 200 shared, trivial 3.954 / 17.410
    hr_raw  v33 2.142 (skill +1.812, corr 0.893, std_ratio 1.10) vs v28 3.030 (+0.925, 0.793, 0.93) -> dMAE -0.887, sigma 0.192, z -4.62 DEMONSTRATED
    hr_peak v33 16.715 (skill +0.695, corr 0.515, std_ratio 0.88) vs v28 21.832 (-4.422, 0.287, 0.50) -> dMAE -5.117, sigma 1.022, z -5.01 DEMONSTRATED
  DENSE, 128 shared, trivial 6.986 / 17.218
    hr_raw  v33 3.076 vs v28 2.851 -> dMAE +0.225, sigma 0.343, z +0.66, inside noise (no-harm holds)
    hr_peak v33 15.880 vs v28 16.674 -> dMAE -0.794, sigma 0.901, z -0.88, inside noise
Both supabase effects clear k=2 by a wide margin; both dense deltas are inside noise, so the arm buys peak on the deployment draw at no measured dense cost.

GEOMETRY IS FLAT AND THAT IS THE POINT: eff_rank 8.637 vs v28 8.599 (+0.038). The deployment gain came with NO rank movement, which is the fourth independent confirmation that eff_rank is a veto threshold and not a direction -- v31 took rank to 10.106 and lost deployment; this arm left rank alone and won it.

THE DEFECT THAT BLOCKS PROMOTION: 47 of 128 dense emissions never close their JSON. The failure is a repetition loop, not difficulty -- the model emits the full valid object and then repeats '"temp":"normal","state":"flow"' until max_new_tokens. The unparsed rows are not the hard ones (true_mean 76.8 sd 5.7 vs parsed 72.9 sd 10.0 -- if anything they are the EASIER, more central rows), so this is a decode/termination bug, not a capability limit. Every number above survives it because prefix-parsing recovers the emitted values on all 128 rows, but a deployment arm cannot ship emitting unterminated JSON on 37% of a draw.

PROCESS NOTE: this arm was launched at 20:18 by a heartbeat cron session, not by the main session. It was correct in design (one-variable, pre-flight verified, caption sanity checked) but it ran outside any systemd unit, which is what exposed the watchdog's unit-only liveness bug (fixed, commit d09559a).

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
