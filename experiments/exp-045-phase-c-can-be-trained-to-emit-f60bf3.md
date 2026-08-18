---
artifacts:
- outputs/checkpoints/chatab_json_s42/phase_c
- outputs/checkpoints/chatab_chat_s42/phase_c
- outputs/checkpoints/chatab_chat_s43/phase_c
- outputs/checkpoints/chatab_chat_s44/phase_c
- outputs/probes/chatab_json_s42_emissions_test_summary.json
- outputs/probes/chatab_chat_s42_emissions_test_summary.json
- outputs/probes/chatab_chat_s43_emissions_test_summary.json
- outputs/probes/chatab_chat_s44_emissions_test_summary.json
- outputs/probes/chatab_json_s42_emissions_supabase_summary.json
- outputs/probes/chatab_chat_s42_emissions_supabase_summary.json
- outputs/probes/chatab_chat_s43_emissions_supabase_summary.json
- outputs/probes/chatab_chat_s44_emissions_supabase_summary.json
- logs/chat_coach_ab.log
- logs/chat_coach_token_composition_FIXED.json
- scratch/run_chat_coach_ab.sh
- scratch/emission_audit_anystyle.py
- scratch/score_chat_coach_gates.py
- scratch/legacy_bundle_convert.py
- scratch/prove_value_span_equivalence.py
- scratch/validate_anystyle_extractor.py
- scratch/thin_evidence_census.py
- scratch/where_is_confabulation.py
- scratch/check_prompt_truncation.py
- tests/test_chat_coach_target.py
- tests/test_value_span_split.py
- data/canonical_1min_conv
closed: true
config_note: "exp-chat-coach-20260818. Driver scratch/run_chat_coach_ab.sh, log logs/chat_coach_ab.log.\n\nARMS
  (sequential, RTX 5070, all off outputs/checkpoints/v5c/phase_b):\n  chatab_json_s42
  \ lora_proxy_12ch_json_s42  target_style json_slots  seed 42  CONTROL\n  chatab_chat_s42
  \ lora_proxy_12ch_chat_s42  target_style chat_coach  seed 42\n  chatab_chat_s43
  \ lora_proxy_12ch_chat_s43  target_style chat_coach  seed 43\n  chatab_chat_s44
  \ lora_proxy_12ch_chat_s44  target_style chat_coach  seed 44\n\nSINGLE VARIABLE
  asserted in-config before launch: chat_s42 vs json_s42 differ in\n`target_style`
  alone (profiles cloned programmatically by scratch/emit_chat_profiles.py).\n\nDATA:
  data/canonical_1min_conv, split=train (4627 rows / 89 subjects). Held-out test split\n574
  rows / 13 subjects, subject overlap 0. The historical data/canonical/ bundle is
  GONE\nfrom disk, which is why the json control is RETRAINED rather than taken from
  v9band04.\n\nOOM DEVIATION: batch_size 2 -> 1, grad_accum 4 -> 8, max_steps 2600
  -> 5200 on ALL FOUR\narms (chat targets ~184 tok vs json ~95 tok OOM at bs=2 on
  12 GB). Sample budget unchanged;\nthe change is identical across arms so it cannot
  confound the contrast.\n\nDEFECT FOUND AND FIXED MID-CAMPAIGN (this is load-bearing
  for interpreting the arms):\nprepare_training_inputs located @@value@@ spans by
  toggling on the token id of a standalone\n'@@'. Whether the tokenizer emits that
  id depends on the PRECEDING CHARACTER. json puts its\ndelimiters after a quote or
  colon (2/2 recognised); prose puts them after a space, where\nthe tokenizer merges
  ' @@' into a different id (1/2 recognised). The toggle desynced,\ninverted, and
  left the literal marker IN the supervised target.\nMeasured over 500 rows per style
  (scratch/prove_value_span_equivalence.py):\n  json: 0/500 token-id diffs, 0/500
  value-flag diffs, 0/500 '@@' leaks  -> UNAFFECTED\n  chat: 500/500 token-id diffs,
  500/500 flag diffs, 500/500 '@@' leaks -> CORRUPTED\nFix: _split_value_spans() strips
  the delimiters from TEXT and classifies tokens by\ncharacter offset, removing the
  dependence on tokenizer merge behaviour. 12 regression\ntests in tests/test_value_span_split.py.\nTIMING:
  fix written 01:24:36; the json control had started 01:05 and finished 01:44, so
  the\ncontrol trained with the OLD code — which the equivalence proof shows is byte-identical
  for\njson. The chat arms started 02:06:59, after the fix. The comparison stands.\n\nCORRECTED
  SIDE MEASUREMENT: with the desync, chat appeared to concentrate 54.4% of its loss\non
  value tokens (91.8 'value' tokens from ~11 atoms — implausible on its face, which
  is what\nprompted the audit). Post-fix the real numbers are chat 26.1 value tokens
  / 15.0% loss share\nvs json 24.3 / 32.6% — a 2.17x DILUTION, the direction originally
  predicted in the\nhypothesis rationale. logs/chat_coach_token_composition_FIXED.json
  supersedes\nlogs/chat_coach_token_composition.json, which is wrong.\n\nINSTRUMENT:
  scratch/emission_audit_anystyle.py reads both surfaces. Validated before use\n(scratch/validate_anystyle_extractor.py):
  0 disagreements with the shipped json extractor\nover 784 stored rows x 2 slots,
  and 100% round-trip of hr_raw/hr_peak/hrv_raw from real\nchat targets (300/300,
  300/300, 141/141). So a chat-vs-json difference cannot be an\nextractor artifact.\n\nCONTROL
  RESULT (complete, all stages RC=0):\n  test split n=128, parse 119/128: hr_raw skill
  +4.828 bpm (MAE 2.761 vs trivial 7.588,\n    corr 0.879); hr_peak skill -2.117 (MAE
  18.178 vs trivial 16.061, corr 0.326)\n  supabase n=200, parse 200/200: hr_raw skill
  +0.958 (MAE 3.057 vs trivial 4.015,\n    corr 0.767); hr_peak skill -8.141\n  This
  reproduces the shipped v9band04 pattern (positive hr_raw, negative hr_peak) on the\n
  \ new bundle, so the control is sound and g1's bar is +4.828 - 2.26 = +2.568 bpm."
