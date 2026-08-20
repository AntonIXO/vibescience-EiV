---
ambition_class: null
created_at: '2026-08-20T03:33:21+00:00'
former_ids: []
gap_closure_estimate: null
gates: []
id: ctrl-chain-seed-family-seeds-44-45-added-to-5b95e9
interventions:
- dataset-allowlist-repaired-all-phases
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_seed_band_mae_bpm
  direction: down
  magnitude_note: ''
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
problem_tags: []
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- deployment-distribution
- collapse
- baseline
- control
- sensor-text
- epoch-resolution
---

## Statement
CTRL-CHAIN SEED FAMILY (seeds 44/45 added to existing 42/43). The new-corpus full-chain family (phase_a_12ch_pmdata → phase_b_12ch → band04-C with incumbent 3-ds dict, A/B retrained per seed) is BISTABLE with a chain-level seed band far exceeding the v9band04 family's 0.60 bpm — because it re-rolls A+B+C basins per seed while v9band04's family shared one frozen v5c Phase-B init. PREREGISTERED READINGS on the 4-seed set {42: 6.03, 43: 2.84, 44: ?, 45: ?}: (1) the band (max-min) exceeds 2 bpm — confirming chain-level bistability as the family property; (2) at least 2 of 4 seeds land <= 3.5 MAE — confirming the new corpus supports v9band04-grade deployment chains (the corpus-exoneration claim of rule-out 8e8d8e generalizes beyond one lucky seed); (3) attractor check per seed (no emitted value > 50% of rows on healthy seeds). FALSIFIERS: if 44 and 45 both land > 5.5, s43 was the outlier and the family median is genuinely degraded vs v9band04 — the corpus-defect framing partially revives (as a median shift, not a categorical break); if the band < 1 bpm, the bistability claim dies and s42-vs-s43 needs a different explanation (nondeterminism audit).

## Rationale


## Plan


## Links
Tests problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
Applies [[dataset-allowlist-repaired-all-phases]].
Predicts [[eiv_supabase_seed_band_mae_bpm]] (down).

#deployment-distribution #collapse #baseline #control #sensor-text #epoch-resolution
