---
ambition_class: mechanism
created_at: '2026-08-30T16:43:13+00:00'
former_ids: []
gap_closure_estimate: 0.7
gates:
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr >= 0.60 (scored on the current eval_v3_1min
    sha 03d62723, same instrument as v15-v17). The old bundle''s own healthy precedents
    on this recipe class: jsonfit_s42 corr 0.809, v9band04/v10conly 0.74 class.'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-new-encoder-healthy-on-old-bundle
  mde: 0.1
- blocking: true
  description: 'g2 BLOCKING: supabase hr_raw MAE beats trivial 3.954 (skill > 0).'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-beats-trivial
  mde: 0.8
- blocking: false
  description: 'g3 advisory: dense-128 hr_raw MAE < 8.0 (new-eval trivial 6.986).'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-advisory
  mde: 1.0
id: complement-cell-of-the-v17-2x2-the-new-v11full-838f07
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'If the new-corpus encoder is the carrier (v17 PASS branch), v18
    should COLLAPSE (its prefix comes from the same v11full encoder... wait -- no:
    v18 uses the NEW encoder on OLD data, so if the encoder is the carrier v18 collapses;
    if the data path is the carrier v18 is healthy). Prior split evenly with v17;
    the pair is what decides, not either alone. Calibration 0.408, record 1/7. Gates
    decide.'
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
COMPLEMENT CELL of the v17 2x2: the NEW v11full encoder trained C-only on the OLD healthy bundle (data/canonical_1min_conv [10344]) with the byte-identical band04 recipe produces a HEALTHY adapter (supabase hr_raw corr >= 0.60, skill > 0) -- i.e. the collapse does NOT travel with the encoder onto old data; combined with v17 this yields a double dissociation localising the carrier.

## Rationale
Completes the 2x2 opened by exp-v17. Every collapsed arm shares TWO properties at once: a post-rebuild-trained encoder AND post-rebuild C training data. v17 separates them in one direction (old enc x new data); this arm is the complement (new enc x old data). The old bundle data/canonical_1min_conv is the exact tensor the healthy jsonfit_s42 control trained on (corr 0.809 recorded), so 'new encoder x old data' has a healthy same-data precedent to compare against. The corpus-era trace (skill reference canonical-refresh-20260824) brackets the breaking change into [12547]->[13518] (2026-08-24 refresh, stats sha 8498adeb->50e93f6c, +dapper/k_emocon/brain_wearable); the 2x2 tells us WHICH ARTIFACT class carries it forward -- encoder weights or the C-stage draw.

## Plan
SINGLE SEED (profile's 42). Runner scratch/run_v18_oldbundle.sh, queued behind v17 on the GPU (waits for its SCORING DONE). ONE variable vs exp-v16: --tensor-path/--mask-path/--metadata-path point at data/canonical_1min_conv ([10344] -- the bundle jsonfit_s42 trained on healthily), same v11full/phase_b encoder, same byte-untouched band04 profile, same scoring instrument (current eval_v3_1min). Warm cache via warm_if_needed --data-dir. DOUBLE DISSOCIATION READOUT with exp-v17: v17 healthy + v18 collapsed => encoder convicted both directions; v17 collapsed + v18 healthy => C data path convicted; both collapse => two defects; both healthy => interaction only. NOTE: training asserts tensor resolution against the bundle manifest -- canonical_1min_conv is epoch_minutes=1, same T=1440, so no assert should fire; if hr-bucket process-global guard fires (different corpus implies different cut points), the run dies loudly at init, which is a clean abort not a confound.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-new-encoder-healthy-on-old-bundle` (blocking): g1 BLOCKING: supabase hr_raw corr >= 0.60 (scored on the current eval_v3_1min sha 03d62723, same instrument as v15-v17). The old bundle's own healthy precedents on this recipe class: jsonfit_s42 corr 0.809, v9band04/v10conly 0.74 class.
Gate `g2-beats-trivial` (blocking): g2 BLOCKING: supabase hr_raw MAE beats trivial 3.954 (skill > 0).
Gate `g3-dense-advisory` (advisory): g3 advisory: dense-128 hr_raw MAE < 8.0 (new-eval trivial 6.986).

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
