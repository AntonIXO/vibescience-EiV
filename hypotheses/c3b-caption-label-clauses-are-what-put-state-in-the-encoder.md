---
ambition_class: mechanism
created_at: '2026-09-02T21:37:01+00:00'
former_ids: []
gap_closure_estimate: 0.2
gates:
- blocking: true
  description: (enc_v36labels - twin) - (enc_v28affect01 - twin) >= +0.02 under multi-span
    gb AND positive under multi-span ridge, all four cells from the same g5 harness
    on identical rows and folds
  diagnostic_id: eiv_state_auc_trained_minus_random
  id: g1
  mde: 0.0141
- blocking: true
  description: each encoder's random twin is rebuilt from its OWN geometry (both PL120/ST60
    here, so the twins are comparable by construction) and scored in the same invocation
  diagnostic_id: eiv_state_auc_trained_minus_random
  id: g2
  mde: null
- blocking: false
  description: 'advisory: dataset-provenance shortcut bal-acc on the v36labels tokens
    does not exceed v28affect01''s by more than +0.05, else the gain is reported as
    confounded with corpus identity'
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  id: g3
  mde: null
id: c3b-caption-label-clauses-are-what-put-state-in-the-encoder
interventions: []
override_reason: ''
paper_refs:
- sensorlm-2025-2506-09108
- marco-blanco-2025-rocket-random-projection-theory-2502-17061
- sensorfm-2026-2605-22759
predicted_effects:
- diagnostic_id: eiv_state_auc_trained_minus_random
  direction: up
  magnitude_note: (v36labels - its twin) minus (v28affect01 - its twin) >= +0.02 under
    multi-span gb, outside the +/-0.0141 band. Inside the band or negative = caption
    clauses do not carry state.
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: enc_v36labels multi-span gb over_null >= +0.09 (v41's PL60 encoder
    reads +0.0852 on the same rows).
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
problem_tags:
- optihealth-eiv
- representation-learning
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: c3-labels-in-caption-encoder-carries-more-state-than-hr-caption-encoder
topic_tags:
- optihealth
- sensor-text
- control
- state-supervision
- readout
- baseline
---

## Statement
TRACK C-3 (CORRECTED COMPARATOR). The encoder learns what the alignment caption says, tested as a ONE-VARIABLE contrast: v36labels (caption_style `hr_dispersion_labels` -- HR statistics PLUS the raw label clauses 'valence X' / 'stress X' on 35.1% of roster windows) against v28affect01 (caption_style `hr_only`), which are IDENTICAL on every other Phase A key including patch_len 120 / stride 60 / d_model 192 / input_dim 24 / state_patch_weight 0.0. Measured as trained-minus-its-own-random-twin on the g5 clean pool (2,136 rows), encoder-only, single and multi span, under BOTH estimator families. Committed prediction: (enc_v36labels - twin_v36labels) - (enc_v28affect01 - twin_v28affect01) >= +0.02 under gb, and positive under multi-span ridge. If the delta is inside the +/-0.0141 band or negative, adding window-level label clauses to the caption does NOT put momentary state into the encoder, and the caption route is closed for clause-style captions -- the next caption arm must carry minute-stamped SEMANTIC content (SensorLM Table 4) or nothing. CONFOUND THAT CANNOT BE REMOVED and is reported beside the number: the clause is a measured corpus-provenance shortcut (bal-acc lift +0.294 valence / +0.379 stress), so a positive that arrives WITH a provenance jump is reported as confounded, not as support.

## Rationale
SUPERSEDES c3-labels-in-caption-...: that version used v41rebased (+0.0340 trained-minus-twin) as the comparator, but the config diff shows v41 and v36labels carry the SAME caption_style (`hr_dispersion_labels`) and differ in GEOMETRY (PL60/ST30 vs PL120/ST60) and state supervision (state_patch_weight 0.5 vs 0.0). Comparing them would have measured patch length and called it caption. v28affect01 is the correct control: `hr_only` caption, everything else identical to v36labels. Motivation unchanged -- SensorLM (arXiv:2506.09108) Table 4: statistical/structural captions give 0.49-0.51 on concept readout while semantic captions give 0.71-0.84; ROCKET theory (arXiv:2502.17061): trained ~ random means the objective was not aligned with the label, so the caption is the lever. v36labels' DO-NOT-RETRY entry covers it as an HR/calibration lever (held-out 4.097, calibration lost); its encoder's STATE readout has never been measured, and this arm does not touch HR. CPU only, two existing checkpoints, ~1 CPU-hour.

## Plan
CPU, after C-1 frees the cores. (1) g5_pool_ceiling.py --chain outputs/checkpoints/v36labels/phase_a --only enc --out logs/g5_enc_v36labels.json (8 arms: enc/enc@random x single/multi span x ridge/gb). (2) same for outputs/checkpoints/v28affect01/phase_a. (3) compare the four trained-minus-twin deltas. Clean pool, so directly comparable to v41's published cells.

## Links
Tests problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].
Predicts [[eiv_state_auc_trained_minus_random]] (up).
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Gate `g1` (blocking): (enc_v36labels - twin) - (enc_v28affect01 - twin) >= +0.02 under multi-span gb AND positive under multi-span ridge, all four cells from the same g5 harness on identical rows and folds
Gate `g2` (blocking): each encoder's random twin is rebuilt from its OWN geometry (both PL120/ST60 here, so the twins are comparable by construction) and scored in the same invocation
Gate `g3` (advisory): advisory: dataset-provenance shortcut bal-acc on the v36labels tokens does not exceed v28affect01's by more than +0.05, else the gain is reported as confounded with corpus identity
Cites [[sensorlm-2025-2506-09108]], [[marco-blanco-2025-rocket-random-projection-theory-2502-17061]], [[sensorfm-2026-2605-22759]].
Supersedes [[c3-labels-in-caption-encoder-carries-more-state-than-hr-caption-encoder]].

#optihealth #sensor-text #control #state-supervision #readout #baseline #optihealth-eiv #representation-learning #matched-baseline