crash_reason: ''
created_at: '2026-08-17T23:07:45+00:00'
diagnostics_measured:
- after: 2.185
  before: 2.761
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
- after: -1.095
  before: -2.117
  diagnostic_id: eiv_gen_peak_skill_bpm
- after: 0.0
  before: 0.0
  diagnostic_id: eiv_supabase_floor_emission_rate
do_not_retry:
- Do NOT re-run chat-vs-json as a single-seed comparison expecting to resolve which
  is better on hr_raw. The 3-seed chat spread is 1.53 bpm (+3.896 to +5.428) against
  a 2.26 bpm seed band, so the arms are statistically indistinguishable on grounding
  BY CONSTRUCTION at this sample size. Choosing between them on hr_raw needs many
  more seeds, and the honest answer is already 'no detectable difference'.
- Do NOT read chat's less-negative hr_peak (-0.487/-1.095 vs json -2.117) as an improvement.
  The 2.04 bpm spread is inside the 2.42 bpm hr_peak seed band. hr_peak is encoder-limited
  and target style cannot move it.
- 'Do NOT use scratch/emission_diversity_audit.py to score a prose arm. Its extractor
  is a JSON-key regex and returns null on prose, which would refute chat_coach by
  instrument artifact. Use scratch/emission_audit_anystyle.py (validated: 0 disagreements
  with the old extractor over 784 stored json rows).'
- Do NOT quote logs/chat_coach_token_composition.json. It was measured through the
  @@-toggle desync and reports chat value share 0.544 / 91.8 value tokens, both wrong.
  logs/chat_coach_token_composition_FIXED.json (0.150 / 26.1) supersedes it.
- Do NOT use data/canonical_1min_conv for state_at(epoch), annotated_epochs() positionally,
  or any within-day transition claim. Label timestamps were destroyed upstream; events
  sit at sentinel epochs purely so infer_state_label's majority reduction returns
  the historical day-level answer. See its label_provenance.json.
- Do NOT attribute chat's parse advantage (1.000 vs 0.930) to target style alone.
  The json_slots prompt is truncated at 256 of 327 tokens, losing its 'output ONLY
  the JSON object' instruction, so the control is handicapped on exactly the axis
  parse rate measures. Fix the prompt and re-run the control before claiming prose
  is inherently more parseable.
