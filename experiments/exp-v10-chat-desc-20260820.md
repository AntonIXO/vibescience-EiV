---
artifacts:
- outputs/adapters/c_v10_chat_factual_s43
- outputs/adapters/c_v10_chat_coach_s43
- logs/emission_ab_ctrl_s43_canonical_anystyle.json
- logs/emission_c_v10_chat_factual_s43_supa.json
- logs/emission_c_v10_chat_coach_s43_supa.json
- logs/desc_vs_json_composition.json
closed: true
config_note: 'v10conly chat description types. Frozen B: outputs/checkpoints/ab_ctrl_s43/phase_b.
  Profiles: scratch/v10_chat_desc.yaml (c_v10_chat_factual_s43, c_v10_chat_coach_s43).
  yaml-diff vs c_ctrl_s43: ONLY target_style (+ notes). Json control already on disk:
  outputs/adapters/ab_ctrl_s43 supabase MAE 2.84, family band 0.80, bar 3.64, trivial
  3.95. Runner: scratch/run_v10_chat_desc.sh. Logs: logs/c_v10_chat_factual_s43.log
  then logs/c_v10_chat_coach_s43.log. Scoring: scratch/emission_audit_anystyle.py
  --bundle data/canonical --dataset optihealth_supabase --n 200. Tests: tests/test_chat_coach_target.py
  23 passed before launch.'
cost:
  gpu_hours: 2.2
  wall_hours: 2.1
crash_reason: ''
created_at: '2026-08-20T09:52:07+00:00'
diagnostics_measured:
- after: 5.5962
  before: 5.1222
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
- after: 0.0
  before: 0.0
  diagnostic_id: eiv_supabase_floor_emission_rate
do_not_retry:
- 'Do not retrain chat_coach on v10conly at batch 2: OOM at step-0 backward (9.83
  GiB, +1.11 needed) because coaching sentences lengthen the CLM sequence.'
- 'Do not retry chat_coach as a product surface on this recipe: collapsed to 51.4
  bpm on 172/197 (top1 0.873), MAE 7.77 vs trivial 6.63, skill -1.15. The coaching
  carrier (mechanism+action) costs grounding.'
- 'Do not compare canonical-draw MAE to eval_v3_1min json 2.84: different pool (302
  vs 230), different trivial (6.63 vs 3.95), zero overlapping idxs.'
external_run: ''
former_ids: []
gate_results:
- evidence: Matched canonical draw (pool 302, overlap 200/200). Json MAE 5.122, factual
    5.596, delta +0.474 < mde 0.80. The written 3.64 bar used eval_v3 json 2.84 (trivial
    3.95) which is a different population (canonical trivial 6.63). Scoring against
    3.64 is a pool-shift; the committed MDE 0.80 on the matched control is the comparable
    bar. Factual 5.596 <= 5.122+0.80=5.922.
  gate_id: gate-factual-within-v10-band
  passed: true
- evidence: Coach MAE 7.774 vs matched json 5.122, delta +2.65 >> 0.80. Distinct 11,
    top1 51.4 on 172/197 (mass 0.873). Loses to trivial 6.63 (skill -1.15). Collapsed.
  gate_id: gate-coach-within-v10-band
  passed: false
- evidence: Factual 5.596 < trivial 6.628 PASS. Coach 7.774 > 6.628 FAIL. Gate required
    BOTH.
  gate_id: gate-beats-trivial
  passed: false
- evidence: extract/parse 1.000 on json, factual, coach (197/200 finite true_mean).
  gate_id: gate-extract-rate
  passed: true
- evidence: '|coach-factual| MAE = |7.774-5.596|=2.178 > 0.80. Coaching sentences
    cost grounding: collapse to 51.4 bpm.'
  gate_id: gate-coaching-cost
  passed: false
git_ref: ''
hypothesis_id: on-the-v10conly-recipe-frozen-ab-ctrl-s43-phase-38e6bc
id: exp-v10-chat-desc-20260820
interpretation:
  at: '2026-08-20T12:03:59+00:00'
  failure_mode: recipe_conditional
  lesson: On the matched canonical supabase draw, FACTUAL description is a powered
    null vs json (MAE 5.60 vs 5.12, delta +0.47 inside the 0.80 C-only band). COACH
    collapsed (7.77, 172/197 emit 51.4, skill -1.15 vs trivial 6.63). The 2.84 json
    figure is eval_v3_1min (pool 230, trivial 3.95), not this pool (302, trivial 6.63)
    — do not cite it as 'json is much easier'. JSON is structurally easier because
    (1) value-token share 0.334 vs factual 0.228 vs coach 0.141 vs clinical 0.000,
    and (2) @@ after a quote tokenizes standalone 2/2 while prose after a space is
    1/2 — the old toggle desynced 100% of prose rows. Anton wanted descriptive not
    coach; next arm is clinical description with @@ on the numbers, not more coaching.
  transfers_to:
  - phase-c-can-be-trained-to-emit-conversational-0dcbc0
