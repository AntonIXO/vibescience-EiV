---
ambition_class: mechanism
created_at: '2026-08-30T08:35:22+00:00'
former_ids: []
gap_closure_estimate: 0.4
gates:
- blocking: true
  description: 'g1 BLOCKING (the fork): supabase hr_raw corr >= 0.60 AND top1_mass
    <= 0.50. These two together define ''not collapsed to a constant'' -- v11full
    read corr -0.035 with top1 0.805, and corr alone is not enough because a degenerate
    arm can post a small positive correlation while emitting one value on most rows.
    Single seed is sufficient: the C-only band is 0.80 bpm (exp-054) and the effect
    under test is a 2.6+ bpm collapse, ~3x outside it.'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-not-collapsed
  mde: 0.1
- blocking: true
  description: 'g2 BLOCKING (deployment, absolute bar): supabase hr_raw MAE must beat
    the trivial baseline (3.954 on this draw), i.e. skill > 0. v11full scored 5.490
    (skill -1.535). This is an absolute bar and the seed band does not excuse missing
    it.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-beats-trivial
  mde: 0.8
- blocking: false
  description: 'g3 (non-blocking, dense sanity): dense-128 hr_raw MAE. v11full posted
    21.277 against a 7.774 trivial, so any value in the normal 2.6-6.1 range is evidence
    the collapse is gone rather than merely hidden on the sparse population. Non-blocking
    because the dense seed band (1.483-2.261) is wide.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g3-dense-sane
  mde: 1.0
id: the-fresh-chain-collapse-is-not-caused-by-the-37ddda
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'PRIMARY. v11full collapsed to corr -0.035 with top1_mass 0.805
    (one value on 80.5% of rows). Every C-only family off a frozen A/B init in this
    vault is healthy (v9band04 corr 0.68, v10conly 0.739-0.792), and the production-stage
    probe run today shows the v11 prefix carries HR as well as the healthy one (R2
    0.7593 vs 0.7715, corr 0.8793 vs 0.8791). So I predict corr >= 0.60 and a MAE
    in the 2.7-4.0 range. CALIBRATION: I am 3/11 on the supabase MAE diagnostic and
    0.411 overall, and my last two predictions on this exact problem were both wrong
    -- the gates decide, not this.'
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
The fresh-chain collapse is NOT caused by the encoder's content. Training the identical C recipe off the FROZEN v11full/phase_b -- byte-identical profile, same 20-dataset roster, same seed 43, single variable being that the encoder was not retrained in this session -- produces a non-collapsed adapter: supabase hr_raw corr >= 0.60 with top1_mass <= 0.50, versus v11full's corr -0.035 at top1_mass 0.805.

## Rationale
THE ENCODER IS EXONERATED BY DIRECT MEASUREMENT, WHICH NARROWS THIS PROBLEM FOR THE FIRST TIME. Measured today at the PRODUCTION stage (channel_fusion -> cross_modal projector, 1,500 windows / 281 subjects, subject-grouped OOF RidgeCV):
    ab_ctrl_s43/phase_b (behind the only HEALTHY family)  HR R2 +0.7715, corr +0.8791, ratio 0.9147
    v11full/phase_b     (behind a COLLAPSED chain)        HR R2 +0.7593, corr +0.8793, ratio 0.9949
Soft-token norms are equal to three decimals (50.61 vs 50.59). The prefix behind the collapsed chain carries heart rate as well as the prefix behind the healthy one.

An earlier ENCODER-stage probe was even blunter: ab_ctrl_s43 read R2 -0.0269 while v11full read +0.0256 -- the healthy chain's encoder is WORSE on that instrument. Either way, 'Phase A/B produces a dead representation' is dead as an explanation.

TWO MORE CANDIDATES ELIMINATED CHEAPLY BEFORE ANY GPU WAS SPENT:
  * Phase-A hyperparameters: all 20 compared keys are byte-identical between the healthy checkpoint and every collapsed one (uniformity_weight 3.0, temperature 0.03, lr 1e-4, steps 2000, batch 16, mask_ratio 0.15, ...).
  * Caption collisions feeding InfoNCE false negatives: 99.7% of captions are unique in both pools; only 53 of 9,278 windows share a caption.

WHAT REMAINS is the one structural difference between every healthy arm and every collapsed one: healthy arms train C against an encoder FROZEN from a previous session; collapsed arms train C against an encoder written moments earlier in the same chain. Six collapses now span three corpora (12,547 / 13,518 / 14,092) and three rosters (3-ds / 6-ds / 20-ds), so corpus and roster are already varied out. This arm varies the remaining one.

## Plan
SINGLE SEED, C-ONLY, runner scratch/run_v12_conly.sh -> scratch/score_v12.sh.

Profile lora_proxy_12ch_v12conly is byte-identical to lora_proxy_12ch_v11full except its notes -- verified by dict diff, the only differing key is `notes`. It trains off the FROZEN outputs/checkpoints/v11full/phase_b instead of the encoder produced moments earlier in the same chain. That is the single variable.

WHY ONE SEED IS ENOUGH HERE: the C-only re-seed band is 0.80 bpm (exp-054, frozen A/B init) and the failure under test is a 2.6+ bpm collapse to constant output, roughly 3x the band. A seed family is required for within-band comparisons; this is not one.

BOTH OF TODAY'S FIXES ARE CARRIED: the auto-warm path (warm_if_needed.py + the OOM-aware retry in _precompute_missing_text_embeddings, 8 tests) ran unattended at launch and reported RC=0; the window-level state presence mask (mask_unlabelled_state, 12 tests) is inert at Phase C because C does not run the state head, and is carried for the A-stage arm queued behind this.

IF g1 PASSES: the collapse is caused by retraining A/B in the same session as C, not by the encoder's content -- and since the prefix probes equal, the mechanism is something about the freshly-written checkpoint that the frozen path does not reproduce (write/read round-trip, optimiser or RNG state, or the projector weights C inherits). Next step is a byte-level diff of the two load paths, not another training run.
IF g1 FAILS: the encoder is exonerated AND freezing does not help, which leaves the C stage's own interaction with this corpus -- and the next probe is the 20-dataset roster's effect on the C sampler, not the encoder.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-not-collapsed` (blocking): g1 BLOCKING (the fork): supabase hr_raw corr >= 0.60 AND top1_mass <= 0.50. These two together define 'not collapsed to a constant' -- v11full read corr -0.035 with top1 0.805, and corr alone is not enough because a degenerate arm can post a small positive correlation while emitting one value on most rows. Single seed is sufficient: the C-only band is 0.80 bpm (exp-054) and the effect under test is a 2.6+ bpm collapse, ~3x outside it.
Gate `g2-beats-trivial` (blocking): g2 BLOCKING (deployment, absolute bar): supabase hr_raw MAE must beat the trivial baseline (3.954 on this draw), i.e. skill > 0. v11full scored 5.490 (skill -1.535). This is an absolute bar and the seed band does not excuse missing it.
Gate `g3-dense-sane` (advisory): g3 (non-blocking, dense sanity): dense-128 hr_raw MAE. v11full posted 21.277 against a 7.774 trivial, so any value in the normal 2.6-6.1 range is evidence the collapse is gone rather than merely hidden on the sparse population. Non-blocking because the dense seed band (1.483-2.261) is wide.

#deployment-distribution #collapse #baseline #sensor-text #epoch-resolution #matched-baseline #optihealth-eiv
