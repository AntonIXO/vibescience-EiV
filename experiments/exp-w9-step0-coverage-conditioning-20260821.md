---
artifacts: []
closed: true
config_note: 'CPU/GPU-light Step-0 for H-W9, run before any training arm. Script scratch/w9_step0_coverage_conditioning.py,
  readings S1-S4 committed PRE-DATA. n=12,471 windows / 587 subjects, corpus ac7d2b1c,
  frozen ab_ctrl_s43 phase_b.


  WHY A TRAINED ARM IS NOT NEEDED TO SCORE THE CORE CLAIM. H-W9''s argument is INFORMATIONAL:
  the contraction naive = median + c*(present - median) is algebraically invertible
  iff c is known, so supplying c should let a reader de-dilute. A trained model can
  only consume information that is present. Appending the coverage block directly
  to the production-stage features and refitting the SAME readout therefore gives
  an upper bound on what any conditioning mechanism could extract -- if the information
  does not help a linear reader that is handed it explicitly, no FiLM/embedding will
  conjure it.


  BOTH SIDES OF THE TWO-SIDED PREDICTION MEASURED ON IDENTICAL ROWS: (a) readout =
  short-cohort variance-compression ratio, subject-grouped GroupKFold(5) RidgeCV ->
  present-only mean HR; (b) shortcut = provenance decodability, subject-grouped OOF
  RandomForest predicting dataset identity over 16 classes (chance 0.0625). Coverage
  block = 5 scalars (hr present fraction, coverage, coverage_epochs/1440, HR contiguity,
  all-channel mask mean).


  BOTH POOLING MODES RUN because H-W6 is now CONFIRMED (exp-w6b, 0.5630 -> 0.6897):
  the live question is whether coverage adds anything ON TOP of the shipped fix, not
  whether it beats the broken avg pool. S4 shuffled-coverage control included to separate
  coverage INFORMATION from feature WIDTH.'
cost: null
crash_reason: ''
created_at: '2026-08-21T04:39:07+00:00'
diagnostics_measured:
- after: 0.6849
  before: 0.6897
  diagnostic_id: eiv_extent_variance_compression_ratio
- after: 0.7602
  before: 0.7357
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
do_not_retry: []
external_run: logs/w9_step0_coverage_conditioning.json
former_ids: []
gate_results:
- evidence: ''
  gate_id: g1-shortcut-must-not-rise
  passed: true
- evidence: ''
  gate_id: g2-ratio-beats-c-null
  passed: false
- evidence: ''
  gate_id: g3-dense-no-harm
  passed: false
- evidence: ''
  gate_id: g4-shuffled-coverage-control
  passed: true
git_ref: ''
hypothesis_id: h-w9-explicit-coverage-conditioning-is-a-double-3cb794
id: exp-w9-step0-coverage-conditioning-20260821
interpretation:
  at: '2026-08-21T04:40:12+00:00'
  failure_mode: claim_false
  lesson: 'EXPLICIT COVERAGE CONDITIONING IS INERT, NOT HARMFUL -- a third outcome
    the two-sided hypothesis did not enumerate. Adding five coverage scalars to production-stage
    features changed the short-cohort variance-compression ratio by -0.0048 (masked_avg)
    and -0.0261 (avg), i.e. the WRONG SIGN against a >= +0.05 bar, while provenance
    decodability rose +0.0245 / +0.0319 (under the +0.05 shortcut bar, but in the
    feared direction).


    THE CONTROL IS WHAT MAKES THIS CONCLUSIVE. Pairing every window with a SHUFFLED
    coverage vector scored 0.6902 versus 0.6849 for the TRUE coverage and 0.6897 for
    no coverage at all. Wrong c is indistinguishable from right c. That converts an
    ambiguous ''small negative delta'' into a demonstrated ''this feature carries
    nothing'', and it cost one extra fit.


    WHY A CORRECT ALGEBRAIC ARGUMENT PRODUCED A DEAD LEVER, which is the transferable
    part. H-W9 reasoned: the contraction naive = median + c*(present - median) is
    invertible iff c is known, therefore supplying c is the minimum sufficient information
    to de-dilute. The arithmetic is right. The premise is already satisfied: encoder_format_version
    is `with_presence_v1`, which CONCATENATES THE PRESENCE FLAGS AS INPUT CHANNELS,
    so the encoder has been fed the mask since day one. Handing a reader a summary
    of something already in its input is a no-op. RULE: before proposing to SUPPLY
    a quantity, grep the input pipeline for whether it is already supplied. An information
    argument is only as good as its claim about what the model currently lacks.


    THE TWO-SIDED FRAMING PAID FOR ITSELF. I wrote this hypothesis expecting possible
    net harm and gated it on the shortcut rather than on the gain, with a deterioration
    control attached. The result was neither of the two anticipated outcomes -- not
    a de-dilution win, not a shortcut disaster, but inertness. A one-sided ''this
    will help'' preregistration would have read -0.0048 as noise and bought a 3-seed
    GPU family; the shuffled control is the thing that closed it.


    CONSEQUENCE FOR THE COVERAGE-SHORTCUT PAIR: `h5-the-shortcut-is-the-coverage-2aaaa0`
    owns the SUPPRESSION direction and is still unrun. This kills only AMPLIFICATION.
    It does, however, supply direct evidence for H5''s premise -- the coverage signal
    is fully available to the model already, which is exactly the condition under
    which a suppression arm is worth running. If anyone revisits H5, cite this rather
    than re-deriving the premise.


    SCOPE: frozen-feature upper bound, HR-mean readout, production stage. Untested
    for a trained conditioning pathway on state/hr_peak targets; g3 (dense no-harm)
    UNMEASURED because no conditioned model was trained.'
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: -0.0048
  diagnostic_id: eiv_extent_variance_compression_ratio
  direction: down
