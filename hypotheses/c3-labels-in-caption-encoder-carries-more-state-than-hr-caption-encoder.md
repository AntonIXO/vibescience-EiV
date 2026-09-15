---
ambition_class: mechanism
created_at: '2026-09-02T20:54:22+00:00'
former_ids: []
gap_closure_estimate: 0.2
gates:
- blocking: true
  description: (enc_v36labels - twin_v36labels) - (enc_v41 - twin_v41) >= +0.02 under
    gb AND positive under multi-span ridge
  diagnostic_id: eiv_state_auc_trained_minus_random
  id: g1
  mde: 0.0141
- blocking: false
  description: dataset-provenance shortcut bal-acc from the v36labels encoder tokens
    does not exceed v41's by more than +0.05 -- otherwise the state gain is confounded
    with corpus identity and is reported as such
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  id: g2
  mde: null
- blocking: true
  description: both encoders' random twins are rebuilt from their OWN geometry (v36labels
    is PL120/23 patches; v41 is PL60/47) -- never share a twin across geometries
  diagnostic_id: eiv_state_auc_trained_minus_random
  id: g3
  mde: null
id: c3-labels-in-caption-encoder-carries-more-state-than-hr-caption-encoder
interventions: []
override_reason: ''
paper_refs:
- sensorlm-2025-2506-09108
- marco-blanco-2025-rocket-random-projection-theory-2502-17061
predicted_effects:
- diagnostic_id: eiv_state_auc_trained_minus_random
  direction: up
  magnitude_note: 'enc_v36labels multi-span gb: trained minus own twin >= +0.055 (v41
    reads +0.0340); delta of deltas >= +0.02, outside the +/-0.0141 band. Inside the
    band = caption route closed for window-level clauses.'
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: enc_v36labels/gb over_null >= +0.10 (v41's enc_ms/gb is +0.0852).
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
problem_tags:
- optihealth-eiv
- representation-learning
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- optihealth
- sensor-text
- control
- state-supervision
- readout
- baseline
---

## Statement
TRACK C-3. The encoder learns what the alignment caption says: the v36labels Phase A encoder (outputs/checkpoints/v36labels, caption = hr_dispersion_labels PLUS the raw label clauses 'valence X' / 'stress X' on 35.1% of roster windows) carries MORE momentary state than the v41 encoder whose caption carries only HR statistics and a window label -- measured as trained-minus-its-own-random-twin on the g5 clean pool, encoder-only, multi-span, under both families. Committed prediction: enc_v36labels - twin_v36labels >= +0.055 under gb (v41's is +0.0340), i.e. the caption change adds at least +0.02 over v41's trained-minus-twin, and the delta exceeds the +/-0.0141 band. If it does NOT (delta inside the band or negative), the caption route to state is closed with one CPU-hour spent: a window-level label clause is not the minute-stamped semantic caption SensorLM's Table 4 says is required, and the next caption arm must say WHEN. NOTE the confound this cannot remove and reports beside the number: v36labels' clause is a known corpus-provenance shortcut (bal-acc lift +0.294 valence / +0.379 stress), so a positive result may be 'the encoder learned which corpus' -- the dataset-provenance shortcut diagnostic is recorded alongside and a positive that comes WITH a provenance jump is reported as confounded, not as support.

## Rationale
SensorLM (arXiv:2506.09108, Table 4): statistical-only and structural-only captions give AUROC 0.49-0.51 on concept readout; semantic captions with time spans give 0.71-0.84; statistical captions lift health-state linear probes 0.63 -> 0.67. ROCKET theory (arXiv:2502.17061) says trained ~ random means the objective was not aligned with the label, so the lever is the alignment text. v36labels is the only existing checkpoint whose caption carries any affect label at all, and it was never scored for STATE -- the DO-NOT-RETRY entry on it is about HR calibration (held-out 4.097, calibration lost, seed spread), which this measurement does not touch. Cheapest possible test of the caption route before any Phase A retrain is funded: existing checkpoint, CPU, g5 harness unchanged except for the checkpoint path.

## Plan
CPU. (1) Dump three hr_dispersion_labels captions and three v36labels captions for the same windows; record verbatim what each says about WHEN. (2) g5_pool_ceiling.py --only enc --ckpt outputs/checkpoints/v36labels/phase_a (+ @random twin, PL120 geometry) -> logs/g5_enc_v36labels.json. (3) scratch provenance probe on the same tokens for g2. Compare against logs/g5_enc_multiscale.json (v41). ~1 CPU-hour.

## Links
Tests problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].
Predicts [[eiv_state_auc_trained_minus_random]] (up).
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Gate `g1` (blocking): (enc_v36labels - twin_v36labels) - (enc_v41 - twin_v41) >= +0.02 under gb AND positive under multi-span ridge
Gate `g2` (advisory): dataset-provenance shortcut bal-acc from the v36labels encoder tokens does not exceed v41's by more than +0.05 -- otherwise the state gain is confounded with corpus identity and is reported as such
Gate `g3` (blocking): both encoders' random twins are rebuilt from their OWN geometry (v36labels is PL120/23 patches; v41 is PL60/47) -- never share a twin across geometries
Cites [[sensorlm-2025-2506-09108]], [[marco-blanco-2025-rocket-random-projection-theory-2502-17061]].

#optihealth #sensor-text #control #state-supervision #readout #baseline #optihealth-eiv #representation-learning #matched-baseline

## Resolution (2026-09-03)

SUPERSEDED 2026-09-03 by c3b-caption-label-clauses-are-what-put-state-in-the-encoder (inconclusive / directional_only) BEFORE any compute: this hypothesis compared v36labels against v41rebased, but both share caption_style hr_dispersion_labels, so the contrast would have measured patch length (PL120 vs PL60), not caption content. c3b compares against v28affect01 (hr_only) at matched geometry. Its answer stands for this one: caption effect +0.016..+0.052, and the hr_only control reads BELOW its own random twin on 3 of 4 cells.
