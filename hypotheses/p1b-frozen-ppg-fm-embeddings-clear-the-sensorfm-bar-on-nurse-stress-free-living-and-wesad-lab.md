---
ambition_class: mechanism
created_at: '2026-09-04T22:47:48+00:00'
former_ids: []
gap_closure_estimate: 0.3
gates:
- blocking: true
  description: 'Per-minute FM embedding -> ridge -> HR on held-out subjects: MAE <=
    0.5 x always-median MAE on PPG-DaLiA ECG-truth HR (measured 2026-09-04: Pulse-PPG
    10.6379/22.7271 = 0.468 PASS; PaPaGei 14.3324/22.683 = 0.632 FAIL, and below its
    twin). Second half: Pulse-PPG WESAD binary-stress LOSO linear probe F1 within
    +/-0.05 of the published 0.876 (protocol differs; sanity bound). Only Pulse-PPG
    proceeds to gated state claims.'
  diagnostic_id: eiv_state_auc_over_null_lab
  id: g0-instrument-validity
  mde: null
- blocking: false
  description: 'WESAD clean pool (449 rows / 15 subjects, GroupKFold by subject):
    ppgfm_ms:pulse_ppg/gb AUC >= 0.90; union_ppg_ms:pulse_ppg/gb over null >= raw_multiscale/gb
    over null - 0.013 (2 sigma_lab). Advisory.'
  diagnostic_id: eiv_state_auc_over_null_lab
  id: g1-lab-cell
  mde: 0.013
- blocking: true
  description: 'nurse_stress SOLO cell (1,685 rows / 21 windows / 12 subjects, GroupKFold
    by subject, own within-window permutation null): ppgfm_ms:pulse_ppg/gb over null
    >= raw_multiscale/gb over null + 0.027 AND ppgfm_ms:pulse_ppg over null minus
    ppgfm_ms:pulse_ppg@random over null >= 0.027 AND ppgfm_ms:pulse_ppg/gb AUC >=
    clock/gb AUC + 0.027, each under BOTH ridge and gb (the AUC-vs-clock clause under
    gb only). Primary, blocking. A directional result under 0.027 is a powered null
    against the lever.'
  diagnostic_id: eiv_state_auc_over_null_freeliving
  id: g2-free-living-cell
  mde: 0.027
id: p1b-frozen-ppg-fm-embeddings-clear-the-sensorfm-bar-on-nurse-stress-free-living-and-wesad-lab
interventions: []
override_reason: ''
paper_refs:
- papagei-2025-open-ppg-fm-2410-20542
- pulse-ppg-2025-field-trained-2502-01108
- pulselm-2026-ppg-text-benchmark-2603-03331
- sensorfm-2026-2605-22759
- marco-blanco-2025-rocket-random-projection-theory-2502-17061
- amin-2025-stress-reproducibility-consumer-wearables-2505-05694
predicted_effects:
- diagnostic_id: eiv_state_auc_over_null_freeliving
  direction: up
  magnitude_note: 'nurse_stress solo cell, Pulse-PPG ppgfm_ms/gb over null minus raw_multiscale/gb
    over null: +0.00 to +0.04, most likely UNDER the 0.027 (2 sigma) bar -> powered
    null. Twin gap predicted positive but under 0.027 under ridge; the clock arm predicted
    to read within 0.03 of raw_multiscale.'
- diagnostic_id: eiv_state_auc_over_null_lab
  direction: none
  magnitude_note: 'WESAD clean pool: features saturate (0.984 gb). FM alone predicted
    BELOW 0.90 AUC under gb (smoke ridge at W=30 read 0.49); union within 0.013 of
    raw_multiscale. Reported, advisory.'
- diagnostic_id: eiv_state_auc_trained_minus_random
  direction: up
  magnitude_note: 'Pulse-PPG minus its seeded random twin, over_null, per cell: predicted
    +0.01 to +0.05 on WESAD under gb (the twin is a ROCKET-style random projection
    and will not be far behind), near zero on nurse_stress.'
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
problem_tags:
- optihealth-eiv
- representation-learning
- matched-baseline
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: p1-frozen-ppg-foundation-model-embeddings-clear-the-sensorfm-bar-on-the-free-living-cell
topic_tags:
- wearables
- readout
- control
- baseline
- optihealth
---

