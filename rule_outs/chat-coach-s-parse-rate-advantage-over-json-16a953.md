---
created_at: '2026-08-18T21:06:16+00:00'
former_ids: []
id: chat-coach-s-parse-rate-advantage-over-json-16a953
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-18T21:06:16+00:00'
topic_tags:
- target-style
- prompt-budget
- control
- medgemma
- optihealth
---

## Claim
chat_coach's parse-rate advantage over json_slots (1.000 vs 0.930, exp-045 gate g2) is a property of the PROSE TARGET STYLE — i.e. prose is inherently more parseable/emittable than JSON slot filling.

## Method
Preregistered confound-closing control arm (exp-045 follow-up, scratch/run_jsonfit_control.sh; p1/p2/p3 committed before launch in b106d8b). lora_proxy_12ch_jsonfit_s42 is byte-identical to the exp-045 json control except ONE key: prompt_path -> prompts/json_slots_prompt_fit256.md (250 templated tokens, all 13 keys and 11 enums intact, within 1 token of the chat prompt's 251, so prompt LENGTH is matched across arms). Same seed 42, same v5c/phase_b init, same data/canonical_1min_conv bundle. Scored by scratch/emission_audit_anystyle.py on the subject-disjoint test split (13 held-out subjects) and on production optihealth_supabase rows; verdicts by scratch/score_jsonfit_control.py. Row-set comparability verified directly from recorded idx values: all three arms (chat_s42, json_s42, jsonfit_s42) scored IDENTICAL rows — 128/128 test, 200/200 supabase.

## Evidence
p1 CONFIRMED, p2 REFUTED. Fitting the prompt to the budget takes the json control's parse rate from 119/128 = 0.930 to 128/128 = 1.000, EQUAL to chat_coach's 1.000. The 9 rows the truncated control failed (idx 17, 18, 20, 50, 53, 819, 837, 1075, 1082) were recovered 9/9; jsonfit failed 0. The gap was caused by the shipped json_slots prompt being 327 templated tokens against a 256 budget: silently truncated, losing its temp/state key definitions and the literal 'output ONLY the JSON object' instruction — exactly the axis g2 measures. p3 HOLDS: hr_raw skill +4.967 (jsonfit) vs +4.828 (json ctl) = +0.139 bpm against a 2.26 bpm seed band, confirming the prompt tail carries no numeric information. Paired on the 119 rows BOTH arms parsed, jsonfit hr_raw MAE 2.315 vs 2.761 bpm (-0.445). CONSEQUENCE: exp-045's g2 reading is AMENDED. Against a length-matched, budget-fitted control (250 vs chat's 251 templated tokens), json_slots and chat_coach are BOTH 1.000 on parse and indistinguishable on grounding (+4.967 vs +4.961 = 0.006 bpm against a 2.26 bpm band). The exp-045 headline survives ONLY in its powered-null form: prose costs no detectable grounding — and now equally, prose BUYS no detectable parseability. What remains genuinely unique to chat_coach is the epistemics JSON structurally cannot express (abstention ~64% of test rows, hedging 98-99% of production supabase rows), which this control does not touch.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].

#target-style #prompt-budget #control #medgemma #optihealth