external_run: 'local: logs/chat_coach_ab.log'
former_ids: []
gate_results:
- evidence: 'chat per-seed test-split hr_raw skill +4.961/+5.428/+3.896, median +4.961
    vs json control +4.828. Delta +0.134 bpm against the preregistered bar of json-2.26
    = +2.568. PASS. |delta| 0.134 is far under the 2.26 mde, so this is a POWERED
    NULL on the difference: no detectable grounding cost from the prose carrier, NOT
    a demonstrated superiority.'
  gate_id: g1_hr_raw_within_seed_band
  passed: true
- evidence: chat numeric-extraction rate 1.000 on all three seeds (128/128 each) vs
    the json control's 119/128 = 0.930. The prose surface is MORE reliably machine-readable
    than the JSON one on this eval, which was not predicted.
  gate_id: g2_parse_rate
  passed: true
- evidence: 'INFO. Test split: chat abstains on 0.633/0.641/0.641 of rows and hedges
    on 0.023, json 0.000 on both by construction. Supabase (production): chat hedges
    on 0.99/0.98/0.98 of rows, matching the measured thin-evidence census (rr backed
    by 1-4 observed epochs on 213/223 supabase rows, temp on 219/223). The model reproduces
    the target''s epistemics at generation time, seed-stably.'
  gate_id: g3_abstention_behaviour
  passed: true
- evidence: 'INFO / trap-check PASSES. chat hr_peak skill -0.487/-1.095/-2.528, json
    -2.117: all NEGATIVE, so the encoder excursion limit is intact and no instrument
    artifact is indicated. chat s42/s43 are nominally less negative than the control,
    but the 2.04 bpm spread sits inside the 2.42 bpm hr_peak seed band, so it is noise
    and must NOT be read as chat improving hr_peak.'
  gate_id: g4_hr_peak_stays_negative
  passed: true
git_ref: ''
hypothesis_id: phase-c-can-be-trained-to-emit-conversational-0dcbc0
id: exp-045-phase-c-can-be-trained-to-emit-f60bf3
key_numbers:
  chat_hedge_rate_supabase_median: 0.98
  chat_parse_rate_min: 1.0
  chat_test_abstain_rate_median: 0.641
  chat_test_hr_raw_skill_median: 4.961
  chat_test_hr_raw_skill_s42: 4.961
  chat_test_hr_raw_skill_s43: 5.428
  chat_test_hr_raw_skill_s44: 3.896
  chat_test_hr_raw_spread: 1.532
  chat_value_loss_share: 0.15
  delta_median_chat_minus_json: 0.134
  g1_bar: 2.568
  json_parse_rate: 0.93
  json_test_hr_raw_skill: 4.828
  json_value_loss_share: 0.326
  mde_hr_raw_seed_band: 2.26
  supabase_hr_raw_skill_chat_median: 1.243
  supabase_hr_raw_skill_json: 0.958
  value_loss_share_dilution: 2.17
observed_effects:
- delta: -0.576
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: down
- delta: 1.022
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
- delta: 0.0
  diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_dense128_hr_raw_mae_bpm: false
    eiv_gen_peak_skill_bpm: false
    eiv_supabase_floor_emission_rate: true
provenance: {}
verdict: refutes
---

## Summary
Tests hypothesis [[phase-c-can-be-trained-to-emit-conversational-0dcbc0]].
External run: local: logs/chat_coach_ab.log

## Measurements
- [[eiv_dense128_hr_raw_mae_bpm]]: before 2.761 → after 2.185  (Δ -0.576, down ✗ predicted none)
- [[eiv_gen_peak_skill_bpm]]: before -2.117 → after -1.095  (Δ +1.022, up ✗ predicted none)
- [[eiv_supabase_floor_emission_rate]]: before 0.0 → after 0.0  (Δ +0, flat ✓ predicted none)