## Statement
TRACK P1, RE-BASED AFTER P0 (2026-09-04/05). The free-living cell p1 pre-registered does not exist: dryad_ifh_affect's Samsung ppg.csv is a 19.6 Hz stream (median inter-sample step 50-51 ms on all 24 participants, logs/p0_ppg_inventory.json), and both towers' band-passes are UNDEFINED there — PaPaGei's 0.5-12 Hz Chebyshev-II and Pulse-PPG's 0.5-10 Hz Butterworth both need Nyquist above the upper cutoff (9.8 Hz), and scipy refuses both designs. So the free-living cell of this route is nurse_stress (E4 BVP 64 Hz, observer-coded stress_level with rule >=1.5 stress / <=0.5 flow; 1,685 flow-vs-stress transition rows over 21 windows / 12 subjects), scored ALONE as its own cell beside a CLOCK-ONLY control arm (the four circadian channels; clock alone read 0.6358 on this cohort, 2026-08) — never pooled, per exp-047's rule. Claim: a FROZEN Pulse-PPG (50 Hz, field-trained) per-minute embedding, PCA-50 then mean+std pooled at W in {30,120,240} exactly like raw_multiscale, read by ridge and gradient boosting with own within-window permutation nulls on rows byte-identical to g5's construction, beats the 15 handcrafted HR/HRV features on nurse_stress by >= 2 sigma (0.027 over null), beats its own random-init twin (same ResNet-26, torch.manual_seed(0), same preprocessing, outputs/ppgfm/pulse_ppg_random) by 0.027, and beats the clock arm by 0.027, under BOTH estimator families. On the lab cell (WESAD clean pool, 449 rows / 15 subjects, GroupKFold by subject) the FM alone reads >= 0.90 AUC under gb and union(raw_multiscale ∪ FM) is not below raw_multiscale by more than 0.013. PaPaGei-S is carried as the second arm but FAILED g0 (DaLiA ECG-truth HR MAE 14.33 vs always-median 22.68, ratio 0.632; loses to its own twin 12.01) and so its state numbers are reported, not gated. CASE (finger 1000 Hz, 77 rows / 11 subjects) is reported as a secondary lab slice via a minute_rel join. This is the SensorFM bar on a pretrained encoder over a NEW input (the raw waveform); rule-out that-changing-the-phase-a-objective-caption-d6b326 covers objectives on the minute tensor and does not cover it.

## Rationale
P0 measured what the plan told it to verify: dryad's 'ppg' column is a waveform but at ~19.6 Hz, not ~64 Hz — the processor's comment was wrong. Below every rate either FM was evaluated at (60 Hz floor) and below both filter designs' Nyquist. Upsampling would be out-of-distribution, not equivalent (PaPaGei's objective reads the dicrotic notch, which is not inside a 9.8 Hz Nyquist). nurse_stress is the only remaining free-living raw-PPG cohort with a state rule. Its known defect is that shift-schedule timing carries the label (clock-only 0.6358 vs majority 0.771; exp-047 excluded it from every POOLED number because its 43% share sank zero-parameter arms). Scored alone, with the schedule priced by a clock arm and the permutation null drawn within window as always, it is a legitimate — if weaker — free-living cell. g0 is already decided: Pulse-PPG passes on ECG ground truth (DaLiA 10.64 vs 22.73, ratio 0.468; twin 0.603, so +3.06 bpm over the twin), PaPaGei fails and loses to its twin — consistent with Pulse-PPG's wrist/field pretraining vs PaPaGei's clinical fingertip pretraining. Prior for the state claim stays LOW: Pulse-PPG's own field-stress probe reads F1 0.54 vs 0.39 naive; the 15 features already carry what wrist HR/HRV carries (§2d.6); the per-minute FM -> canonical device-HR ridge reads only 9.66 vs 12.85 (ratio 0.75) on these corpora, i.e. the embedding's HR content is modest at the minute grid; and a smoke reading of the single-scale ridge on WESAD came in at AUC 0.49 where HR features read 0.96. Committed prediction: nurse_stress Pulse-PPG/gb lands +0.00 to +0.04 over raw_multiscale/gb — most likely UNDER the 0.027 bar (powered null); on WESAD the FM alone reads BELOW 0.90 under gb (revised from p1 on the smoke reading) and the union is within 0.013 of the features.

