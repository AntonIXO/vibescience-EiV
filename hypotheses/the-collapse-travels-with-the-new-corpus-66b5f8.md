---
ambition_class: mechanism
created_at: '2026-08-30T16:22:12+00:00'
former_ids: []
gap_closure_estimate: 0.7
gates:
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr >= 0.60. This encoder''s OWN C-only
    family was healthy on the old corpus (v10conly 2.836/0.739 class) and this exact
    checkpoint reads the new eval at +0.760 inference. If C-only training on the new
    bundle off THIS encoder stays >= 0.60, the new-corpus A/B ENCODER is convicted
    as the carrier; if it collapses, the encoder is exonerated fully and the C-stage
    data path (tensor/draw/collator) is the only remaining site.'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-old-encoder-trains-healthy-on-new-corpus
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
id: the-collapse-travels-with-the-new-corpus-66b5f8
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'The 2x2 logic: v12 froze the NEW encoder and C still collapsed,
    so ''encoder content'' was refuted for the FROZEN case -- but every collapsed
    arm still used a NEW-CORPUS-TRAINED encoder''s soft tokens as the prefix. Linear
    HR probes read those tokens fine (R2 0.759), but a probe is not a CLM gradient:
    the tokens could carry HR linearly while their DISTRIBUTION (norms, anisotropy,
    within-batch geometry) makes prefix-reading a harder optimisation than constant-emission.
    My prior: ~50/50, genuinely. 1/7 record on this problem, calibration 0.408. Gates
    decide.'
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
The collapse travels with the NEW-CORPUS-TRAINED ENCODER, not with the C-stage data: training the byte-identical healthy band04 recipe C-only off the OLD healthy ab_ctrl_s43/phase_b encoder on the CURRENT fixed [14041] bundle passes the healthy gates (supabase hr_raw corr >= 0.60, skill > 0), where the identical arm off the v11full encoder read corr 0.231 / skill -6.1.

## Rationale
exp-v16 eliminated the label-side merger defects: the ddb7498 bundle passes validate cleanly and the healthy recipe still collapses (corr 0.231, top1 0.810) -- 11/11. The prefix-statistics diff (scratch/_prefix_diff_old_vs_new.py) found the C-pool z-distributions nearly identical between the old healthy bundle and the current one (hr_mean pres 92.8%->80.5%, z_p50 0.01->0.02, std 0.75->0.75; only respiratory_rate moved, median 14.8->19.0 raw, at 10% presence). So the DATA the C stage draws looks the same in z-space -- what differs in every collapsed arm is the ENCODER whose soft tokens form the prefix: all eleven collapses used encoders A/B-trained on post-rebuild corpora. The exp-v12 'encoder is clean' verdict tested freezing the NEW encoder, not substituting the OLD one -- the 2x2 has an untested cell, and this arm is it.

## Plan
SINGLE SEED (profile's 42). Runner scratch/run_v17_oldenc.sh = run_v16_fixed.sh with ONE variable vs exp-v16: --encoder-checkpoint outputs/checkpoints/ab_ctrl_s43/phase_b (the OLD-corpus healthy encoder, arch-identical: input_dim 24, d_model 192, PL120/ST60) instead of v11full/phase_b. Same byte-untouched band04 profile, same fixed [14041] bundle, same rebuilt eval (sha 03d62723), same scoring. This fills the missing 2x2 cell: old-enc x old-corpus = healthy (recorded), new-enc x new-corpus = collapsed (v15/v16), old-enc x new-corpus = THIS ARM. CAVEAT noted: respiratory_rate z-space moved between corpora (median 14.8 -> 19.0), so the old encoder sees resp shifted ~ -1 sigma at 10% presence -- small, named, and if g1 fails marginally this is the first suspect to check before concluding. Projector dimension-mismatch path: Phase A/B checkpoint trained at same d_llm, no reinit expected; verify in the phase_c log that no projector reinit was reported.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-old-encoder-trains-healthy-on-new-corpus` (blocking): g1 BLOCKING: supabase hr_raw corr >= 0.60. This encoder's OWN C-only family was healthy on the old corpus (v10conly 2.836/0.739 class) and this exact checkpoint reads the new eval at +0.760 inference. If C-only training on the new bundle off THIS encoder stays >= 0.60, the new-corpus A/B ENCODER is convicted as the carrier; if it collapses, the encoder is exonerated fully and the C-stage data path (tensor/draw/collator) is the only remaining site.
Gate `g2-beats-trivial` (blocking): g2 BLOCKING: supabase hr_raw MAE beats trivial 3.954 (skill > 0).
Gate `g3-dense-advisory` (advisory): g3 advisory: dense-128 hr_raw MAE < 8.0 (new-eval trivial 6.986).

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