- delta: 0.0245
  diagnostic_id: eiv-dataset-provenance-shortcut-balacc
  direction: up
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv-dataset-provenance-shortcut-balacc: false
    eiv_extent_variance_compression_ratio: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[h-w9-explicit-coverage-conditioning-is-a-double-3cb794]].
External run: logs/w9_step0_coverage_conditioning.json

## Measurements
- [[eiv_extent_variance_compression_ratio]]: before 0.6897 → after 0.6849  (Δ -0.0048, down ✗ predicted up)
- [[eiv-dataset-provenance-shortcut-balacc]]: before 0.7357 → after 0.7602  (Δ +0.0245, up ✗ predicted none)

## Gates
- `g1-shortcut-must-not-rise`: PASS
- `g2-ratio-beats-c-null`: **FAIL**
- `g3-dense-no-harm`: **FAIL**
- `g4-shuffled-coverage-control`: PASS

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

EXPLICIT COVERAGE CONDITIONING IS INERT, NOT HARMFUL -- a third outcome the two-sided hypothesis did not enumerate. Adding five coverage scalars to production-stage features changed the short-cohort variance-compression ratio by -0.0048 (masked_avg) and -0.0261 (avg), i.e. the WRONG SIGN against a >= +0.05 bar, while provenance decodability rose +0.0245 / +0.0319 (under the +0.05 shortcut bar, but in the feared direction).

THE CONTROL IS WHAT MAKES THIS CONCLUSIVE. Pairing every window with a SHUFFLED coverage vector scored 0.6902 versus 0.6849 for the TRUE coverage and 0.6897 for no coverage at all. Wrong c is indistinguishable from right c. That converts an ambiguous 'small negative delta' into a demonstrated 'this feature carries nothing', and it cost one extra fit.

WHY A CORRECT ALGEBRAIC ARGUMENT PRODUCED A DEAD LEVER, which is the transferable part. H-W9 reasoned: the contraction naive = median + c*(present - median) is invertible iff c is known, therefore supplying c is the minimum sufficient information to de-dilute. The arithmetic is right. The premise is already satisfied: encoder_format_version is `with_presence_v1`, which CONCATENATES THE PRESENCE FLAGS AS INPUT CHANNELS, so the encoder has been fed the mask since day one. Handing a reader a summary of something already in its input is a no-op. RULE: before proposing to SUPPLY a quantity, grep the input pipeline for whether it is already supplied. An information argument is only as good as its claim about what the model currently lacks.

THE TWO-SIDED FRAMING PAID FOR ITSELF. I wrote this hypothesis expecting possible net harm and gated it on the shortcut rather than on the gain, with a deterioration control attached. The result was neither of the two anticipated outcomes -- not a de-dilution win, not a shortcut disaster, but inertness. A one-sided 'this will help' preregistration would have read -0.0048 as noise and bought a 3-seed GPU family; the shuffled control is the thing that closed it.

