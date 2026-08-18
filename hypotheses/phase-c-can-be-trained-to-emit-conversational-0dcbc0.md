---
created_at: '2026-08-17T22:07:12+00:00'
former_ids: []
gates:
- blocking: true
  description: 'PRIMARY. chat_coach median hr_raw skill (3 seeds, held-out TEST split)
    >= json_slots control hr_raw skill MINUS 2.26 bpm (the measured Phase-C hr_raw
    seed band). Passing means ''no detectable grounding cost from switching the carrier
    to prose''. mde is set to the band itself: if chat loses by LESS than 2.26 the
    run is not powered to call it a real cost, and the verdict is a powered null rather
    than a near-miss.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g1_hr_raw_within_seed_band
  mde: 2.26
- blocking: true
  description: chat_coach numeric-extraction rate >= 0.90 on the test split. A prose
    arm whose values cannot be reliably read out of the text is not a usable product
    surface regardless of its MAE. The extractor is anchored on unit phrases (bpm
    / ms), validated against the json extractor on the json arm.
  diagnostic_id: eiv_gen_pred_bucket_entropy
  id: g2_parse_rate
  mde: 0.05
- blocking: false
  description: INFO. Abstention rate on absent channels and hedge rate on thin-evidence
    rows, at GENERATION time. The target builder abstains on ~57% of rows and hedges
    on ~7%; the question is whether the model REPRODUCES that rather than papering
    over gaps. Not scored as a win by itself — reproducing a target property is not
    the same as clinical value.
  diagnostic_id: eiv_supabase_floor_emission_rate
  id: g3_abstention_behaviour
  mde: 0.1
- blocking: false
  description: INFO / TRAP-CHECK. hr_peak skill is expected NEGATIVE on BOTH arms
    (three-seed json result -2.88/-2.73/-3.16; encoder excursion limit). A chat arm
    that appears to FIX hr_peak must be treated as an instrument artifact until the
    prose extractor is validated against a donor-prefix control, not reported as a
    win.
  diagnostic_id: eiv_gen_peak_skill_bpm
  id: g4_hr_peak_stays_negative
  mde: 2.42
id: phase-c-can-be-trained-to-emit-conversational-0dcbc0
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: none
  magnitude_note: 'PRIMARY. Predict chat_coach hr_raw MAE lands WITHIN the 2.26 bpm
    hr_raw seed band of the json_slots control on the same bundle and the same held-out
    test split. Direction ''none'' is the actual prediction: the @@value@@ atoms,
    the deviation weighting and the value_loss_weight all act on identical numbers
    in both arms, so the carrier text should not move numeric grounding materially.
    A large LOSS would mean prose dilutes the value-token gradient share; a large
    GAIN would be surprising and would need a mechanism before being believed.'
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: none
  magnitude_note: 'hr_peak skill stays NEGATIVE on both arms. This is a control, not
    a target: it is the encoder excursion limit (three-seed json result -2.88/-2.73/-3.16)
    and target style cannot touch it. If chat_coach shows positive hr_peak skill,
    suspect the prose extractor before believing it.'
- diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
  magnitude_note: INFO. The 30-floor atom was removed at a71034c, so both arms should
    sit near 0. Reported to confirm the converted bundle did not reintroduce a training-target
    attractor.
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- conditioning-collapse
status: refuted
supersedes: null
topic_tags:
- target-style
- optihealth
- medgemma
- readout
- deployment-distribution
---

## Statement
Phase C can be trained to emit conversational annotation + coaching (`target_style: chat_coach`) instead of a JSON slot schema WITHOUT a detectable loss of numeric grounding, because the numeric supervision mechanism (@@value@@ atoms under value_loss_weight and deviation weighting) is identical in both target styles and only the carrier text differs. Predicted: chat_coach hr_raw emission skill lands within the measured 2.26 bpm Phase-C seed band of a json_slots control trained on the same bundle, same split, same driver.

## Rationale
MECHANISM FOR THE 'NO COST' PREDICTION. Phase C's numeric supervision runs through the @@value@@ delimiters: gemma_runtime.prepare_training_inputs strips them and assigns value_loss_weight to the enclosed tokens, and deviation_weighted_clm_loss scales the per-sample CE by clinical abnormality. `_build_chat_coach_target` emits the SAME denormalised statistics inside the SAME delimiters, drawn from the same sample_tensor and population_stats. Measured on 100 real rows: 10.3 @@atoms per chat target vs 10.2 per json target. So the gradient signal that produces hr_raw skill is present in both arms at the same density; what differs is the surrounding text.

WHAT COULD MAKE IT FAIL, stated in advance. chat targets are ~184 tokens vs ~95 for json, so the value tokens are a SMALLER FRACTION of the supervised sequence (roughly 10 atoms in 184 tokens vs 10 in 95). If the fixed prose scaffolding competes for capacity the way eiv_clm_value_gradient_share was designed to test, chat could lose hr_raw skill for a reason that has nothing to do with whether prose is the right product surface. That is the single most likely failure mode and it is measurable rather than fatal.

KNOWN CONFOUND, disclosed before the run: the json_slots prompt is 327 templated tokens against a 256 budget and is therefore silently TRUNCATED, losing its last two key definitions and the 'output ONLY JSON' instruction. The chat_coach prompt is 216 tokens and is not truncated. The json control is thus running with a decapitated prompt — exactly as every shipped arm has. This handicaps the CONTROL, so a chat win must not be attributed to target style without re-running json with a shortened prompt. A chat LOSS, by contrast, would be conservative and interpretable.

## Plan


## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (none).
Predicts [[eiv_gen_peak_skill_bpm]] (none).
Predicts [[eiv_supabase_floor_emission_rate]] (none).
Gate `g1_hr_raw_within_seed_band` (blocking): PRIMARY. chat_coach median hr_raw skill (3 seeds, held-out TEST split) >= json_slots control hr_raw skill MINUS 2.26 bpm (the measured Phase-C hr_raw seed band). Passing means 'no detectable grounding cost from switching the carrier to prose'. mde is set to the band itself: if chat loses by LESS than 2.26 the run is not powered to call it a real cost, and the verdict is a powered null rather than a near-miss.
Gate `g2_parse_rate` (blocking): chat_coach numeric-extraction rate >= 0.90 on the test split. A prose arm whose values cannot be reliably read out of the text is not a usable product surface regardless of its MAE. The extractor is anchored on unit phrases (bpm / ms), validated against the json extractor on the json arm.
Gate `g3_abstention_behaviour` (advisory): INFO. Abstention rate on absent channels and hedge rate on thin-evidence rows, at GENERATION time. The target builder abstains on ~57% of rows and hedges on ~7%; the question is whether the model REPRODUCES that rather than papering over gaps. Not scored as a win by itself — reproducing a target property is not the same as clinical value.
Gate `g4_hr_peak_stays_negative` (advisory): INFO / TRAP-CHECK. hr_peak skill is expected NEGATIVE on BOTH arms (three-seed json result -2.88/-2.73/-3.16; encoder excursion limit). A chat arm that appears to FIX hr_peak must be treated as an instrument artifact until the prose extractor is validated against a donor-prefix control, not reported as a win.

#target-style #optihealth #medgemma #readout #deployment-distribution #optihealth-eiv #matched-baseline #subject-heldout #conditioning-collapse