## Gates
- `g1_hr_raw_within_seed_band`: PASS — chat per-seed test-split hr_raw skill +4.961/+5.428/+3.896, median +4.961 vs json control +4.828. Delta +0.134 bpm against the preregistered bar of json-2.26 = +2.568. PASS. |delta| 0.134 is far under the 2.26 mde, so this is a POWERED NULL on the difference: no detectable grounding cost from the prose carrier, NOT a demonstrated superiority.
- `g2_parse_rate`: PASS — chat numeric-extraction rate 1.000 on all three seeds (128/128 each) vs the json control's 119/128 = 0.930. The prose surface is MORE reliably machine-readable than the JSON one on this eval, which was not predicted.
- `g3_abstention_behaviour`: PASS — INFO. Test split: chat abstains on 0.633/0.641/0.641 of rows and hedges on 0.023, json 0.000 on both by construction. Supabase (production): chat hedges on 0.99/0.98/0.98 of rows, matching the measured thin-evidence census (rr backed by 1-4 observed epochs on 213/223 supabase rows, temp on 219/223). The model reproduces the target's epistemics at generation time, seed-stably.
- `g4_hr_peak_stays_negative`: PASS — INFO / trap-check PASSES. chat hr_peak skill -0.487/-1.095/-2.528, json -2.117: all NEGATIVE, so the encoder excursion limit is intact and no instrument artifact is indicated. chat s42/s43 are nominally less negative than the control, but the 2.04 bpm spread sits inside the 2.42 bpm hr_peak seed band, so it is noise and must NOT be read as chat improving hr_peak.

**Verdict:** refutes

## Key numbers
- `chat_hedge_rate_supabase_median`: 0.98
- `chat_parse_rate_min`: 1
- `chat_test_abstain_rate_median`: 0.641
- `chat_test_hr_raw_skill_median`: 4.961
- `chat_test_hr_raw_skill_s42`: 4.961
- `chat_test_hr_raw_skill_s43`: 5.428
- `chat_test_hr_raw_skill_s44`: 3.896
- `chat_test_hr_raw_spread`: 1.532
- `chat_value_loss_share`: 0.15
- `delta_median_chat_minus_json`: 0.134
- `g1_bar`: 2.568
- `json_parse_rate`: 0.93
- `json_test_hr_raw_skill`: 4.828
- `json_value_loss_share`: 0.326
- `mde_hr_raw_seed_band`: 2.26
- `supabase_hr_raw_skill_chat_median`: 1.243
- `supabase_hr_raw_skill_json`: 0.958
- `value_loss_share_dilution`: 2.17

## Artifacts
- `outputs/checkpoints/chatab_json_s42/phase_c`
- `outputs/checkpoints/chatab_chat_s42/phase_c`
- `outputs/checkpoints/chatab_chat_s43/phase_c`
- `outputs/checkpoints/chatab_chat_s44/phase_c`
- `outputs/probes/chatab_json_s42_emissions_test_summary.json`
- `outputs/probes/chatab_chat_s42_emissions_test_summary.json`
- `outputs/probes/chatab_chat_s43_emissions_test_summary.json`
- `outputs/probes/chatab_chat_s44_emissions_test_summary.json`
- `outputs/probes/chatab_json_s42_emissions_supabase_summary.json`
- `outputs/probes/chatab_chat_s42_emissions_supabase_summary.json`
- `outputs/probes/chatab_chat_s43_emissions_supabase_summary.json`
- `outputs/probes/chatab_chat_s44_emissions_supabase_summary.json`
- `logs/chat_coach_ab.log`
- `logs/chat_coach_token_composition_FIXED.json`
- `scratch/run_chat_coach_ab.sh`
- `scratch/emission_audit_anystyle.py`
- `scratch/score_chat_coach_gates.py`
- `scratch/legacy_bundle_convert.py`
- `scratch/prove_value_span_equivalence.py`
- `scratch/validate_anystyle_extractor.py`
- `scratch/thin_evidence_census.py`
- `scratch/where_is_confabulation.py`
- `scratch/check_prompt_truncation.py`
- `tests/test_chat_coach_target.py`
- `tests/test_value_span_split.py`
- `data/canonical_1min_conv`

## Do not retry
- Do NOT re-run chat-vs-json as a single-seed comparison expecting to resolve which is better on hr_raw. The 3-seed chat spread is 1.53 bpm (+3.896 to +5.428) against a 2.26 bpm seed band, so the arms are statistically indistinguishable on grounding BY CONSTRUCTION at this sample size. Choosing between them on hr_raw needs many more seeds, and the honest answer is already 'no detectable difference'.
- Do NOT read chat's less-negative hr_peak (-0.487/-1.095 vs json -2.117) as an improvement. The 2.04 bpm spread is inside the 2.42 bpm hr_peak seed band. hr_peak is encoder-limited and target style cannot move it.
- Do NOT use scratch/emission_diversity_audit.py to score a prose arm. Its extractor is a JSON-key regex and returns null on prose, which would refute chat_coach by instrument artifact. Use scratch/emission_audit_anystyle.py (validated: 0 disagreements with the old extractor over 784 stored json rows).
- Do NOT quote logs/chat_coach_token_composition.json. It was measured through the @@-toggle desync and reports chat value share 0.544 / 91.8 value tokens, both wrong. logs/chat_coach_token_composition_FIXED.json (0.150 / 26.1) supersedes it.
- Do NOT use data/canonical_1min_conv for state_at(epoch), annotated_epochs() positionally, or any within-day transition claim. Label timestamps were destroyed upstream; events sit at sentinel epochs purely so infer_state_label's majority reduction returns the historical day-level answer. See its label_provenance.json.
- Do NOT attribute chat's parse advantage (1.000 vs 0.930) to target style alone. The json_slots prompt is truncated at 256 of 327 tokens, losing its 'output ONLY the JSON object' instruction, so the control is handicapped on exactly the axis parse rate measures. Fix the prompt and re-run the control before claiming prose is inherently more parseable.

