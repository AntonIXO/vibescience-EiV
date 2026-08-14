---
created_at: '2026-08-05T22:07:53+00:00'
former_ids:
- the-hr-peak-failure-is-an-objective-side-defect-in-the-rendered-target-not-a-rep
gates:
- blocking: true
  description: PRIOR-DOMINATION TEST. Measure the per-position character/token entropy
    of the RENDERED hr_peak target against the rendered hr_raw target on the identical
    strict-clean population, using the exact render path from datasets.py (round(v,1)
    inside json.dumps). If hr_peak's target is NOT materially LOWER-entropy than hr_raw's,
    prior-domination is refuted as the discriminating mechanism, because hr_raw succeeds
    under the identical objective.
  diagnostic_id: null
  id: gate-peak-entropy-below-raw
  mde: null
- blocking: true
  description: VARIABLE-WIDTH / PLACE-VALUE TEST. Compare per-slot mutual information
    between slot symbol and underlying value under (A) position-alignment, which is
    what the model sees today, and (B) place-alignment at fixed width, where every
    slot is a constant place value. hr_raw is the built-in control and MUST show a
    near-zero delta because it is already fixed-width; a non-zero control delta invalidates
    the instrument. A large POSITIVE differential gain for hr_peak means the variable-width
    render is destroying per-slot numeric information and a fixed-width target is
    a cheap data-contract fix. A gain near or below zero refutes the mechanism.
  diagnostic_id: null
  id: gate-place-alignment-differential-gain
  mde: null
- blocking: true
  description: MEAN-CONFOUND AUDIT OF MY OWN HEADLINE. hr_raw (daily mean) demonstrably
    succeeds end-to-end. If the projector's hr_peak decodability is largely mean-recovery
    in disguise, the 7.605 bpm S0 number this series is built on measures a capability
    the system already has. Compute MAE(peak | TRUE daily mean) via linear, isotonic
    and GBT and compare against 7.605. The standing rule -- never quote a bare number,
    always the increment over what the true daily mean already buys -- has never been
    applied to the decoded-MAE numbers. If mean-only matches or beats 7.605, E26-E31's
    decoded-MAE framing needs heavy qualification.
  diagnostic_id: null
  id: gate-mean-confound-increment
  mde: null
id: the-hr-peak-failure-is-an-objective-cb1111
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_digit_readout_decoded_mae
  direction: none
  magnitude_note: 'I predict hr_peak''s rendered target shows MEASURABLY LOWER per-position
    entropy and a HIGHER majority-token share than hr_raw''s, consistent with a near-deterministic
    prior that costs little to reproduce and therefore starves the prefix of gradient.
    Direction ''none'' because these are target-distribution statistics, not a change
    to the decoded-MAE diagnostic itself; no readout is refit and no model is run.
    CONCRETE FALSIFIER: if hr_peak entropy is equal to or HIGHER than hr_raw''s, prior-domination
    cannot be what distinguishes them, since hr_raw succeeds (+4.27 bpm) under the
    identical objective, architecture and prefix while hr_peak fails (-6.48 bpm).
    Secondary prediction: the mean-confound audit will show the projector''s 7.605
    genuinely beats mean-only, i.e. the increment is real -- but it is being measured
    rather than assumed because the standing increment rule was never applied to the
    decoded-MAE series.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The hr_peak failure is an OBJECTIVE-SIDE defect in the rendered target, not a representation defect -- and the discriminating mechanism is that hr_peak's target is near-deterministic at the token level, so token-level cross-entropy is dominated by a prior that costs almost nothing to reproduce and leaves negligible gradient pressure to read the sensor prefix.

Operationally, on the identical strict-clean population, using the exact render path from datasets.py:
  (a) hr_peak's rendered target shows LOWER per-position entropy and HIGHER majority-token share than hr_raw's;
  (b) any residual failure is explained by variable-width rendering scrambling place value across positions;
  (c) the projector's 7.605 bpm decodability is a genuine increment over what the TRUE daily mean already buys.

IF (a) IS REFUTED -- hr_peak entropy equals or exceeds hr_raw's -- prior-domination cannot explain the asymmetry, because hr_raw succeeds under the identical objective, architecture and prefix.

IF (b) IS REFUTED -- place-alignment does not raise per-slot mutual information for hr_peak, with hr_raw's near-zero delta confirming the instrument works -- then variable-width rendering is not the mechanism and fixed-width target proposals must stop.

