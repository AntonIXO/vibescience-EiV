---
ambition_class: mechanism
created_at: '2026-09-03T17:49:53+00:00'
former_ids: []
gap_closure_estimate: 0.3
gates:
- blocking: true
  description: 'Per-minute FM embedding -> ridge -> device hr_mean, held-out subjects:
    MAE <= 0.5 x always-median MAE, for each FM (not the twin). Pulse-PPG WESAD binary-stress
    linear probe within +/-0.05 of the published F1 0.876 (sanity bound, protocol
    differs). Fail = extraction defect; nothing is concluded about the route.'
  diagnostic_id: eiv_state_auc_over_null_lab
  id: g0-instrument-validity
  mde: null
- blocking: false
  description: 'WESAD (E4 BVP) held-out: FM-only AUC >= 0.90 under gb; union(raw_multiscale
    ∪ FM)/gb over null >= raw_multiscale/gb over null - 0.013 (2 sigma_lab).'
  diagnostic_id: eiv_state_auc_over_null_lab
  id: g1-lab-cell
  mde: 0.013
- blocking: true
  description: 'dryad_ifh_affect held-out subjects, rows identical to exp-f construction:
    FM/gb over null >= raw_multiscale/gb over null + 0.027 AND FM minus its random
    twin >= 0.027, under BOTH ridge and gb. Primary gate. A directional result under
    0.027 is a powered null against the lever.'
  diagnostic_id: eiv_state_auc_over_null_freeliving
  id: g2-free-living-cell
  mde: 0.027
id: p1-frozen-ppg-foundation-model-embeddings-clear-the-sensorfm-bar-on-the-free-living-cell
interventions: []
override_reason: ''
paper_refs:
- papagei-2025-open-ppg-fm-2410-20542
- pulse-ppg-2025-field-trained-2502-01108
- pulselm-2026-ppg-text-benchmark-2603-03331
- wavesfm-2026-hierarchical-wearable-waveforms-2605-09173
- sensorfm-2026-2605-22759
- marco-blanco-2025-rocket-random-projection-theory-2502-17061
predicted_effects:
- diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: up
  magnitude_note: 'Pulse-PPG/gb on dryad held-out subjects: +0.00 to +0.04 above raw_multiscale/gb
    over null — most likely UNDER the 0.027 (2 sigma) bar, i.e. a powered null. PaPaGei
    within +/-0.013 of its random twin. The dryad handcrafted bar itself is unmeasured
    and is computed in the same run.'
- diagnostic_id: eiv_state_auc_over_null_lab
  direction: none
  magnitude_note: WESAD is saturated by features (0.976 AUC); FM alone >= 0.90 AUC
    predicted; union within 2 sigma (0.013) of features. The informative lab reading
    is the CogWear E4-vs-Watch4 cross-device slice, reported not gated.
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
problem_tags:
- optihealth-eiv
- representation-learning
- matched-baseline
reopen_reason: ''
reopened_from: null
status: proposed
supersedes: null
topic_tags:
- wearables
- readout
- control
- baseline
- optihealth
---

## Statement
TRACK P1 (NEXT.md §8). A FROZEN open-weight PPG foundation model over RAW wrist PPG — Pulse-PPG (50 Hz, field-trained) as the primary arm, PaPaGei-S (10 s @ 125 Hz) as the second — mean-pooled to the minute grid and multi-scale-pooled at W in {30,120,240} exactly like raw_multiscale, read out by ridge and gradient boosting with own permutation nulls, beats the 15 handcrafted HR/HRV features on the FREE-LIVING momentary flow-vs-stress cell (dryad_ifh_affect, Samsung-watch ppg.csv, EMA stress_level, held-out subjects) by at least 2 sigma (0.027 AUC over null), and beats its own random-init twin (same ResNet, seeded weights, same preprocessing) by the same margin, under both estimator families. On the lab cell (WESAD, E4 BVP) the FM alone reads >= 0.90 AUC and the union with the features is not worse than the features. This is the SensorFM bar (frozen embedding + linear/GBM head > engineered features) applied to a pretrained encoder on a NEW input (raw waveform), not a fourth Phase A objective on the minute tensor — rule-out that-changing-the-phase-a-objective-caption-d6b326 does not cover it.

