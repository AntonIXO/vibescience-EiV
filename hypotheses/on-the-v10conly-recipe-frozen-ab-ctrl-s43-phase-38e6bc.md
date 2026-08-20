---
ambition_class: mechanism
created_at: '2026-08-20T09:51:50+00:00'
former_ids: []
gap_closure_estimate: 0.3
gates:
- blocking: true
  description: chat_factual supabase hr_raw MAE <= 3.64 bpm (json s43 2.84 + v10conly
    C-only family band 0.80 from exp-054). After > 3.64 is a real cost, not noise.
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: gate-factual-within-v10-band
  mde: 0.8
- blocking: true
  description: chat_coach supabase hr_raw MAE <= 3.64 bpm on the same draw. Same bar
    as factual.
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: gate-coach-within-v10-band
  mde: 0.8
- blocking: true
  description: Both chat arms beat the supabase trivial always-median baseline (3.95
    on the v10conly 12,547-corpus draw). An arm inside the json band that loses to
    trivial is not a product surface.
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: gate-beats-trivial
  mde: null
- blocking: true
  description: hr_raw extract rate >= 0.90 on both chat arms (anystyle prose regex
    on 'heart rate averaged ... bpm'). A prose arm that cannot be parsed is not a
    product.
  diagnostic_id: null
  id: gate-extract-rate
  mde: null
- blocking: false
  description: 'INFO: |coach MAE - factual MAE| < 0.80. If coach is worse than factual
    by more than the family band, the coaching sentences cost grounding and the product
    surface should ship factual.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: gate-coaching-cost
  mde: 0.8
id: on-the-v10conly-recipe-frozen-ab-ctrl-s43-phase-38e6bc
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  direction: down
  magnitude_note: 'Primary is FACTUAL chat vs json s43 (2.84). Predict no cost: factual
    MAE at or below 2.84, and in any case <= 3.64 (family band). Coach is a second
    arm whose extra mechanism/action sentences we predict also stay inside 3.64; |coach-factual|
    is an INFO gate, not the primary.'
- diagnostic_id: eiv_supabase_floor_emission_rate
  direction: none
  magnitude_note: 'Hygiene: both chat arms keep exact-30 floor rate at 0 like the
    v10conly json family. Not a win, a non-regression.'
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
problem_tags:
- optihealth-eiv
- matched-baseline
- conditioning-collapse
- subject-heldout
reopen_reason: ''
reopened_from: null
status: refuted
supersedes: null
topic_tags:
- target-style
- baseline
- deployment-distribution
- optihealth
- medgemma
- readout
---

## Statement
On the v10conly recipe (frozen ab_ctrl_s43/phase_b, c_ctrl_s43 law, seed 43), Phase C trained with chat description type factual (measured facts + hedges + abstention, same @@atoms@@ as chat_coach) emits supabase hr_raw at MAE no worse than json s43 2.84 beyond the 0.80 C-only family band (bar 3.64), and beats trivial 3.95. The coach description (adds mechanism + action) stays inside the same bar. This is a substrate transfer of exp-045 plus a within-chat description-type ablation.

## Rationale
exp-045 showed chat_coach vs json_slots is a powered null on the OLD 5955-row converted corpus / v5c encoder. Evidence is bound to substrate. v10conly is a new recipe (12,547 corpus, frozen ab_ctrl_s43 B, 3-dataset C, family band 0.80). Transfer is untested. Splitting chat into factual vs coach isolates whether the coaching carrier (mechanism+action), which dilutes value-token loss share, costs grounding — exp-045 never compared two chat description types, only chat vs json.

## Plan
Clone c_ctrl_s43 (v10conly C recipe) twice, changing only target_style. Frozen encoder outputs/checkpoints/ab_ctrl_s43/phase_b. Sequential on the 5070: factual then coach, 2600 steps each. Score both with scratch/emission_audit_anystyle.py --bundle data/canonical --dataset optihealth_supabase --n 200 --batch-size 1. Json control is the already-trained outputs/adapters/ab_ctrl_s43 (MAE 2.84). yaml-diff asserted before launch.

## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_supabase_hr_raw_mae_bpm]] (down).
Predicts [[eiv_supabase_floor_emission_rate]] (none).
Gate `gate-factual-within-v10-band` (blocking): chat_factual supabase hr_raw MAE <= 3.64 bpm (json s43 2.84 + v10conly C-only family band 0.80 from exp-054). After > 3.64 is a real cost, not noise.
Gate `gate-coach-within-v10-band` (blocking): chat_coach supabase hr_raw MAE <= 3.64 bpm on the same draw. Same bar as factual.
Gate `gate-beats-trivial` (blocking): Both chat arms beat the supabase trivial always-median baseline (3.95 on the v10conly 12,547-corpus draw). An arm inside the json band that loses to trivial is not a product surface.
Gate `gate-extract-rate` (blocking): hr_raw extract rate >= 0.90 on both chat arms (anystyle prose regex on 'heart rate averaged ... bpm'). A prose arm that cannot be parsed is not a product.
Gate `gate-coaching-cost` (advisory): INFO: |coach MAE - factual MAE| < 0.80. If coach is worse than factual by more than the family band, the coaching sentences cost grounding and the product surface should ship factual.

#target-style #baseline #deployment-distribution #optihealth #medgemma #readout #optihealth-eiv #matched-baseline #conditioning-collapse #subject-heldout
