---
artifacts:
- logs/emission_mc01_supa_summary.json
- logs/emission_mc01_dense_summary.json
- logs/mc01.log
- scratch/run_mc01.sh
- scratch/gen_mc01.py
- outputs/adapters/mc01
closed: true
config_note: 'Runner scratch/run_mc01.sh: warm caches (deterministic, per process-completion-detection
  skill) → a_mc01 (phase_a_12ch_pmdata + min_coverage 0.1) → b_mc01 (phase_b_12ch
  verbatim; its own 0.25 floor already exceeds 0.1) → c_mc01_s42 (band04 law + incumbent
  3-ds dict, = ab_ctrl''s C) → supabase-200 + dense-128 audits batch 1. Generator
  scratch/gen_mc01.py. Comparators on the same draw: ab_ctrl 6.03 (the 0.0 control),
  v9band04 3.04 (healthy anchor), trivial 3.95. Budget ~1.5 h GPU. Single seed 42;
  effect under test ~3 bpm vs family band 0.60.'
cost:
  gpu_hours: 1.5
  wall_hours: 1.5
crash_reason: ''
created_at: '2026-08-20T00:35:30+00:00'
diagnostics_measured:
- after: 11.08
  before: 6.03
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT pursue Phase-A pool composition (min_coverage floor / the 974 sparse windows)
  as the mechanism of the new-corpus-encoder deployment collapse: mc01 (floor restored
  to 0.1, single variable vs ab_ctrl) came out WORSE (11.08 vs 6.03 supabase MAE,
  top1 0.78 attractor). The pool floor is not the lever, and the delta''s sign suggests
  substantial seed-level variance between fresh chains — treat per-chain MAE differences
  under ~5 bpm as unresolved noise in this family until a same-chain seed band is
  measured.'
- 'Do NOT interpret ANY fresh-chain-vs-fresh-chain MAE ordering (ab_ctrl 6.03 vs ab_wl
  20.28 vs mc01 11.08 vs c_patch120 ~24) as a recipe effect: all four are single seeds
  of a family whose seed band is UNMEASURED and evidently large. The only robust contrast
  remains fresh-new-corpus-chain (6-24) vs old-corpus v9band04 (3.04) — every fresh
  chain is broken, by 2-20 bpm, direction consistent.'
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: mc01-discriminator-mechanism-a-of-the-new-5b7727
id: exp-051-mc01-discriminator-mechanism-a-8217fb
interpretation:
  at: '2026-08-20T02:07:34+00:00'
  failure_mode: claim_false
  lesson: 'Mechanism (a) — the 974 sparse min_coverage<0.1 windows in the Phase-A
    pool — is NOT what breaks new-corpus chains: restoring the 0.1 floor made deployment
    WORSE (11.08 vs the 0.0 control''s 6.03), with a fresh 78%-mass attractor. Two
    things learned beyond the rejection: (1) the fresh-chain family''s seed band is
    unmeasured and evidently HUGE (same-code single seeds span 6–11 bpm) — measure
    a same-chain seed pair BEFORE buying the next mechanism arm, or mechanism deltas
    and seed noise are indistinguishable; (2) the only robust contrast remains fresh-new-corpus
    (6–24 bpm) vs old-corpus v9band04 (3.04) — direction consistent across 5 chains.
    Next cheapest probe is CPU: byte-diff captions + cached text embeddings for identical
    windows between v5c-era cache dirs and today''s (mechanisms b/c), no retrain needed.'
  transfers_to:
  - mc01-discriminator-mechanism-a-of-the-new-5b7727
key_numbers:
  ab_ctrl_reference: 6.03
  mc01_dense_corr: 0.677
  mc01_dense_mae: 9.02
  mc01_supa_corr: 0.175
  mc01_supa_mae: 11.08
  mc01_supa_top1_mass: 0.78
  trivial: 3.95
  v9band04_reference: 3.044
observed_effects:
- delta: 5.05
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint:
  interventions: analog-state-supervision-ladder
verdict: refutes
---

