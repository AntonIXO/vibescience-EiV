---
created_at: '2026-08-15T07:35:58+00:00'
former_ids: []
gates:
- blocking: true
  description: PRIMARY. On lifesnaps TENSE/ANXIOUS, best encoder-token arm (enc_global
    or enc_local, avg or avgmax, each + clock) MINUS the shallow+clock arm must be
    >= +0.05 AUC on identical rows and identical subject-grouped split. MDE 0.05.
    If the gap is under this, the encoder representation genuinely does not see affect
    that summary scalars miss, and E16/E18's conclusion extends to the production
    function class rather than being an artifact of my features.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g1_encoder_beats_shallow
  mde: 0.05
- blocking: true
  description: CLOCK CONTROL, non-negotiable and model-class independent. The best
    encoder arm must also exceed the clock-only arm by >= +0.03. Hour-of-day is free
    to any architecture; an encoder arm that beats shallow features but not the clock
    has learned circadian bookkeeping, not state.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g2_encoder_beats_clock
  mde: 0.03
- blocking: false
  description: LOCALITY, retested at the representation level. enc_local minus enc_global
    at the same operator. E17 found this sign-unstable on raw patch tokens; this asks
    the same question of the trained encoder. Informational.
  diagnostic_id: eiv_affect_multichannel_increment
  id: g3_local_vs_global_at_encoder
  mde: null
id: hyp-e22-encoder-representation-affect-20260815
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'Best encoder-token arm exceeds the shallow-summary-scalar arm by
    >= +0.05 AUC on identical rows/split, and also exceeds clock-only by >= +0.03.
    Prior gaps between shallow and deep on structural targets in this vault: h4b 6x
    (+0.0706 -> +0.4305), exp-e19 30x (+0.0090 -> +0.2807). If affect is carried by
    temporal shape rather than by distributional summary, a gap of that character
    should appear here.'
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
- baseline
- control
- pooling
- epoch-resolution
---

## Statement
H-E22 (encoder representation vs summary features). The state nulls in E16/E17/E18 are properties of MY FEATURE CONSTRUCTION, not of the corpus. They probed ~17 hand-crafted summary scalars with RF/logistic; the production system is a PatchTST-style encoder whose learned patch map plus attention can represent temporal shape, ordering and cross-channel timing that a percentile vector destroys by construction.

PREDICTION: reading affect off the REAL trained Phase-B encoder's tokens beats the identical-row shallow-summary arm by >= +0.05 AUC, and beats clock-only by >= +0.03.

IF CONFIRMED: 'state detection is dead on this corpus' was an over-claim about my probe, the state slot stays, and the next arm is Phase B triplets retrained on the new per-epoch momentary labels.
IF REFUTED: the encoder representation genuinely carries no affect beyond summary statistics, which upgrades E16/E18 from 'shallow found nothing' to a statement about the production function class — a much stronger and now properly-scoped null.

## Rationale
Anton's challenge: 'why are you sure PatchTST + triplet could not detect better than your attempts to verify this?' My E16/E17/E18 nulls all used ~17 hand-crafted summary scalars fed to RF/logistic. That function class cannot represent temporal shape, ordering, multi-scale structure or cross-channel timing — a percentile vector destroys all of it. The production encoder is a learned linear patch map with attention across patches.

Two measurements IN THIS VAULT contradict the assumption that shallow bounds deep on structural targets: h4b's ladder (caption scalars +0.0706 vs deep stack +0.4305, and its note explicitly records the 'shallow will match the stack' worry as REFUTED) and exp-e19 from this same session (raw-patch avg +0.0090 vs trained encoder tokens +0.2807 under the identical operator). The rule-out affect-multichannel-supply-dead-20260814 asserts the opposite ('shallow-at-chance bounds deep on daily summaries') and that assertion is what this arm tests directly.

A shallow null means NO CHEAP WIN. It does not mean NO SIGNAL. This is the cheapest possible test of the difference, and it reuses the exact E19 harness that has already been validated on this encoder.

## Plan
scratch/e22_encoder_affect.py -> logs/e22_encoder_affect.json. GPU, minutes.

Load the REAL v5d Phase-B temporal_encoder (patch_len=120, stride=60 -> 23 tokens/channel, input_dim=24 = 12 channels + 12 presence flags, d_model=192). Strict weight load; refuse to proceed on any missing key.

Encode each UNIQUE day-window ONCE, then index per ping (a subject's pings often share a day, so this avoids re-encoding and keeps the representation identical across arms).

ARMS, all scored by RF(300) with GroupKFold(5) by subject, OOF AUC, on the IDENTICAL row set:
  clock_only            hour sin/cos + minute-of-day          CONFOUND FLOOR
  shallow_plus_clock    the exact ~17 summary scalars from E16/E18, + clock
  enc_global_avg        all 23 tokens/channel, avg-pooled, + clock
  enc_global_avgmax     same, avg+max concat, + clock
  enc_local_avg         only tokens overlapping [ping-120, ping), avg, + clock
  enc_local_avgmax      same, avg+max, + clock
The avg/avgmax split is included because exp-e19 showed the pooling operator moved excursion readout by +0.4042 on this very encoder — reading affect off avg-pooled tokens alone could repeat that mistake.

TARGETS: lifesnaps TENSE/ANXIOUS (the E16/E18 target, for a like-for-like comparison) and lifesnaps TIRED.

The decisive number is enc_* MINUS shallow_plus_clock, because that isolates REPRESENTATION from the feature engineering my null actually tested.

## Links
Tests problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1_encoder_beats_shallow` (blocking): PRIMARY. On lifesnaps TENSE/ANXIOUS, best encoder-token arm (enc_global or enc_local, avg or avgmax, each + clock) MINUS the shallow+clock arm must be >= +0.05 AUC on identical rows and identical subject-grouped split. MDE 0.05. If the gap is under this, the encoder representation genuinely does not see affect that summary scalars miss, and E16/E18's conclusion extends to the production function class rather than being an artifact of my features.
Gate `g2_encoder_beats_clock` (blocking): CLOCK CONTROL, non-negotiable and model-class independent. The best encoder arm must also exceed the clock-only arm by >= +0.03. Hour-of-day is free to any architecture; an encoder arm that beats shallow features but not the clock has learned circadian bookkeeping, not state.
Gate `g3_local_vs_global_at_encoder` (advisory): LOCALITY, retested at the representation level. enc_local minus enc_global at the same operator. E17 found this sign-unstable on raw patch tokens; this asks the same question of the trained encoder. Informational.

#wearables #cross-modal #baseline #control #pooling #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
