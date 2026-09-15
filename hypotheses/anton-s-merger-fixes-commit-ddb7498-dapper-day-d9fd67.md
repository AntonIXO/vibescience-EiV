---
ambition_class: mechanism
created_at: '2026-08-30T15:19:44+00:00'
former_ids: []
gap_closure_estimate: 0.9
gates:
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr >= 0.60 on the rebuilt eval (sha
    03d62723, 230 supabase strict-clean rows source). On the broken bundle this exact
    recipe read 0.355 with top1 0.805; healthy historical range is 0.74-0.81.'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-fixed-bundle-heals-training
  mde: 0.1
- blocking: true
  description: 'g2 BLOCKING: supabase hr_raw MAE beats the trivial (skill > 0). Broken-bundle
    v15 read skill -10.313; healthy family 2.692-2.836 MAE.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-beats-trivial
  mde: 0.8
- blocking: false
  description: 'g3 advisory: dense-128 hr_raw MAE < 8.0 (new-bundle trivial to be
    read from the capture). Broken-bundle arms sat at 21-25.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-advisory
  mde: 1.0
id: anton-s-merger-fixes-commit-ddb7498-dapper-day-d9fd67
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'PRIMARY. If the merger defects (DAPPER 16:00 anchor, 4,184 cross-window
    duplicate label-epochs, 8,580 fabricated stress_level, WESAD transients, DREAMT
    contradictions) were what made every post-rebuild bundle un-trainable, this arm
    returns to the healthy 0.74-0.81 corr class. My record on this problem is 1/6
    and overall calibration 0.408. I note the mechanism is NOT yet named: none of
    the five fixed defect classes has an obvious path into the C-stage PREFIX (they
    are label-side), while the v15 lesson localised the cause in the prefix distribution
    -- so my honest prior is only ~45% PASS, and a FAIL would be as informative: it
    would say the breaking change predates ddb7498''s label fixes and lives in the
    tensor/normalization path.'
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
Anton's merger fixes (commit ddb7498: DAPPER day-anchoring, cross-window label dedup, fabricated-stress removal, WESAD transient trim, DREAMT contradiction fix) restore Phase-C trainability: the byte-identical healthy band04 recipe, C-only off the same frozen v11 encoder, trained on the FIXED [14041] bundle passes the healthy gates -- supabase hr_raw corr >= 0.60 and skill > 0 -- where the identical arm on the broken [14092] bundle read corr 0.355 / skill -10.3.

## Rationale
exp-v15 closed the disjunction on the corpus side: the byte-identical healthy recipe collapses when TRAINED on b659855 while the same bundle reads fine at inference. Anton then landed ddb7498: merger fixes verified on the rebuilt bundle (validate: no findings; DAPPER anchored 00:00, zero duplicate label-epochs, zero fabricated stress_level, WESAD transient 1016->678, DREAMT contradictions 0) plus a 2.8x label extraction tranche (163k->452k label-epochs, 19 new channels, deliberately headless for now). Corpus is [14041,1440,12], 1620 subjects, splits GROWN not moved, normalization sha 24e731a63c97. This arm re-runs the exact v15 design on the fixed bundle -- the preregistered next step written into the v15 interpretation and the problem synthesis.

## Plan
SINGLE SEED (profile's own 42). Runner scratch/run_v16_fixed.sh = run_v15_exact.sh with only artifact paths renamed (adapter outputs/adapters/v16fixed_c, logs v16_*) so v15's broken-bundle artifacts stay comparable; profile lora_proxy_12ch_wl_band04 BYTE-UNTOUCHED, frozen encoder outputs/checkpoints/v11full/phase_b unchanged. Eval bundle rebuilt from the fixed corpus BEFORE launch (scratch/build_eval_v3_1min.py: 6918 rows, 519 subjects, tensor sha 03d62723..., now includes dapper 406 rows). Test suite green on the new bundle (972 passed). Scoring identical instrument. CAVEATS carried from Anton's notes: stai_state pools STAI-6 and STAI-20 by declared range (score against a within-dataset null only); brain_wearable_monitoring stress_level still dropped for 4/16 subjects (in manifest dropped_columns). NOTE the comparison caveat: the eval bundle changed with the corpus (as it must), so v15-vs-v16 varies {bundle, eval draw} together; the healthy-reference anchor (ab_ctrl on the new eval) can be re-run if g1 lands ambiguous.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-fixed-bundle-heals-training` (blocking): g1 BLOCKING: supabase hr_raw corr >= 0.60 on the rebuilt eval (sha 03d62723, 230 supabase strict-clean rows source). On the broken bundle this exact recipe read 0.355 with top1 0.805; healthy historical range is 0.74-0.81.
Gate `g2-beats-trivial` (blocking): g2 BLOCKING: supabase hr_raw MAE beats the trivial (skill > 0). Broken-bundle v15 read skill -10.313; healthy family 2.692-2.836 MAE.
Gate `g3-dense-advisory` (advisory): g3 advisory: dense-128 hr_raw MAE < 8.0 (new-bundle trivial to be read from the capture). Broken-bundle arms sat at 21-25.

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
