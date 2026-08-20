---
artifacts:
- logs/emission_ab_ctrl_supa_summary.json
- logs/emission_ab_ctrl_dense_summary.json
- logs/emission_ab_wl_supa_summary.json
- logs/emission_ab_wl_dense_summary.json
- logs/allowlist_ab_resume.log
- scratch/run_allowlist_ab_resume.sh
- scratch/gen_allowlist_ab.py
- outputs/adapters/ab_ctrl
- outputs/adapters/ab_wl
closed: true
config_note: 'Runner scratch/run_allowlist_ab.sh (chained: warm 4 text caches with
  empty GPU per the process-completion-detection skill → CONTROL A→B→C → REPAIRED
  A→B→C → 4 emission audits batch 1 on the rebuilt eval draw). Profiles: control =
  phase_a_12ch_pmdata/phase_b_12ch/c_ab_incumbent_s42 (incumbent 3-dataset C dict,
  unit weights); repaired = phase_a_12ch_1min_wl/phase_b_12ch_wl/c_ab_wl_s42. Both
  C arms carry the settled band04 augment law (hr_augment_band_prob 0.5, min_mean
  45) — generator scratch/gen_allowlist_ab.py. Corpus [12547,1440,12] ac7d2b1c; eval
  draw = rebuilt data/eval_v3_1min (strict-clean 6496 of 12547; supabase pool 230;
  trivial median baseline recomputed on capture = 3.95). Comparators on the SAME draw
  measured this session: v9band04_s42 supabase MAE 3.044 skill +0.91 corr 0.68 / dense
  MAE 3.20 corr 0.916; c_patch120 failure reference (35.1-attractor). Budget ~2.5
  h GPU (2 full chains + 4 generation-bound audits). Seed 42 single-seed: all deltas
  priced against the 0.60 bpm family band; bar-relative claims only.'
cost:
  gpu_hours: 3.2
  wall_hours: 3.3
crash_reason: ''
created_at: '2026-08-19T21:24:11+00:00'
diagnostics_measured:
- after: 20.28
  before: 3.044
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
do_not_retry:
- 'Do NOT re-run the allowlist A/B as a same-session fresh-chain design expecting
  v9band04-level numbers: BOTH arms trained this session (fresh A->B->C, 12,547 corpus,
  band04 law, seed 42) land far below the trivial baseline (ctrl 6.03 / wl 20.28 vs
  trivial 3.95) while v9band04 - whose ENCODER comes from the old corpus''s v5c phase_b
  - scores 3.04 on the same draw. Every fresh chain trained on the new corpus today
  (c_patch120 x2, ab_ctrl, ab_wl) is deployment-broken regardless of allowlist or
  state heads; the common factor is the NEW-CORPUS A/B ENCODER feeding C. Investigate
  that (new problem needed: caption law / population-stat shift / min_coverage-0.0
  training pool composition at Phase A/B) before buying any more C stages off new-corpus
  encoders.'
- 'Do NOT read the wl arm''s 198/200 ''stress'' state emissions as state detection:
  it is a collapsed prior (ctrl emits 200/200 neutral on the same rows). Both are
  attractor behaviour, not conditioning.'
external_run: ''
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: allowlist-a-b-full-chain-executes-runbook-dc0de8
id: exp-050-allowlist-a-b-full-chain-a44bb0
interpretation:
  at: '2026-08-20T00:33:11+00:00'
  failure_mode: recipe_conditional
  lesson: 'The allowlist question was ASKED ON A BROKEN SUBSTRATE and the experiment
    accidentally isolated a bigger variable: every A/B encoder trained on the 12,547
    corpus this session produces a deployment-broken C (fresh chains: 6.03/20.28/23.6/24.6
    MAE vs trivial 3.95), while v9band04 — identical C recipe family, OLD-corpus v5c
    encoder — scores 3.04 on the same draw. The allowlist deltas (ctrl 6.03 beats
    wl 20.28, inverted vs prediction) are readable only as ''which arm is less broken''.
    Until a fresh new-corpus A→B→C chain reproduces v9band04-level deployment MAE,
    no C-stage experiment on new-corpus encoders is interpretable — the follow-up
    problem is WHY new-corpus Phase A/B degrades the prefix (candidates: min_coverage-0.0
    pool shift at A/B, caption regeneration, byte-inequivalent text caches, population-stat
    drift). The state-slot collapse (never flow, prior-only) reproduces in all fresh
    chains.'
  transfers_to:
  - allowlist-a-b-full-chain-executes-runbook-dc0de8
key_numbers:
  ab_ctrl_dense_mae: 10.05
  ab_ctrl_supa_corr: 0.357
  ab_ctrl_supa_mae: 6.03
  ab_ctrl_supa_skill: -2.08
  ab_wl_dense_mae: 21.69
  ab_wl_supa_mae: 20.28
  ab_wl_supa_skill: -16.33
  trivial_new_draw: 3.95
  v9band04_same_draw_mae: 3.044
observed_effects:
- delta: 17.236
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_bucket_acc_over_majority: false
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint:
  interventions: dataset-allowlist-repaired-all-phases
verdict: refutes
---

