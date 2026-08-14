---
created_at: '2026-08-14T08:45:01+00:00'
former_ids: []
gates:
- blocking: true
  description: 'BLOCKING primary (sign flip): dense-128 hr_peak skill vs the STRONGEST
    computable trivial baseline (max of always-predict-median and hr_raw-derived =
    emit_raw + median excursion) > 0 bpm. v9band04 reference: -2.88 (s42), -2.72 (s43),
    -3.16 (s44) vs median alone. If it passes, a donor-prefix capture must run BEFORE
    any claim stronger than ''sign flipped'' (eiv_gen_peak_skill_bpm requires the
    donor control).'
  diagnostic_id: null
  id: g1-peak-sign-flip
  mde: null
- blocking: true
  description: 'BLOCKING regression guard (same bars as E3): supabase hr_raw MAE <=
    3.45 AND dense-128 hr_raw MAE <= 4.73 (worst v9band04 seed values). The lm_head
    arm must not buy peak at the cost of the working hr axes.'
  diagnostic_id: null
  id: g2-hr-axes-preserved
  mde: null
- blocking: true
  description: 'BLOCKING operational: training completes RC=0 with peak VRAM < 11.25
    GiB (the untied bf16 lm_head clone adds ~1.34 GB; eiv_peak_vram_gib gate).'
  diagnostic_id: null
  id: g3-vram-ceiling
  mde: null
- blocking: false
  description: 'Informational: supabase hr_peak MAE vs its own trivial (17.84 on the
    200-row draw; v9band04_s42 was -2.28 skill) and emitted-peak std ratio / top-atom
    mass (attractor check). Also hrv_raw and bucket entropy for completeness.'
  diagnostic_id: null
  id: g4-deployment-and-attractors
  mde: null
id: hyp-e9-lmhead-lora-20260814
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
  magnitude_note: 'Committed: dense-128 hr_peak skill moves from -2.9 (v9band04_s42)
    to POSITIVE, expected +1 to +4 bpm vs strongest computable baseline — E26 measured
    the untied-readout free-running ceiling at ~+4.2 bpm over median and the E8 chain
    proves the information reaches the emission position (increment +0.5032). NOT
    predicting accurate peaks: E26''s per-digit analysis showed magnitude-only recovery.
    Genuine uncertainty: E26''s readout was trained OUTSIDE the CLM objective; inside
    it, the template prior competes for the same gradient.'
- diagnostic_id: eiv_peak_vram_gib
  direction: up
  magnitude_note: Untied lm_head clone (262145x2560 bf16) ~ +1.34 GiB + LoRA rank-32
    on it. Expect peak < 11.25 GiB; blocking gate g3.
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- medgemma
- collapse
- cross-modal
- sensor-text
---

## Statement
H-E9 (the E4 arm, executed): adding lm_head to the LoRA target set — with the head UNTIED from the input embedding table and the embedding table asserted bit-identical after attach — converts the readout-localized peak signal into positive generated hr_peak skill on the v9band04 recipe.

## Rationale
E6->E7->E8 (2026-08-14) closed the localization on the current recipe: raw supply 0.4847 -> encoder 0.5004 -> projector 0.5263 -> LM final layer at the emission position +0.5032 increment, hr_raw control 0.9981, shuffle null clean — while generation has negative peak skill. The frozen readout is the sole failure stage, exactly the condition under which the diagnostic's own design note says 'LoRA on the LM head / final layers is then precisely targeted'. E26 established capacity in lm_head's real function class is sufficient for ~4.2 bpm. The blocking design issue (weight tying) is resolved in code: untie via clone + bit-identity assertion on the embedding table. Expectation recorded per the runbook: PARTIAL-CREDIT arm worth ~4 bpm, not a fix.

## Plan
E9: (1) code change landed — lora_target_modules threaded configs/training.yaml -> cli_train -> run_lora_proxy_adaptation -> attach_lora_adapters; lm_head UNTIED (weight cloned to independent Linear, tie flags off) before attach; assert input-embedding table bit-identical after attach (aborts if violated). Tests: 24 passed. (2) Profile lora_proxy_12ch_wl_band04_lmhead = v9band04 recipe + lm_head in targets (yaml-diff verified: only lora_target_modules+notes differ). (3) Train Phase C off the SAME phase_b checkpoint (outputs/checkpoints/v5c/phase_b), seed 42, tag v11lmhead_s42, ~40 min GPU; then supabase-200 + dense-128 captures (batch 1). (4) Score with scratch/e4_peak_read_probe.py pattern extended to the strongest-baseline rule + scratch/score_slots_deployment.py for hr axes. (5) If g1 passes: donor-prefix capture before claiming skill (diagnostic contract). Routes: PASS g1+g2 -> first-ever positive peak skill; quantify against E26's ~4.2 bpm and decide whether the numeric-head architecture arm is still needed. FAIL g1 with g2 intact -> E26's capacity ceiling was optimistic in-place; lm_head LoRA joins the ruled-out list and the architecture question (scalar numeric head / binned encoding) is the only surviving peak lever — gate-prediction-entropy would then be 0-for-4 and per rb-e3's own note, stop adding cheap levers.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_gen_peak_skill_bpm]] (up).
Predicts [[eiv_peak_vram_gib]] (up).
Gate `g1-peak-sign-flip` (blocking): BLOCKING primary (sign flip): dense-128 hr_peak skill vs the STRONGEST computable trivial baseline (max of always-predict-median and hr_raw-derived = emit_raw + median excursion) > 0 bpm. v9band04 reference: -2.88 (s42), -2.72 (s43), -3.16 (s44) vs median alone. If it passes, a donor-prefix capture must run BEFORE any claim stronger than 'sign flipped' (eiv_gen_peak_skill_bpm requires the donor control).
Gate `g2-hr-axes-preserved` (blocking): BLOCKING regression guard (same bars as E3): supabase hr_raw MAE <= 3.45 AND dense-128 hr_raw MAE <= 4.73 (worst v9band04 seed values). The lm_head arm must not buy peak at the cost of the working hr axes.
Gate `g3-vram-ceiling` (blocking): BLOCKING operational: training completes RC=0 with peak VRAM < 11.25 GiB (the untied bf16 lm_head clone adds ~1.34 GB; eiv_peak_vram_gib gate).
Gate `g4-deployment-and-attractors` (advisory): Informational: supabase hr_peak MAE vs its own trivial (17.84 on the 200-row draw; v9band04_s42 was -2.28 skill) and emitted-peak std ratio / top-atom mass (attractor check). Also hrv_raw and bucket entropy for completeness.

#medgemma #collapse #cross-modal #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
