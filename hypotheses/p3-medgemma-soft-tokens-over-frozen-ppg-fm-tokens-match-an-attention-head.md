---
ambition_class: mechanism
created_at: '2026-09-03T17:50:17+00:00'
former_ids: []
gap_closure_estimate: 0.1
gates:
- blocking: true
  description: 'Split-honest held-out rows: MedGemma+LoRA soft-token hr_raw MAE <=
    attention-head MAE + 0.416 (2 x measured seed sigma 0.208), scale in [0.85, 1.35].
    Predicted to FAIL.'
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  id: g1-llm-within-seed-band-of-head
  mde: 0.416
- blocking: false
  description: State clause AUC of the LLM's emitted label on the same rows >= the
    head's thresholded AUC - 2 sigma of the relevant cell (lab 0.013 / free-living
    0.027), reported per cell.
  diagnostic_id: eiv_state_auc_over_null_lab
  id: g2-state-clause-not-worse
  mde: null
- blocking: false
  description: 'Narrator over the head''s numbers: eiv_narration_numeric_fidelity_mae_bpm
    <= 0.5 and 0 unparseable rows, reproducing exp-n on the new tower.'
  diagnostic_id: eiv_narration_numeric_fidelity_mae_bpm
  id: g3-narrator-fidelity
  mde: null
id: p3-medgemma-soft-tokens-over-frozen-ppg-fm-tokens-match-an-attention-head
interventions: []
override_reason: ''
paper_refs:
- pulselm-2026-ppg-text-benchmark-2603-03331
- opentslm-2025-time-series-language-models-2510-02410
- tan-2024-llm-useful-timeseries-2406-16964
- sensorfm-2026-2605-22759
- pulse-ppg-2025-field-trained-2502-01108
- papagei-2025-open-ppg-fm-2410-20542
predicted_effects:
- diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: up
  magnitude_note: LLM emission MAE >= 1.5x the attention head's MAE on the same frozen
    FM tokens (the head is the comparator recorded in the same experiment); i.e. the
    blocking gate FAILS and the formatter verdict holds on a pretrained tower. Narrator
    arm within 0.05 bpm of the head, as N/N3 measured.
problem_id: is-the-frozen-llm-load-bearing-or-a-formatter
problem_tags:
- optihealth-eiv
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- medgemma
- cross-modal
- readout
- control
---

## Statement
TRACK P3 (NEXT.md §8). With the tower swapped from the custom 12-channel encoder to a FROZEN pretrained PPG foundation model (Pulse-PPG or PaPaGei-S per-minute embeddings, patched to the incumbent PL120 geometry through a 512->192 linear adapter and the cross-modal projector), MedGemma-1.5-4B + LoRA reading those tokens as soft tokens and emitting the JSON slots recovers hr_raw within the measured seed band (0.416 bpm, 2 sigma) of an attention head trained over the IDENTICAL frozen tokens, and reads the state clause no worse than the head by 2 sigma — i.e. a field-grade pretrained tower changes the formatter verdict. Split-honest (split: train on every stage), scored on held-out subjects of the PPG-bearing corpora, with the narrator over the head's numbers as the third arm. This is the fair per-task frozen-probe-vs-LLM ablation on a PPG FM that PulseLM (2603.03331) did not run: its 'Deaf' head baseline is task-blind (one answer vocabulary across 12 tasks, never sees the question).

## Rationale
The resolved verdict on this problem (head 0.812 vs LLM 2.843 bpm, 75% of the loss in the forward pass; rule-out the-llm-s-emission-loss-was-an-artefact-of-the-incumbent-s-weak-tower) was measured on two custom towers, PL120 and PL60, and the better tower lost MORE (58% -> 66% of skill discarded). A pretrained foundation-model tower is the one remaining tower the field would ask about, and PulseLM is the closest published system (frozen PPG FM -> one prefix token -> LLM + LoRA) yet reports no fair head baseline; OpenTSLM (2510.02410) and SensorLM report none either. The committed prediction is that the verdict HOLDS: the LLM emits >= 1.5x the head's hr_raw MAE, because the loss is in the interface, not the tower (§2d.1-2). Either outcome is publishable; a pass would reopen the soft-token path under NEXT.md §6.5's condition. Precondition: P1's g0 (the FM tokens carry HR), not P1's g2 — the HR ablation stands on its own.

## Plan
Precondition: p1 g0 passes. (i) scratch/t1b_heads_v41_tower.py --tower ppgfm:<model>: attention head + mean-pool ridge over the frozen FM tokens, same seed, same held-out rows (PPG-bearing held-out subjects; rows listed in the capture). (ii) Phase C profile lora_proxy_12ch_ppgfm (default-off): incumbent recipe, split: train, PL120 geometry (PL60 does not fit Phase C on the 5070), encoder frozen, tokens = FM minute embeddings -> 512->192 adapter -> patching -> projector; ~2.5 GPU-h. Scored by scratch/emission_diversity_audit.py on the same rows; read scale before MAE. (iii) scratch/n_narrator_prototype.py over (i)'s numbers (label + numbers, never a rule). Optional fourth arm only if (ii) is context-bound: Flamingo-style cross-attention binding (OpenTSLM). Record eiv_splithonest_heldout_hr_raw_mae_bpm for (ii) with (i) as the comparator in the same record; hr_peak and the state clause alongside.

## Links
Tests problem [[is-the-frozen-llm-load-bearing-or-a-formatter]].
Predicts [[eiv_splithonest_heldout_hr_raw_mae_bpm]] (up).
Gate `g1-llm-within-seed-band-of-head` (blocking): Split-honest held-out rows: MedGemma+LoRA soft-token hr_raw MAE <= attention-head MAE + 0.416 (2 x measured seed sigma 0.208), scale in [0.85, 1.35]. Predicted to FAIL.
Gate `g2-state-clause-not-worse` (advisory): State clause AUC of the LLM's emitted label on the same rows >= the head's thresholded AUC - 2 sigma of the relevant cell (lab 0.013 / free-living 0.027), reported per cell.
Gate `g3-narrator-fidelity` (advisory): Narrator over the head's numbers: eiv_narration_numeric_fidelity_mae_bpm <= 0.5 and 0 unparseable rows, reproducing exp-n on the new tower.
Cites [[pulselm-2026-ppg-text-benchmark-2603-03331]], [[opentslm-2025-time-series-language-models-2510-02410]], [[tan-2024-llm-useful-timeseries-2406-16964]], [[sensorfm-2026-2605-22759]], [[pulse-ppg-2025-field-trained-2502-01108]], [[papagei-2025-open-ppg-fm-2410-20542]].

#medgemma #cross-modal #readout #control #optihealth-eiv #matched-baseline