## Plan
Code: scratch/g5_pool_ceiling.py --ppgfm <store,...> --no-encoder (Track P arms: ppgfm:<tower>/{ridge,gb} at W=30, ppgfm_ms:<tower> at {30,120,240}, union_ppg_ms:<tower>/gb; a <tower>_random store is the twin; clock/{ridge,gb} control; --datasets lifts the nurse_stress exclusion for a solo cell). Join validated per dataset at lag 0 (100% overlap of store minutes with measured-HR minutes on wesad/nurse_stress/wearable_exam_stress/brain_wearable/consumer_grade). Runs: (1) --pool clean --datasets wesad, all four stores, --fold-seeds 6 --device-slice -> logs/g5_ppgfm_wesad.json; (2) --pool clean --datasets nurse_stress, same -> logs/g5_ppgfm_nurse.json; (3) --pool clean --datasets case -> logs/g5_ppgfm_case.json (secondary). g0's second half: LOSO logistic-regression WESAD binary stress (stress vs baseline+amusement) on Pulse-PPG per-minute embeddings, F1 reported against the published 0.876 as a sanity bound. Record eiv_state_auc_over_null_freeliving from (2), eiv_state_auc_over_null_lab from (1), eiv_state_auc_trained_minus_random from each. Outcomes: g2 pass -> first encoder in this project to clear the SensorFM bar; fund P2/P3 on product grounds. g2 fail with g0 pass -> waveform morphology adds nothing to HR/HRV statistics for free-living stress at 12 subjects; route closed for state, kept for HR/morphology heads; P3 funded on paper grounds only (its g0 precondition holds for Pulse-PPG).

## Links
Tests problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].
Predicts [[eiv_state_auc_over_null_freeliving]] (up).
Predicts [[eiv_state_auc_over_null_lab]] (none).
Predicts [[eiv_state_auc_trained_minus_random]] (up).
Gate `g0-instrument-validity` (blocking): Per-minute FM embedding -> ridge -> HR on held-out subjects: MAE <= 0.5 x always-median MAE on PPG-DaLiA ECG-truth HR (measured 2026-09-04: Pulse-PPG 10.6379/22.7271 = 0.468 PASS; PaPaGei 14.3324/22.683 = 0.632 FAIL, and below its twin). Second half: Pulse-PPG WESAD binary-stress LOSO linear probe F1 within +/-0.05 of the published 0.876 (protocol differs; sanity bound). Only Pulse-PPG proceeds to gated state claims.
Gate `g1-lab-cell` (advisory): WESAD clean pool (449 rows / 15 subjects, GroupKFold by subject): ppgfm_ms:pulse_ppg/gb AUC >= 0.90; union_ppg_ms:pulse_ppg/gb over null >= raw_multiscale/gb over null - 0.013 (2 sigma_lab). Advisory.
Gate `g2-free-living-cell` (blocking): nurse_stress SOLO cell (1,685 rows / 21 windows / 12 subjects, GroupKFold by subject, own within-window permutation null): ppgfm_ms:pulse_ppg/gb over null >= raw_multiscale/gb over null + 0.027 AND ppgfm_ms:pulse_ppg over null minus ppgfm_ms:pulse_ppg@random over null >= 0.027 AND ppgfm_ms:pulse_ppg/gb AUC >= clock/gb AUC + 0.027, each under BOTH ridge and gb (the AUC-vs-clock clause under gb only). Primary, blocking. A directional result under 0.027 is a powered null against the lever.
Cites [[papagei-2025-open-ppg-fm-2410-20542]], [[pulse-ppg-2025-field-trained-2502-01108]], [[pulselm-2026-ppg-text-benchmark-2603-03331]], [[sensorfm-2026-2605-22759]], [[marco-blanco-2025-rocket-random-projection-theory-2502-17061]], [[amin-2025-stress-reproducibility-consumer-wearables-2505-05694]].
Supersedes [[p1-frozen-ppg-foundation-model-embeddings-clear-the-sensorfm-bar-on-the-free-living-cell]].

#wearables #readout #control #baseline #optihealth #optihealth-eiv #representation-learning #matched-baseline