## Summary
Tests hypothesis [[allowlist-a-b-full-chain-executes-runbook-dc0de8]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 3.044 → after 20.28  (Δ +17.236, up ✗ predicted down)

**Verdict:** refutes

## Interpretation
**Failure mode:** `recipe_conditional`

The allowlist question was ASKED ON A BROKEN SUBSTRATE and the experiment accidentally isolated a bigger variable: every A/B encoder trained on the 12,547 corpus this session produces a deployment-broken C (fresh chains: 6.03/20.28/23.6/24.6 MAE vs trivial 3.95), while v9band04 — identical C recipe family, OLD-corpus v5c encoder — scores 3.04 on the same draw. The allowlist deltas (ctrl 6.03 beats wl 20.28, inverted vs prediction) are readable only as 'which arm is less broken'. Until a fresh new-corpus A→B→C chain reproduces v9band04-level deployment MAE, no C-stage experiment on new-corpus encoders is interpretable — the follow-up problem is WHY new-corpus Phase A/B degrades the prefix (candidates: min_coverage-0.0 pool shift at A/B, caption regeneration, byte-inequivalent text caches, population-stat drift). The state-slot collapse (never flow, prior-only) reproduces in all fresh chains.

Transfers to: [[allowlist-a-b-full-chain-executes-runbook-dc0de8]]

## Key numbers
- `ab_ctrl_dense_mae`: 10.05
- `ab_ctrl_supa_corr`: 0.357
- `ab_ctrl_supa_mae`: 6.03
- `ab_ctrl_supa_skill`: -2.08
- `ab_wl_dense_mae`: 21.69
- `ab_wl_supa_mae`: 20.28
- `ab_wl_supa_skill`: -16.33
- `trivial_new_draw`: 3.95
- `v9band04_same_draw_mae`: 3.044

## Artifacts
- `logs/emission_ab_ctrl_supa_summary.json`
- `logs/emission_ab_ctrl_dense_summary.json`
- `logs/emission_ab_wl_supa_summary.json`
- `logs/emission_ab_wl_dense_summary.json`
- `logs/allowlist_ab_resume.log`
- `scratch/run_allowlist_ab_resume.sh`
- `scratch/gen_allowlist_ab.py`
- `outputs/adapters/ab_ctrl`
- `outputs/adapters/ab_wl`

## Do not retry
- Do NOT re-run the allowlist A/B as a same-session fresh-chain design expecting v9band04-level numbers: BOTH arms trained this session (fresh A->B->C, 12,547 corpus, band04 law, seed 42) land far below the trivial baseline (ctrl 6.03 / wl 20.28 vs trivial 3.95) while v9band04 - whose ENCODER comes from the old corpus's v5c phase_b - scores 3.04 on the same draw. Every fresh chain trained on the new corpus today (c_patch120 x2, ab_ctrl, ab_wl) is deployment-broken regardless of allowlist or state heads; the common factor is the NEW-CORPUS A/B ENCODER feeding C. Investigate that (new problem needed: caption law / population-stat shift / min_coverage-0.0 training pool composition at Phase A/B) before buying any more C stages off new-corpus encoders.
- Do NOT read the wl arm's 198/200 'stress' state emissions as state detection: it is a collapsed prior (ctrl emits 200/200 neutral on the same rows). Both are attractor behaviour, not conditioning.

## Notes
VERDICT BY THE PREREGISTERED PREDICTIONS: REFUTES. (1) FAIL - REPAIRED does not beat trivial (20.28 vs 3.95) nor land near v9band04's 3.04. (2) FAIL, INVERTED - CONTROL (6.03) beats REPAIRED (20.28) by 14 bpm; the direction was predicted the other way. (3) MIXED - no >50% single-value attractor on wl supa (top1 0.290) but ctrl supa sits at 0.440 and wl dense at 0.500, borderline attractor behaviour; state slot fully collapsed in both arms (ctrl 200/200 neutral, wl 198/200 stress). (4) moot - arms are 14 bpm apart, far outside the 0.60 band.

THE FINDING THAT MATTERS IS NOT THE ALLOWLIST. Meta-analysis across today's four fresh chains, all on the same corpus/draw/law: c_patch120_s42 MAE 23.6, c_patch120_s43 24.6, ab_wl 20.28, ab_ctrl 6.03 - versus v9band04 (old-corpus v5c encoder, same C recipe family, same draw) at 3.04. EVERY chain whose A/B encoder was trained on the 12,547 corpus THIS SESSION is deployment-broken; the only healthy adapter rides an OLD-corpus encoder. The allowlist experiment accidentally isolated a bigger variable: something in the new-corpus Phase A/B training (not the C recipe - band04 law was held; not the corpus tensor - v9band04 reads the same eval rows fine) degrades the soft-token prefix for deployment-population emission. Candidates for the follow-up problem: (a) Phase A/B trained at min_coverage 0.0 now includes 974 sparse windows that shift the InfoNCE population; (b) population_stats recomputation (0.2-0.6% shifts) interacting with caption text; (c) hierarchical-caption text cache regenerated on the new corpus (captions numerically differ); (d) the deterministic-caption warm bug meant ab arms' B stages trained on a fresh in-process precompute - byte-equivalence with v5c's cache unverified.

WHAT SURVIVES: ctrl arm (10-dataset A/B + 3-dataset C, the historical mismatch) is the LEAST broken fresh chain (6.03, corr 0.357, dense corr 0.708) - the mismatch-costs story is refuted at this scale; state emission collapse reproduces across all fresh chains (never flow).

#dataset-whitelist #deployment-distribution #sensor-text #epoch-resolution #wearables #baseline