## Rationale
Three Phase A objectives on the 12-channel minute tensor gave one answer: no trained encoder beats raw_multiscale or its random twin on held-out subjects (exp-c1, exp-c3b, exp-h, exp-e, exp-f). The one input the custom encoder never saw is the waveform. The field's evidence that waveform embeddings carry wearable stress: Pulse-PPG's own linear probe reads WESAD binary stress F1 0.876 vs naive 0.407 and MOODS field stress 0.540 vs 0.386 (arXiv 2502.01108); PaPaGei evaluates WESAD valence/arousal on E4 64 Hz wrist PPG (2410.20542); PulseLM (2603.03331) finds PulsePPG > PaPaGei on every wearable source and names stress one of the three weak 10-s tasks. So the prior is LOW for free-living: the 15 features already carry what wrist HR/HRV carries (§2d.6), and 10-s morphology may add nothing at 21 subjects. The committed prediction is therefore a small positive effect most likely UNDER the bar (a powered null), with PaPaGei inside its twin's band. LifeSnaps (the 49-subject cell, +0.045 bar) has no raw PPG; the free-living claim of this route is made on dryad (21-23 subjects) or not at all, so the dryad handcrafted bar is computed in the same run on the same rows. Instrument validity gate g0: FM embedding -> ridge -> device hr_mean on held-out subjects must read <= 0.5x the always-median MAE (a PPG encoder that cannot read HR is an extraction defect), and the Pulse-PPG WESAD probe should land within 0.05 of the published 0.876 (protocol differs; sanity bound).

## Plan
P0 (remote): verify dryad ppg column is a waveform and its rate; per-segment SQI (PaPaGei skewness_sqi); gap-aware 10-s segmentation aligned to (sample_idx, epoch) via window start_time + anchor clock. P1: extract PaPaGei-S, Pulse-PPG and their random twins per segment (~1e7 segments, 1-2 GPU-h), store per-minute f16 512-d + SQI + n_seg per subject (outputs/ppgfm/<model>/<dataset>/<subject>.npz), ship to this host. Readouts: scratch/g5_pool_ceiling.py --features ppgfm:<model>[@random] on rows byte-identical to exp-c1/exp-f (minutes without PPG imputed with the train-fold median), PCA-50 then mean+std at W in {30,120,240}, GroupKFold by subject, 100 within-window permutations, ridge + gb, --datasets dryad_ifh_affect / wesad / consumer_grade_wearables / case / nurse_stress separately, --fold-seeds 6 for the partition sd. Record eiv_state_auc_over_null_freeliving (dryad), eiv_state_auc_over_null_lab (WESAD), and the g0 HR check. Outcomes: g2 pass -> fund P2 (WavesFM-style temporal model) and P3 (head-vs-LLM); g2 fail with g0 pass -> route closed for free-living state, kept for HR/morphology heads, no LLM arm funded on it; g0 fail -> extraction defect, nothing concluded.

## Links
Tests problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].
Predicts [[eiv_state_auc_over_null_freeliving]] (up).
Predicts [[eiv_state_auc_over_null_lab]] (none).
Gate `g0-instrument-validity` (blocking): Per-minute FM embedding -> ridge -> device hr_mean, held-out subjects: MAE <= 0.5 x always-median MAE, for each FM (not the twin). Pulse-PPG WESAD binary-stress linear probe within +/-0.05 of the published F1 0.876 (sanity bound, protocol differs). Fail = extraction defect; nothing is concluded about the route.
Gate `g1-lab-cell` (advisory): WESAD (E4 BVP) held-out: FM-only AUC >= 0.90 under gb; union(raw_multiscale ∪ FM)/gb over null >= raw_multiscale/gb over null - 0.013 (2 sigma_lab).
Gate `g2-free-living-cell` (blocking): dryad_ifh_affect held-out subjects, rows identical to exp-f construction: FM/gb over null >= raw_multiscale/gb over null + 0.027 AND FM minus its random twin >= 0.027, under BOTH ridge and gb. Primary gate. A directional result under 0.027 is a powered null against the lever.
Cites [[papagei-2025-open-ppg-fm-2410-20542]], [[pulse-ppg-2025-field-trained-2502-01108]], [[pulselm-2026-ppg-text-benchmark-2603-03331]], [[wavesfm-2026-hierarchical-wearable-waveforms-2605-09173]], [[sensorfm-2026-2605-22759]], [[marco-blanco-2025-rocket-random-projection-theory-2502-17061]].

#wearables #readout #control #baseline #optihealth #optihealth-eiv #representation-learning #matched-baseline