## Summary
Tests hypothesis [[mc01-discriminator-mechanism-a-of-the-new-5b7727]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 6.03 → after 11.08  (Δ +5.05, up ✗ predicted down)

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

Mechanism (a) — the 974 sparse min_coverage<0.1 windows in the Phase-A pool — is NOT what breaks new-corpus chains: restoring the 0.1 floor made deployment WORSE (11.08 vs the 0.0 control's 6.03), with a fresh 78%-mass attractor. Two things learned beyond the rejection: (1) the fresh-chain family's seed band is unmeasured and evidently HUGE (same-code single seeds span 6–11 bpm) — measure a same-chain seed pair BEFORE buying the next mechanism arm, or mechanism deltas and seed noise are indistinguishable; (2) the only robust contrast remains fresh-new-corpus (6–24 bpm) vs old-corpus v9band04 (3.04) — direction consistent across 5 chains. Next cheapest probe is CPU: byte-diff captions + cached text embeddings for identical windows between v5c-era cache dirs and today's (mechanisms b/c), no retrain needed.

Transfers to: [[mc01-discriminator-mechanism-a-of-the-new-5b7727]]

## Key numbers
- `ab_ctrl_reference`: 6.03
- `mc01_dense_corr`: 0.677
- `mc01_dense_mae`: 9.02
- `mc01_supa_corr`: 0.175
- `mc01_supa_mae`: 11.08
- `mc01_supa_top1_mass`: 0.78
- `trivial`: 3.95
- `v9band04_reference`: 3.044

## Artifacts
- `logs/emission_mc01_supa_summary.json`
- `logs/emission_mc01_dense_summary.json`
- `logs/mc01.log`
- `scratch/run_mc01.sh`
- `scratch/gen_mc01.py`
- `outputs/adapters/mc01`

## Do not retry
- Do NOT pursue Phase-A pool composition (min_coverage floor / the 974 sparse windows) as the mechanism of the new-corpus-encoder deployment collapse: mc01 (floor restored to 0.1, single variable vs ab_ctrl) came out WORSE (11.08 vs 6.03 supabase MAE, top1 0.78 attractor). The pool floor is not the lever, and the delta's sign suggests substantial seed-level variance between fresh chains — treat per-chain MAE differences under ~5 bpm as unresolved noise in this family until a same-chain seed band is measured.
- Do NOT interpret ANY fresh-chain-vs-fresh-chain MAE ordering (ab_ctrl 6.03 vs ab_wl 20.28 vs mc01 11.08 vs c_patch120 ~24) as a recipe effect: all four are single seeds of a family whose seed band is UNMEASURED and evidently large. The only robust contrast remains fresh-new-corpus-chain (6-24) vs old-corpus v9band04 (3.04) — every fresh chain is broken, by 2-20 bpm, direction consistent.

## Notes
VERDICT BY THE PREREGISTERED DECISION RULE: mc01 MAE 11.08 >= 5.5 ⇒ MECHANISM (a) REJECTED. Restoring the old min_coverage 0.1 pool law at Phase A did not heal the chain — it landed WORSE than the 0.0 control (11.08 vs 6.03), with a fresh attractor (one value on 78% of rows). The 974 sparse windows are not the lever. Two live consequences: (1) proceed to mechanisms (b)/(c) — caption regeneration and text-cache byte-equivalence vs the v5c era — which are CPU-diffable: compare caption text + cached embedding vectors for identical raw windows between the v5c-era cache dirs (.cache/eiv_text_embeddings* pre-2026-08-19 mtimes) and today's; (2) the fresh-chain family evidently has a LARGE unmeasured seed band (three same-code single-seed chains span 6-11 bpm), so the discriminating instrument itself needs a same-chain seed pair before the next mechanism test is bought — otherwise mechanism deltas and seed noise are indistinguishable. Honest summary: the problem stands, mechanism unknown, one candidate eliminated, and the next cheapest probe is a byte-diff, not a retrain.

#deployment-distribution #collapse #denominator #sensor-text #epoch-resolution #state-supervision #wearables
