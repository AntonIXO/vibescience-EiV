---
artifacts: []
closed: true
config_note: 'Runner: scratch/e22_encoder_affect.py -> logs/e22_encoder_affect.json.
  GPU RTX 5070, minutes. Corpus git_sha e849eb7, seed 0.


  Encoder: outputs/checkpoints/v5d/phase_b/sensor_modules/temporal_encoder.pt, config
  {input_dim 24, d_model 192, patch_len 120, stride 60, hidden_dim 64} -> [B, 24,
  23, 192]. Strict weight load enforced. 1947 unique day-windows encoded once, then
  indexed per ping so every arm reads the SAME representation.


  Six arms on identical rows and identical GroupKFold(5)-by-subject splits: clock_only,
  shallow_plus_clock (the exact ~17 summary scalars from E16/E18), enc_global_{avg,avgmax},
  enc_local_{avg,avgmax} where local = tokens overlapping [ping-120, ping). RF(300,
  min_samples_leaf=5).'
cost: null
crash_reason: ''
created_at: '2026-08-15T07:42:52+00:00'
diagnostics_measured:
- after: 0.598
  before: 0.5676
  diagnostic_id: eiv_affect_multichannel_increment
do_not_retry: []
external_run: logs/e22_encoder_affect.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1_encoder_beats_shallow
  passed: false
- evidence: ''
  gate_id: g2_encoder_beats_clock
  passed: false
- evidence: ''
  gate_id: g3_local_vs_global_at_encoder
  passed: true
git_ref: ''
hypothesis_id: hyp-e22-encoder-representation-affect-20260815
id: exp-e22-encoder-affect-20260815
interpretation:
  at: '2026-08-18T21:08:40+00:00'
  failure_mode: claim_false
  lesson: 'Representation power is not the binding constraint on in-the-wild affect:
    the frozen Phase-B encoder adds at most +0.0303 over ~17 hand-crafted summary
    scalars on TENSE/ANXIOUS (0.5980 vs 0.5676) and +0.0011 on TIRED at n=4208 over
    61 subjects, both under the preregistered +0.05 bar, and both GLOBAL encoder arms
    are actively worse than shallow (0.5285 / 0.5383 vs 0.5676). Do not repeat the
    ''swap in a deeper representation'' lever on lifesnaps self-report — exp-e24 landed
    the same hour showing the SAME shallow features reach AUC 0.8497 on WESAD and
    0.6806 on neuro_stress, so the discriminating variable is the LABEL, not the encoder
    or the window. The one clean positive, which contradicts the headline null and
    should be carried forward: reading only the encoder tokens that COVER the ping
    beats pooling the whole day in 4/4 arms (+0.0656/+0.0597 tense, +0.0540/+0.0646
    tired, all above E17''s 0.028 MDE), so E17''s ''locality is a coin flip'' conclusion
    is scoped to RAW patches and must not be cited for learned representations — time-indexed/local
    queries belong in the projector even though they do not rescue affect.'
  transfers_to:
  - hyp-e17-unified-readout-operator-20260815
  - hyp-e24-lab-protocol-subdaily-state-20260815
  - hyp-e25-triplet-degeneracy-fixed-20260815
  - hyp-e27-encoder-on-transitions-20260815
key_numbers:
  clock_only_tense: 0.5507
  enc_global_avg_tense: 0.5285
  enc_global_avgmax_tense: 0.5383
  enc_local_avg_tense: 0.5941
  enc_local_avgmax_tense: 0.598
  gap_encoder_minus_shallow_tense: 0.0303
  gap_encoder_minus_shallow_tired: 0.0011
  gap_local_minus_global_tense_avg: 0.0656
  gap_local_minus_global_tense_avgmax: 0.0597
  gap_local_minus_global_tired_avg: 0.054
  gap_local_minus_global_tired_avgmax: 0.0646
  n: 4208.0
  n_subj: 61.0
  shallow_plus_clock_tense: 0.5676
observed_effects:
- delta: 0.0304
  diagnostic_id: eiv_affect_multichannel_increment
  direction: up
parent_experiment_id: exp-e19-encoder-pooling-20260815
prediction_match:
  overall: true
  per_diagnostic:
    eiv_affect_multichannel_increment: true
provenance: {}
recipe_fingerprint: {}
verdict: 'null'
---

## Summary
Tests hypothesis [[hyp-e22-encoder-representation-affect-20260815]].
Branches from [[exp-e19-encoder-pooling-20260815]].
External run: logs/e22_encoder_affect.json

## Measurements
- [[eiv_affect_multichannel_increment]]: before 0.5676 → after 0.598  (Δ +0.0304, up ✓ predicted up)

## Gates
- `g1_encoder_beats_shallow`: **FAIL**
- `g2_encoder_beats_clock`: **FAIL**
- `g3_local_vs_global_at_encoder`: PASS

**Verdict:** null

> POWERED NULL: the observed effect is smaller than the minimum detectable effect this run preregistered. The direction is noise, not evidence — read this as a refutation of the lever at the committed scale.

