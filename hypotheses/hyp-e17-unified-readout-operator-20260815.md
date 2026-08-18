---
created_at: '2026-08-14T21:44:28+00:00'
former_ids: []
gates:
- blocking: true
  description: 'TASK A PRIMARY. eiv_peak_excursion_increment_r2 = R2(excursion | pooled
    day tokens) - R2(excursion | true daily mean, 1 feature), subject-grouped OOF,
    on the same clean pool. The best non-avg operator (max / avgmax / lse) must exceed
    the avg operator at the SAME token count by >= +0.05 increment. MDE: prior avg-vs-avgmax
    gap on the excursion target was 0.1685 -> 0.2968 (+0.1283) at 23 patches, so this
    design is powered well below +0.05. A gap under +0.05 is a powered null and kills
    the pooling-operator story for excursion.'
  diagnostic_id: eiv_peak_excursion_increment_r2
  id: gA_operator_beats_avg
  mde: 0.05
- blocking: false
  description: 'TASK A SECONDARY (token count, the W3 question folded in). At the
    BEST operator, 144 tokens must beat 23 tokens on increment by >= +0.03. Informational:
    if operator matters and token count does not, W3''s LSM-2-geometry motivation
    is answered cheaply and the GPU stage of rb-w3-patch-grid-token-count-20260815
    should be dropped or rescoped to operator only.'
  diagnostic_id: eiv_peak_excursion_increment_r2
  id: gA_tokens_beat_23
  mde: 0.03
- blocking: true
  description: TASK B PRIMARY, the locality test. At fixed operator and token count,
    AUC(LOCAL readout + clock) - AUC(GLOBAL readout + clock) must be >= +0.03 for
    at least one arm. This is the cleanest possible test of 'the label should read
    its own neighbourhood', because LOCAL and GLOBAL are the SAME tokens from the
    SAME representation differing only in which are read. MDE 0.028 inherited from
    the E16 within-subject permutation null std 0.0271.
  diagnostic_id: eiv_affect_multichannel_increment
  id: gB_local_beats_global
  mde: 0.028
- blocking: true
  description: TASK B TRIVIAL BAR, inherited from E16 and non-negotiable. The best
    TASK B arm's absolute AUC must exceed the within-subject constant predictor (measured
    0.7381 on this exact target/pool). If no arm clears it, cross-subject state detection
    is confirmed DEAD at the representation level regardless of readout, and the recommendation
    is to drop the state slot from the emitted schema rather than ship noise.
  diagnostic_id: eiv_affect_multichannel_increment
  id: gB_beats_within_subject_constant
  mde: 0.7381
id: hyp-e17-unified-readout-operator-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: 'TASK A PRIMARY. At fixed 24h window and fixed token count, replacing
    avg pooling with max/avgmax/lse raises the excursion increment by >= +0.05. Prior
    anchor on 23 patches: avg 0.1685, max 0.1273, avg+max concat 0.2968 on the direct
    excursion target. Predicted ordering avgmax >= lse > avg > max, because avgmax
    retains level AND extremum while max alone discards level.'
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'TASK B PRIMARY. At fixed operator and token count, a LOCAL time-indexed
    readout (tokens covering [ping-120min, ping)) beats a GLOBAL pooled readout of
    the SAME tokens by >= +0.03 AUC over the clock-only control. NOTE the honest tension:
    E16 measured that longer aggregation windows helped, which predicts the OPPOSITE
    sign. This arm separates the two explanations — E16 confounded ''window length''
    with ''which tokens are read'', because shortening the window also threw away
    the rest of the day. Here the day is always present; only the READ changes.'
problem_id: peri-ema-window-destroys-momentary-labels-20260815
problem_tags:
- optihealth-eiv
- matched-baseline
- subject-heldout
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- pooling
- epoch-resolution
- cross-modal
- control
- wearables
- baseline
---

## Statement
H-E17 (unified readout operator). The window-size question and the momentary-state question are ONE question about the READOUT, not two questions about the window. At a FIXED 24h window, the aggregation operator applied to the tokens — not the window length, not the dataset composition — is what limits BOTH (a) daily excursion/hr_peak recovery and (b) momentary state detection, because average pooling destroys extrema and destroys locality simultaneously.

CONCRETELY, two predictions from ONE mechanism:
  A. Replacing avg pooling with max / avg+max / LSE raises the excursion increment over the daily-mean control by >= +0.05 at fixed token count.
  B. Reading only the tokens that COVER the EMA ping (a local, time-indexed readout) beats pooling the whole day by >= +0.03 AUC over a clock-only control, using the SAME tokens and the SAME representation.

If both move, the readout operator is the shared lever and the correct architecture is one 24h encoder with a multi-scale token pyramid plus a time-indexed query decoder (the TimePerceiver arXiv:2512.22550 pattern), NOT a shorter or dynamic window.
If only A moves, excursion is a pooling problem while state is confirmed as trait-ceilinged, and the state slot should be dropped from the emitted schema rather than shipped as noise.
If neither moves, the bottleneck is downstream in the decode/readout stage where E6-E10 already localised the numeric-slot failures, and no encoder-side aggregation change is justified.