IF (c) IS REFUTED -- mean-only prediction matches or beats 7.605 -- then every decoded-MAE number in E26-E31 is largely a mean-recovery measurement and the series' framing needs heavy qualification.

SCOPE: this measures the TARGET DISTRIBUTION and a value-space confound only. Pure CPU, no model, no forward pass. It establishes how much a prior-only predictor can achieve and how much entropy is available for the prefix to explain. It does NOT measure what the model actually emits and does NOT by itself prove or disprove gradient starvation.

## Rationale
E24-E31 eliminated every representation-side explanation for the hr_peak failure. The signal is present in the prefix (R2 0.8084, increment +0.7211), survives all 34 frozen layers, decodes at 7.605 bpm from the projector output against always-median 18.492, sits INSIDE the LM's native embedding subspace (exp-e31: k=256 gives 7.604 vs 7.605 unprojected, with a random-subspace control of equal rank tracking within 1 bpm), and is at a sane scale (effective norm ratio 0.581 once Gemma3's sqrt(2560) embed_scale is accounted for). Non-linear readouts LOSE to Ridge (exp-e28). The prefix-capability theorem does not bind (exp-e29).

Yet the model emits a value with -5.88 bpm skill against always-predict-median.

EVERY ONE OF THOSE NUMBERS IS A FITTED PROBE ON FROZEN FEATURES. A probe bounds what the representation PERMITS; it says nothing about what the training objective SELECTS. That distinction has been a standing caveat on the whole series and it has never been tested.

THE BUILT-IN CONTROL IS WHAT MAKES THIS TRACTABLE. hr_raw and hr_peak are both numeric slots in the SAME JSON target, trained by the SAME objective, through the SAME architecture, from the SAME prefix -- yet rb-e0's per-sample audit found hr_raw SURVIVES (R2 +0.830, skill +4.27 bpm) while hr_peak COLLAPSES (R2 +0.198, skill -6.48 bpm). This is a natural A/B inside one system, and any explanation applying equally to both slots cannot explain the asymmetry.

hr_peak is centred at 119.5 bpm sitting exactly on the 120 tachycardic boundary (datasets.py:1964, L2035, L2156-2159), so a near-deterministic prior is the obvious candidate. It is also cheap to falsify: pure CPU, no GPU, no model, seconds. This is the screening step that decides whether an expensive per-token cross-entropy decomposition is worth running.

The third gate turns the same scepticism on my own headline. hr_raw demonstrably works, so if the projector's hr_peak decodability is largely mean-recovery in disguise, the 7.605 this session produced measures a capability the system already has.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_digit_readout_decoded_mae]] (none).
Gate `gate-peak-entropy-below-raw` (blocking): PRIOR-DOMINATION TEST. Measure the per-position character/token entropy of the RENDERED hr_peak target against the rendered hr_raw target on the identical strict-clean population, using the exact render path from datasets.py (round(v,1) inside json.dumps). If hr_peak's target is NOT materially LOWER-entropy than hr_raw's, prior-domination is refuted as the discriminating mechanism, because hr_raw succeeds under the identical objective.
Gate `gate-place-alignment-differential-gain` (blocking): VARIABLE-WIDTH / PLACE-VALUE TEST. Compare per-slot mutual information between slot symbol and underlying value under (A) position-alignment, which is what the model sees today, and (B) place-alignment at fixed width, where every slot is a constant place value. hr_raw is the built-in control and MUST show a near-zero delta because it is already fixed-width; a non-zero control delta invalidates the instrument. A large POSITIVE differential gain for hr_peak means the variable-width render is destroying per-slot numeric information and a fixed-width target is a cheap data-contract fix. A gain near or below zero refutes the mechanism.
Gate `gate-mean-confound-increment` (blocking): MEAN-CONFOUND AUDIT OF MY OWN HEADLINE. hr_raw (daily mean) demonstrably succeeds end-to-end. If the projector's hr_peak decodability is largely mean-recovery in disguise, the 7.605 bpm S0 number this series is built on measures a capability the system already has. Compute MAE(peak | TRUE daily mean) via linear, isotonic and GBT and compare against 7.605. The standing rule -- never quote a bare number, always the increment over what the true daily mean already buys -- has never been applied to the decoded-MAE numbers. If mean-only matches or beats 7.605, E26-E31's decoded-MAE framing needs heavy qualification.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
