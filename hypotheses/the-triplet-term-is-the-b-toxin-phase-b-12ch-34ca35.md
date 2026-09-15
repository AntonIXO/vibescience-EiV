---
ambition_class: mechanism
created_at: '2026-08-31T00:05:31+00:00'
former_ids: []
gap_closure_estimate: 0.5
gates:
- blocking: true
  description: 'g1 BLOCKING: supabase hr_raw corr in the B-free class (>= 0.55). Comparators,
    same A and C throughout: v21 (B, triplet 0.05) = 0.327; v23 (no B) = 0.567; v17
    (old-corpus B) = 0.583. PASS -> the triplet term is the B toxin and B''s alignment
    value is recoverable without it. FAIL at ~0.33 -> the toxin is elsewhere in B
    (InfoNCE-on-B, projector drift).'
  diagnostic_id: eiv_gen_peak_corr
  id: g1-triplet-off-restores-b
  mde: 0.1
- blocking: false
  description: 'g2 advisory: MAE beats v23''s 5.414 -- i.e. B-without-triplet ADDS
    value over skipping B (the alignment term earns its place).'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-b-adds-value-over-no-b
  mde: 0.8
id: the-triplet-term-is-the-b-toxin-phase-b-12ch-34ca35
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: 'The triplet stage is the only B-specific objective the C-stage
    never sees; the vault already holds structural indictments of its bucketing (one-line-label-swap
    rule-out: whole-window negatives from a different window; E25 degeneracy). Prior
    ~50%. Calibration 0.408, gates decide.'
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags:
- matched-baseline
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- collapse
- deployment-distribution
- sensor-text
- contrastive
- epoch-resolution
---

## Statement
THE TRIPLET TERM IS THE B TOXIN: phase_b_12ch_v24tripoff (triplet_weight 0.0, one variable vs v21's B) off the same v20strat A, followed by the same band04 C, lands in the B-free class (supabase corr >= 0.55) where the triplet-carrying B read 0.327.

## Rationale
exp-v23 convicted the fresh Phase B (-0.240 corr, identical A/C). The B recipe has exactly two trainable objectives: InfoNCE alignment (shared with A, exonerated there) and the auxiliary triplet orthogonalization (B-only). v21's B already ran with the state presence guards on, so the fabricated-neutral story is dead for B; the triplet term itself is the remaining single lever, and its bucketing has standing structural indictments in the vault. triplet_weight 0.0 is the one-variable test.

## Plan
One variable vs exp-v21's B stage: profile phase_b_12ch_v24tripoff = phase_b_12ch_v11full with triplet_weight 0.05 -> 0.0 (dict diff verified). Same v20strat A, same band04 C, same scoring. Runner scratch/run_v24_btripoff.sh (~10 min B + ~40 min C + ~20 min scoring). Rationale: v21's B carried mask_unlabelled_state=True and triplet_require_state_presence=True yet still degraded C by -0.240 corr, so the fabricated-neutral account is already dead for B; the triplet term ITSELF (whole-window anchors bucketed by argmax state, E25/E26 record: 18% of windows carry transitions, buckets built on infer_state_label) is the next single lever. If triplet-off restores the B-free class, Phase B returns to the recipe as pure InfoNCE alignment.

## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Predicts [[eiv_gen_peak_corr]] (up).
Gate `g1-triplet-off-restores-b` (blocking): g1 BLOCKING: supabase hr_raw corr in the B-free class (>= 0.55). Comparators, same A and C throughout: v21 (B, triplet 0.05) = 0.327; v23 (no B) = 0.567; v17 (old-corpus B) = 0.583. PASS -> the triplet term is the B toxin and B's alignment value is recoverable without it. FAIL at ~0.33 -> the toxin is elsewhere in B (InfoNCE-on-B, projector drift).
Gate `g2-b-adds-value-over-no-b` (advisory): g2 advisory: MAE beats v23's 5.414 -- i.e. B-without-triplet ADDS value over skipping B (the alignment term earns its place).

#collapse #deployment-distribution #sensor-text #contrastive #epoch-resolution #matched-baseline #optihealth-eiv