## Notes
VERDICT: SUPPORTS. Both blocking gates pass. Phase C can be trained for conversational annotation + coaching instead of JSON slot filling with NO DETECTABLE COST to numeric grounding.

PRIMARY RESULT (held-out test split, 13 subjects disjoint from the 89 trained on):
  hr_raw skill  json +4.828 | chat +4.961 / +5.428 / +3.896 (median +4.961)
  delta +0.134 bpm against a 2.26 bpm seed band -> POWERED NULL on the difference.
  This is the correct reading: 'no detectable grounding cost', NOT 'chat is better'.
  The chat spread alone (1.53 bpm) is most of the band, so the arms are
  indistinguishable at this sample size by construction.

WHAT IS GENUINELY NEW, beyond the null:
  1. parse/extract 1.000 on all three chat seeds vs 0.930 for json. CAVEAT: the json
     prompt is truncated (327 vs 256 tokens) and loses its 'output ONLY the JSON object'
     instruction, so the control is handicapped on this exact axis. Do not claim prose is
     inherently more parseable until the prompt is fixed and the control re-run.
  2. The model REPRODUCES the target's epistemics at generation, seed-stably: abstains on
     ~64% of test rows ('Not measured today: HRV and respiratory rate...') and hedges on
     98-99% of PRODUCTION supabase rows ('from only 1 minute of data, so treat it as
     indicative'). That hedge rate tracks the independently measured thin-evidence census
     (rr backed by 1-4 observed epochs on 213/223 supabase rows, temp on 219/223). json
     scores 0.000 on both by construction — a JSON enum has nowhere to put a hedge.
  3. Grounding survives a 2.17x DILUTION of the value-token loss share (chat 0.150 vs json
     0.326 at value_loss_weight=1.0). The preregistered failure mode was real and
     quantified, and the model absorbed it. value_loss_weight ~2.17 on the chat arm is an
     untested lever that would restore parity if a future arm needs it.

SUPABASE (production population, n=200): chat hr_raw skill +1.243/+1.570/-0.676 vs json
+0.958, trivial baseline 4.015. Median chat +1.243 beats the control, but seed 44 goes
NEGATIVE — the deployment population remains the fragile one, consistent with the existing
deployment-distribution record. hr_peak is catastrophically negative on BOTH styles
(-8.1 to -9.9), unchanged and unrelated to target style.

DEFECT FIXED MID-CAMPAIGN (see config_note): the @@-span toggle desynced on prose,
corrupting 500/500 chat targets and leaking the literal delimiter into supervision. json
proved byte-identical under old and new code (0/500 diffs), which is why the already-
trained control stands. Without that equivalence proof the whole campaign would have had
to be rerun. Generalised into the `delimiter-span-supervision` skill.

HONEST RESIDUALS:
  - The json control is handicapped by prompt truncation. Every chat-vs-json comparison
    here is conservative for chat on grounding and unreliable on parse rate.
  - Coaching QUALITY is unmeasured. The action sentences come from a 4-entry state map, and
    supabase/mmash/big_ideas contribute no epoch-scoped affect label at all (100% neutral),
    so the 'What to do' line on production rows is a constant. This experiment shows the
    SURFACE is trainable without losing numbers; it does not show the advice is good.
  - The bundle is 5955 rows, not the historical 10344. Absolute numbers are not comparable
    to shipped v9band04 figures; only the within-campaign contrast is.

#target-style #optihealth #medgemma #readout #deployment-distribution #optihealth-eiv #matched-baseline #subject-heldout #conditioning-collapse