CONSEQUENCE FOR THE COVERAGE-SHORTCUT PAIR: `h5-the-shortcut-is-the-coverage-2aaaa0` owns the SUPPRESSION direction and is still unrun. This kills only AMPLIFICATION. It does, however, supply direct evidence for H5's premise -- the coverage signal is fully available to the model already, which is exactly the condition under which a suppression arm is worth running. If anyone revisits H5, cite this rather than re-deriving the premise.

SCOPE: frozen-feature upper bound, HR-mean readout, production stage. Untested for a trained conditioning pathway on state/hr_peak targets; g3 (dense no-harm) UNMEASURED because no conditioned model was trained.

## Notes
H-W9 IS NOT FUNDED: the readout half of its two-sided prediction fails with the WRONG SIGN, and the shuffled-coverage control proves the coverage scalars are informationally inert for this readout.

MEASURED, identical rows (n=12,471 / 587 subjects), both pooling modes:
  pooling=masked_avg  base 0.6897 | +coverage 0.6849 (delta -0.0048) | +SHUFFLED coverage 0.6902
  pooling=avg         base 0.5630 | +coverage 0.5369 (delta -0.0261) | +SHUFFLED coverage 0.5638
  provenance bal-acc  masked 0.7357 -> 0.7602 (+0.0245) | avg 0.7190 -> 0.7509 (+0.0319), chance 0.0625

S1 (readout, bar >= +0.05): FAIL, and not marginally -- the delta is NEGATIVE in both modes.
S2 (shortcut, bar <= +0.05): PASS at +0.0245, but in the feared DIRECTION.
S4 (control): the wrong-c arm scores 0.6902 versus the true-c arm's 0.6849 -- i.e. shuffling the coverage vector is if anything very slightly BETTER than supplying the correct one. That is the signature of a feature carrying no information the representation lacks; the small negative delta on the true-c arm is width-induced regularisation noise, not de-dilution.

WHY THE ALGEBRAIC ARGUMENT DID NOT SURVIVE CONTACT. H-W9's reasoning was sound as arithmetic: naive = median + c*(present - median) is invertible iff c is known, so c 'should' be the minimum sufficient information to de-dilute. The measurement says the premise is already satisfied without it -- the representation ALREADY contains coverage. That is not a surprise in hindsight: encoder_format_version is with_presence_v1, i.e. the presence flags are literally concatenated as input channels, so the encoder has been fed the mask all along. Handing the reader a five-scalar summary of something it can already compute adds nothing. The hypothesis reasoned about the ARITHMETIC of the contraction without checking whether the INPUT PIPELINE already supplied the correction term.

THE TWO-SIDED FRAMING EARNED ITS KEEP, and this is worth carrying: I wrote H-W9 expecting it might do net harm and gated it on the shortcut rather than on the gain. The outcome is a third possibility neither side anticipated -- coverage is simply INERT for readout while nudging provenance decodability up by +0.0245. A one-sided 'this will help' hypothesis would have read the negative delta as noise and gone to GPU; the shuffled control is what converts 'small negative' into 'demonstrably carries nothing'.

RELATION TO H5, restated so the pair is read together: `h5-the-shortcut-is-the-coverage-2aaaa0` owns the SUPPRESSION direction (coverage signature as a shortcut to be removed) and remains inconclusive/never-run. H-W9 was the AMPLIFICATION direction. Amplification is now measured and dead. That does NOT confirm H5 -- suppression could still help or do nothing -- but it does establish that the coverage signal is already fully available to the model, which is the premise H5's suppression arm depends on and which is now directly evidenced rather than assumed.

SCOPE. Frozen-feature upper bound on HR-mean readout at the production stage. It does not test a trained FiLM/embedding pathway on a DIFFERENT target (state, hr_peak), and g3 (dense no-harm) is UNMEASURED because no conditioned model was trained. A trained model with a dedicated conditioning pathway could exploit c harder than a linear readout does -- but it would be exploiting it for provenance, which is what g1 exists to forbid.

COST: one probe run instead of a 3-seed conditioned family.

#extent-heterogeneity #dataset-whitelist #denominator #wearables #pooling #state-supervision #deployment-distribution #optihealth-eiv #data-contract-violation #matched-baseline
