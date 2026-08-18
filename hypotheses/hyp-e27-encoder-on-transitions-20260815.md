---
created_at: '2026-08-15T08:31:16+00:00'
former_ids: []
gates:
- blocking: true
  description: PRIMARY, and it is the GO/NO-GO for the Phase B retrain. Best encoder-token
    arm MINUS the shallow summary arm on the identical transition-pair rows and identical
    subject-grouped split must be >= +0.05 AUC. MDE 0.05, inherited from E22 which
    measured +0.0303 on the daily-label task. If this clears, encoder representation
    carries transition information that percentile features miss and the retrain has
    expected value; if it does not, the retrain is not justified by representation
    and only the SUPPLY argument remains.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g1_encoder_beats_shallow_on_transitions
  mde: 0.05
- blocking: true
  description: SHUFFLE CONTROL, inherited from E26. The best encoder arm refit on
    WITHIN-WINDOW permuted A/B labels must give |AUC-0.5| < 0.05. Encoder features
    are ~4600-dimensional against ~3200 rows, so a high-dimensional readout could
    in principle manufacture apparent skill; the shuffle destroys the state contrast
    while preserving subject, day, window and feature distribution. Without this the
    whole arm is uninterpretable.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g2_shuffle_control_dead
  mde: 0.05
- blocking: false
  description: LOCALITY at the transition scale. enc_local (tokens covering [epoch-120,
    epoch)) minus enc_global (all 23 tokens). E22 found local > global consistently
    on the daily-label task (+0.054 to +0.066, four of four arms). A transition task
    should favour locality MORE, because the two epochs share a day and differ only
    locally — a global read cannot distinguish them except through whatever the pooled
    day carries. Informational.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g3_locality_at_transition_scale
  mde: null
id: hyp-e27-encoder-on-transitions-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'Best encoder-token arm beats the shallow summary arm (E26 baseline
    0.6329) by >= +0.05 on the identical transition rows. HONEST PRIOR: I expect this
    to FAIL. E22 measured only +0.0303 for encoder-over-shallow on lifesnaps daily
    affect, and the same encoder trained under InfoNCE against mean-only captions
    has no reason to have preserved within-day state structure. I am running it because
    it is the cheap gate on a 4-GPU-hour arm, and because a null here is decision-relevant:
    it would mean the Phase B retrain is justified by SUPPLY (E25/E26) rather than
    by representation.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- wearables
- cross-modal
- pooling
- baseline
- control
- epoch-resolution
---

## Statement
H-E27 (encoder representation on the transition task). E26 established real, shuffle-controlled state signal on within-window transitions using shallow summary features (HR 0.6329 vs clock 0.5129, shuffle 0.5151). This arm asks the decision-relevant question BEFORE spending ~4 GPU-hours on a Phase B retrain: does the trained encoder's representation see that transition contrast BETTER than percentile features do?

PREDICTION: the best encoder-token arm beats the shallow arm by >= +0.05 AUC on the identical transition rows and identical subject-grouped split.

GO/NO-GO SEMANTICS, committed before running. PASS -> encoder representation carries transition structure the summary features miss, so retraining Phase B to exploit within-window contrasts has representational upside and the GPU spend is justified. FAIL -> the encoder adds nothing on this task, the retrain is justified ONLY by the supply argument (E25's non-degenerate triplets), and the cheaper move is to ship the shallow E26 detector as a per-window state head.

## Rationale
E26 confirmed real state signal (HR 0.6329 vs clock 0.5129, shuffle 0.5151) in the one design where trait is held constant by construction. The natural next step is the Phase B triplet retrain, but that needs a NEW within-window miner (TripletStateDataset mines on infer_state_label, one collapsed label per window, discarding exactly these contrasts) plus ~4 GPU-hours across >=3 seeds.

Before spending that, ask the cheap question: does the trained encoder representation see the transition contrast better than percentile features do? E22 asked the analogous question on DAILY labels and found +0.0303 — under its bar. But E22's task was daily-reduced affect; nobody has asked it of the TRANSITION task, where the two epochs share a subject and a day and differ only locally. That is a materially different question and it is the one that determines whether the retrain has representational upside or is purely a supply play.

This is the same discipline that saved GPU-hours four times today (E20, E21, E23, E25): measure the premise before funding the intervention.

## Plan
scratch/e27_encoder_transitions.py -> logs/e27_encoder_transitions.json. GPU, ~10 minutes.

SAME ROWS AS E26. Same 1274 transition windows, same flow-vs-stress pair, same W=120 peri-epoch alignment, same GroupKFold-by-subject split. The ONLY variable is the feature source, so any difference is representation.

Load the v5d Phase-B temporal_encoder (patch_len=120, stride=60 -> 23 tokens/channel, input_dim=24, d_model=192), strict weight load. Encode each unique transition window ONCE, index per epoch.

ARMS on identical rows:
  clock_only            hour sin/cos + minute-of-day        CONFOUND FLOOR (E26: 0.5129)
  shallow               the E26 HR/HRV summary scalars      THE BAR TO BEAT (E26: 0.6329)
  enc_global_avg        all 23 tokens/channel, avg
  enc_global_avgmax     all 23 tokens/channel, avg+max
  enc_local_avg         tokens covering [epoch-120, epoch), avg
  enc_local_avgmax      same, avg+max
  SHUFFLE               best encoder arm on within-window permuted labels
RF(300, min_samples_leaf=3), seed 0, NaNs median-imputed within the training fold only.

The avg/avgmax split is carried because exp-e19 showed the pooling operator moved excursion readout by +0.4042 on this very encoder; reading affect off avg-pooled tokens alone would repeat that mistake.

DECISION RULE, COMMITTED BEFORE RUNNING: g1 pass -> build the within-window triplet miner and run the Phase B retrain (option 1). g1 fail -> do NOT spend the ~4 GPU-hours on representation grounds; ship the shallow E26 detector as a per-window state head (option 3) and leave the retrain justified only by supply.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1_encoder_beats_shallow_on_transitions` (blocking): PRIMARY, and it is the GO/NO-GO for the Phase B retrain. Best encoder-token arm MINUS the shallow summary arm on the identical transition-pair rows and identical subject-grouped split must be >= +0.05 AUC. MDE 0.05, inherited from E22 which measured +0.0303 on the daily-label task. If this clears, encoder representation carries transition information that percentile features miss and the retrain has expected value; if it does not, the retrain is not justified by representation and only the SUPPLY argument remains.
Gate `g2_shuffle_control_dead` (blocking): SHUFFLE CONTROL, inherited from E26. The best encoder arm refit on WITHIN-WINDOW permuted A/B labels must give |AUC-0.5| < 0.05. Encoder features are ~4600-dimensional against ~3200 rows, so a high-dimensional readout could in principle manufacture apparent skill; the shuffle destroys the state contrast while preserving subject, day, window and feature distribution. Without this the whole arm is uninterpretable.
Gate `g3_locality_at_transition_scale` (advisory): LOCALITY at the transition scale. enc_local (tokens covering [epoch-120, epoch)) minus enc_global (all 23 tokens). E22 found local > global consistently on the daily-label task (+0.054 to +0.066, four of four arms). A transition task should favour locality MORE, because the two epochs share a day and differ only locally — a global read cannot distinguish them except through whatever the pooled day carries. Informational.

#wearables #cross-modal #pooling #baseline #control #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