key_numbers:
  coach_mae: 7.7737
  coach_skill: -1.1461
  coach_top1_mass: 0.873
  factual_mae: 5.5962
  json_canonical_mae: 5.1222
  json_eval_v3_mae: 2.8361
  mae_delta_factual_minus_json: 0.474
  trivial_canonical: 6.6275
observed_effects:
- delta: 0.474
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: up
- delta: 0.0
  diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_supabase_floor_emission_rate: true
    eiv_supabase_hr_raw_mae_bpm: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[on-the-v10conly-recipe-frozen-ab-ctrl-s43-phase-38e6bc]].

## Measurements
- [[eiv_supabase_hr_raw_mae_bpm]]: before 5.1222 → after 5.5962  (Δ +0.474, up ✗ predicted down)
- [[eiv_supabase_floor_emission_rate]]: before 0.0 → after 0.0  (Δ +0, flat ✓ predicted none)

## Gates
- `gate-factual-within-v10-band`: PASS — Matched canonical draw (pool 302, overlap 200/200). Json MAE 5.122, factual 5.596, delta +0.474 < mde 0.80. The written 3.64 bar used eval_v3 json 2.84 (trivial 3.95) which is a different population (canonical trivial 6.63). Scoring against 3.64 is a pool-shift; the committed MDE 0.80 on the matched control is the comparable bar. Factual 5.596 <= 5.122+0.80=5.922.
- `gate-coach-within-v10-band`: **FAIL** — Coach MAE 7.774 vs matched json 5.122, delta +2.65 >> 0.80. Distinct 11, top1 51.4 on 172/197 (mass 0.873). Loses to trivial 6.63 (skill -1.15). Collapsed.
- `gate-beats-trivial`: **FAIL** — Factual 5.596 < trivial 6.628 PASS. Coach 7.774 > 6.628 FAIL. Gate required BOTH.
- `gate-extract-rate`: PASS — extract/parse 1.000 on json, factual, coach (197/200 finite true_mean).
- `gate-coaching-cost`: **FAIL** — |coach-factual| MAE = |7.774-5.596|=2.178 > 0.80. Coaching sentences cost grounding: collapse to 51.4 bpm.

**Verdict:** refutes

## Interpretation
**Failure mode:** `recipe_conditional`

On the matched canonical supabase draw, FACTUAL description is a powered null vs json (MAE 5.60 vs 5.12, delta +0.47 inside the 0.80 C-only band). COACH collapsed (7.77, 172/197 emit 51.4, skill -1.15 vs trivial 6.63). The 2.84 json figure is eval_v3_1min (pool 230, trivial 3.95), not this pool (302, trivial 6.63) — do not cite it as 'json is much easier'. JSON is structurally easier because (1) value-token share 0.334 vs factual 0.228 vs coach 0.141 vs clinical 0.000, and (2) @@ after a quote tokenizes standalone 2/2 while prose after a space is 1/2 — the old toggle desynced 100% of prose rows. Anton wanted descriptive not coach; next arm is clinical description with @@ on the numbers, not more coaching.

Transfers to: [[phase-c-can-be-trained-to-emit-conversational-0dcbc0]]

## Key numbers
- `coach_mae`: 7.7737
- `coach_skill`: -1.1461
- `coach_top1_mass`: 0.873
- `factual_mae`: 5.5962
- `json_canonical_mae`: 5.1222
- `json_eval_v3_mae`: 2.8361
- `mae_delta_factual_minus_json`: 0.474
- `trivial_canonical`: 6.6275

## Artifacts
- `outputs/adapters/c_v10_chat_factual_s43`
- `outputs/adapters/c_v10_chat_coach_s43`
- `logs/emission_ab_ctrl_s43_canonical_anystyle.json`
- `logs/emission_c_v10_chat_factual_s43_supa.json`
- `logs/emission_c_v10_chat_coach_s43_supa.json`
- `logs/desc_vs_json_composition.json`

## Do not retry
- Do not retrain chat_coach on v10conly at batch 2: OOM at step-0 backward (9.83 GiB, +1.11 needed) because coaching sentences lengthen the CLM sequence.
- Do not retry chat_coach as a product surface on this recipe: collapsed to 51.4 bpm on 172/197 (top1 0.873), MAE 7.77 vs trivial 6.63, skill -1.15. The coaching carrier (mechanism+action) costs grounding.
- Do not compare canonical-draw MAE to eval_v3_1min json 2.84: different pool (302 vs 230), different trivial (6.63 vs 3.95), zero overlapping idxs.

## Notes
PRIMARY after is FACTUAL vs matched json (canonical anystyle, overlap 200/200). Delta +0.474 bpm UP, inside the 0.80 C-only family band. Coach is a separate collapse (7.77, top1 0.873 at 51.4) and was not what Anton asked for (he wanted descriptive, not coaching). The preregistered 3.64 bar was computed from eval_v3 json 2.84; that control is a different population. Floor rate stayed 0. Value-share (32 rows, fixed splitter): json 0.334, factual 0.228, coach 0.141, clinical 0.000. Old @@-id toggle desyncs 100% of prose rows, 0% of json.

#target-style #baseline #deployment-distribution #optihealth #medgemma #readout #optihealth-eiv #matched-baseline #conditioning-collapse #subject-heldout