## Interpretation
**Failure mode:** `claim_false`

Representation power is not the binding constraint on in-the-wild affect: the frozen Phase-B encoder adds at most +0.0303 over ~17 hand-crafted summary scalars on TENSE/ANXIOUS (0.5980 vs 0.5676) and +0.0011 on TIRED at n=4208 over 61 subjects, both under the preregistered +0.05 bar, and both GLOBAL encoder arms are actively worse than shallow (0.5285 / 0.5383 vs 0.5676). Do not repeat the 'swap in a deeper representation' lever on lifesnaps self-report — exp-e24 landed the same hour showing the SAME shallow features reach AUC 0.8497 on WESAD and 0.6806 on neuro_stress, so the discriminating variable is the LABEL, not the encoder or the window. The one clean positive, which contradicts the headline null and should be carried forward: reading only the encoder tokens that COVER the ping beats pooling the whole day in 4/4 arms (+0.0656/+0.0597 tense, +0.0540/+0.0646 tired, all above E17's 0.028 MDE), so E17's 'locality is a coin flip' conclusion is scoped to RAW patches and must not be cited for learned representations — time-indexed/local queries belong in the projector even though they do not rescue affect.

Transfers to: [[hyp-e17-unified-readout-operator-20260815]], [[hyp-e24-lab-protocol-subdaily-state-20260815]], [[hyp-e25-triplet-degeneracy-fixed-20260815]], [[hyp-e27-encoder-on-transitions-20260815]]

## Key numbers
- `clock_only_tense`: 0.5507
- `enc_global_avg_tense`: 0.5285
- `enc_global_avgmax_tense`: 0.5383
- `enc_local_avg_tense`: 0.5941
- `enc_local_avgmax_tense`: 0.598
- `gap_encoder_minus_shallow_tense`: 0.0303
- `gap_encoder_minus_shallow_tired`: 0.0011
- `gap_local_minus_global_tense_avg`: 0.0656
- `gap_local_minus_global_tense_avgmax`: 0.0597
- `gap_local_minus_global_tired_avg`: 0.054
- `gap_local_minus_global_tired_avgmax`: 0.0646
- `n`: 4208
- `n_subj`: 61
- `shallow_plus_clock_tense`: 0.5676

## Notes
MY PREDICTION WAS WRONG AND THE NULL IS REAL — on lifesnaps. The encoder representation adds at most +0.0303 over hand-crafted summary scalars on TENSE/ANXIOUS and +0.0011 on TIRED, both under the +0.05 bar. Both GLOBAL encoder arms are actively WORSE than shallow.

I predicted a 6-30x shallow-vs-deep gap by analogy to h4b (+0.0706 -> +0.4305) and E19 (+0.0090 -> +0.2807). That analogy DOES NOT TRANSFER from excursion to affect. Excursion is a deterministic function of the sequence that averaging destroys and the encoder demonstrably preserves; affect is a latent state whose relation to HR is weak in this cohort regardless of representation. So the correct reading of E16/E18 is now stronger than 'shallow found no cheap win' — the production function class does not find much either, ON LIFESNAPS.

THE ONE CLEAN POSITIVE, AND IT CORRECTS E17. Reading the tokens that COVER the ping beats pooling the whole day, consistently: +0.0656 / +0.0597 on TENSE and +0.0540 / +0.0646 on TIRED — four of four arms, same sign, all above E17's 0.028 MDE. E17 measured this on RAW patch statistics and got a coin flip (four negative, four positive). On TRAINED encoder tokens locality is real and roughly twice as large. E17's g3 conclusion ('locality is a coin flip, not a lever') is therefore scoped to raw patches and should NOT be cited for learned representations. Architecturally this is the TimePerceiver/time-indexed-query direction: it is worth having in the projector even though it does not by itself rescue affect.

READ THIS TOGETHER WITH exp-e24, WHICH LANDED THE SAME HOUR. E24 found WESAD at AUC 0.8497 (clock 0.5640) and neuro_stress at 0.6806 (clock 0.4930), subject-grouped, using the SAME shallow features that fail here. So the discriminating variable is NOT the representation and NOT the window — it is the LABEL. Dense induced-stress protocol labels carry a large effect that percentile features already capture; sparse in-the-wild self-report does not, and no amount of representation power recovers what is not there.

SCOPE, STATED PRECISELY. This tests the FROZEN Phase-B encoder trained under InfoNCE against mean-only captions. It does NOT test Phase B triplets retrained on the new per-epoch momentary labels — problem phase-b-triplet-orthogonalization-is-f6d3c5 records those triplets as near-vacuous (99.1% single-state samples, every non-NONE label a pure dataset identifier), a degeneracy that commit a7fc419 may have fixed. That remains the untested arm, and E24 now gives it a cohort where the labels demonstrably carry signal.

#wearables #cross-modal #baseline #control #pooling #epoch-resolution #optihealth-eiv #matched-baseline #subject-heldout #data-contract-violation