## Rationale
Two open failures share one operator. (1) The projector's adaptive_avg_pool2d averages ACROSS patches, and the registered diagnostic already records that this is the discriminating variable for excursion: avg 0.1685 vs avg+max concat 0.2968 on the excursion target at 23 patches. (2) A 24h window holding 1-2 momentary EMA pings is a multiple-instance bag answered by mean-pooling — precisely the baseline attention-MIL was built to beat (Ilse/Tomczak/Welling arXiv:1802.04712; TimeMIL arXiv:2405.03140 for multivariate time series).

Averaging destroys extrema and destroys locality. If that single account is right, ONE operator change moves BOTH tasks, which no prior arm has tested because prior arms moved the WINDOW rather than the READ.

The design also resolves an ambiguity E16 could not: E16 found longer windows helped state, which is evidence AGAINST locality — but shortening the window also DISCARDS the rest of the day, so 'shorter window' and 'read locally' were confounded. Here the full day is always encoded and only the read changes, so the two explanations separate cleanly. Predicting the local arm to win is therefore a genuine risk against my own most recent measurement, and the E16 result gives a concrete reason it might lose.

## Plan
scratch/e17_unified_readout.py — pure CPU, no GPU, no model weights, operates on raw canonical patches (the information BOUND on any encoder consuming them).

WINDOW HELD FIXED AT 1440 MINUTES IN EVERY ARM. The only variables are (i) the pooling operator and (ii) the token count. This is what makes it orthogonal to both standing window rule-outs.

GRIDS (token count over the 1440-min day): 23 tokens (incumbent count) and 144 tokens (LSM-2 geometry, 10-min patch).
OPERATORS: avg (incumbent) | max | avgmax concat | lse (smooth max, temp 1 bpm).

TASK A — daily excursion (peak minus mean), cross-subject, GroupKFold(5) by subject, RidgeCV, clean pool (HR coverage>0.9, mean>20 bpm, peak>=40), lifesnaps+pmdata, n<=4000. Scored ONLY as increment over R2(excursion | true daily mean); raw R2 is inadmissible per the diagnostic contract.

TASK B — lifesnaps TENSE/ANXIOUS at the ping. The decisive contrast is GLOBAL vs LOCAL readout of the SAME tokens: global pools every token of the day (what the pipeline does today); local reads only tokens overlapping [ping-120min, ping) and summarises them into a fixed-length descriptor. Both arms get the clock features, and the clock-only arm is reported alongside. RF(300, min_samples_leaf=5), GroupKFold(5) by subject. The within-subject constant predictor is computed on the identical pool and reported as the bar.

WHY THIS IS NOT A RETRY OF ANY RULE-OUT: `ro-window-shortening-20260805` and `shortening-the-analysis-window-to-align-with-0f6d56` both eliminated SHORTENING THE WINDOW. Here the window never changes; what changes is aggregation inside it. The LOCAL arm is not a short window — it is a time-indexed READ of a full-day representation, which is the TimePerceiver (arXiv:2512.22550) learnable-target-timestamp-query pattern in its simplest form.

Outputs: logs/e17_unified_readout_a.json, logs/e17_unified_readout_b.json.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `gA_operator_beats_avg` (blocking): TASK A PRIMARY. eiv_peak_excursion_increment_r2 = R2(excursion | pooled day tokens) - R2(excursion | true daily mean, 1 feature), subject-grouped OOF, on the same clean pool. The best non-avg operator (max / avgmax / lse) must exceed the avg operator at the SAME token count by >= +0.05 increment. MDE: prior avg-vs-avgmax gap on the excursion target was 0.1685 -> 0.2968 (+0.1283) at 23 patches, so this design is powered well below +0.05. A gap under +0.05 is a powered null and kills the pooling-operator story for excursion.
Gate `gA_tokens_beat_23` (advisory): TASK A SECONDARY (token count, the W3 question folded in). At the BEST operator, 144 tokens must beat 23 tokens on increment by >= +0.03. Informational: if operator matters and token count does not, W3's LSM-2-geometry motivation is answered cheaply and the GPU stage of rb-w3-patch-grid-token-count-20260815 should be dropped or rescoped to operator only.
Gate `gB_local_beats_global` (blocking): TASK B PRIMARY, the locality test. At fixed operator and token count, AUC(LOCAL readout + clock) - AUC(GLOBAL readout + clock) must be >= +0.03 for at least one arm. This is the cleanest possible test of 'the label should read its own neighbourhood', because LOCAL and GLOBAL are the SAME tokens from the SAME representation differing only in which are read. MDE 0.028 inherited from the E16 within-subject permutation null std 0.0271.
Gate `gB_beats_within_subject_constant` (blocking): TASK B TRIVIAL BAR, inherited from E16 and non-negotiable. The best TASK B arm's absolute AUC must exceed the within-subject constant predictor (measured 0.7381 on this exact target/pool). If no arm clears it, cross-subject state detection is confirmed DEAD at the representation level regardless of readout, and the recommendation is to drop the state slot from the emitted schema rather than ship noise.

#pooling #epoch-resolution #cross-modal #control #wearables #baseline #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
