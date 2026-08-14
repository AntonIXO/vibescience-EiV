---
created_at: '2026-08-05T22:28:26+00:00'
gates:
- blocking: true
  description: 'MODE-COLLAPSE SIGNATURE. run_12ch_pipeline.sh documents Stage C as
    bistable with a mode-collapse basin producing ''constant ~54-67 bpm output regardless
    of input'' at ~50% rate. Test whether the adapter under study is such a draw:
    compute std(emitted)/std(true), the fraction of emissions inside the documented
    54-67 bpm band, and -- the sharp test -- whether swapping in a DONOR prefix changes
    the emitted value. Under mode collapse the model ignores the prefix, so real and
    donor emissions must be near-identical. Ratio near 0 plus donor-identical emissions
    confirms collapse; healthy dispersion plus a large donor gap refutes it.'
  id: gate-mode-collapse-signature
- blocking: true
  description: PRIOR-SAMPLING ARITHMETIC. If the model emits an independent draw from
    the correct marginal p(hr_peak), its expected MAE equals the GINI MEAN DIFFERENCE
    of the target distribution, which is mathematically >= the mean absolute deviation
    from the median for ANY distribution. Such a model is therefore GUARANTEED negative
    skill. Test whether the observed emission MAE matches the Gini mean difference,
    and whether shuffling the emitted values against the truth costs anything. A match
    plus near-zero shuffle cost confirms prior sampling and would make the negative
    skill pure arithmetic rather than an information limit.
  id: gate-prior-sampling-arithmetic
- blocking: true
  description: PREMISE AUDIT. The -5.88 bpm (-6.48 clean sparse) figure that motivated
    this entire E24-E33 series must be re-derived on the STRICT-CLEAN population with
    the baseline median recomputed WITHIN that population. Importing a median across
    populations is itself a sign-flipping error. Report n and a bootstrap CI at every
    filtering step; a point estimate on a small subset is not a result. If the sign
    flips on decontamination the premise is an artifact and must be retracted.
  id: gate-premise-decontamination
- blocking: true
  description: 'PAIRING AUDIT. Cached prediction arrays are stored over the TEST SPLIT,
    so the only valid truth vector is y[te], not y[:n]. Any correlation or per-sample
    statistic computed under the wrong pairing is invalid and must be recomputed and
    explicitly retracted rather than silently corrected. Equally, confirm what each
    cached array actually IS before comparing it to anything: a fitted-probe decode
    and a native JSON emission are different quantities and comparing them as if they
    were the same manufactures false contradictions.'
  id: gate-pairing-and-provenance-audit
id: the-hr-peak-emission-failure-is-a-training-stability-artifact-the-adapter-under-
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_digit_readout_decoded_mae
  direction: none
  magnitude_note: 'PRIMARY PREDICTION: the adapter under study IS a mode-collapse
    draw -- std(emitted)/std(true) near zero, emissions clustered in the documented
    54-67 bpm band, and real-vs-donor emissions nearly identical because the model
    ignores the prefix. If that holds, the emission failure is a TRAINING-STABILITY
    artifact of one unlucky seed rather than a fundamental limit, the correct fix
    is N_SEEDS_C>1 with the pipeline''s existing tripwires, and nine experiments of
    architecture reasoning were aimed at the wrong target. Direction ''none'' because
    no readout is refit and no system change is made; these are dispersion and provenance
    statistics on cached measurements. FALSIFIER: healthy dispersion (ratio near 1),
    zero emissions in the collapse band, and a large donor gap would refute mode collapse
    and leave the gradient-attribution line standing.'
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
The hr_peak emission failure is a TRAINING-STABILITY artifact -- the adapter under study is a draw from the mode-collapse basin that run_12ch_pipeline.sh documents for Stage C ('constant ~54-67 bpm output regardless of input', ~50% of runs) -- rather than evidence of a fundamental limit of soft-prompt conditioning.

IF CONFIRMED: the probe results of E24-E32 remain true (the signal IS present and decodable at 7.605 bpm, +10.835 over the confound floor), but the emission failure is one unlucky seed. The correct fix is the pipeline's existing N_SEEDS_C>1 multi-seed selection with LM-level tripwires, NOT an architecture change, and the gradient-attribution programme is misdirected.

IF REFUTED: the emission is not collapsed, the failure is genuine conditioning weakness, and rb-e33 gradient attribution stands as the next step.

SECONDARY CLAIM, audited in the same pass: the -5.88 / -6.48 bpm premise on which this entire series rests must survive re-derivation on the strict-clean population with the baseline median recomputed within that population. If it does not, the premise is a scoring artifact and must be retracted rather than inherited.

SCOPE: this reads cached predictions and truth from outputs/probes/e26_digit_ceiling_features.npz and the per-sample sidecar. It is a dispersion and provenance audit, not a new measurement of the model, and it cannot establish what a retrained model would do.

## Rationale
While preparing the E33 gradient-attribution run I read run_12ch_pipeline.sh and found this in its own header comments:

  'Stage C is empirically bistable: each training run lands either in the real-conditioning basin or the mode-collapse basin (constant ~54-67 bpm output regardless of input). At the tuned hr_regression_weight=0.002 the success rate is ~50%. Running N independent seeds and selecting the best gives 1 - 0.5^N chance of at least one good model.'

'Constant output regardless of input' IS the hr_peak failure signature. This series has spent nine experiments explaining a negative emitted skill as a property of the architecture, the representation geometry, or the target encoding -- while the project's own pipeline documents that HALF of all Stage C runs land in a basin that produces exactly this behaviour, and ships an N_SEEDS_C mechanism plus tripwires to handle it.

If the adapter under study is a collapsed draw, the probe results all stay TRUE -- the signal really is present and decodable at 7.605 bpm, a genuine +10.835 over the confound floor -- but the EMISSION failure would be a training-stability artifact of one unlucky seed, and the correct action is to retrain with multiple seeds rather than to change the architecture.

This costs nothing to check. outputs/probes/e26_digit_ceiling_features.npz already caches predictions and truth for the exact strict-clean population and split used throughout E26-E32. The question was simply never asked of data already in hand.

The same pass re-audits the -5.88 bpm premise itself. That figure came from a per-sample sidecar whose population was never checked against the strict-clean definition used everywhere else in the series, and the standing rule that a baseline must be recomputed within its own population was never applied to it.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_digit_readout_decoded_mae]] (none).
Gate `gate-mode-collapse-signature` (blocking): MODE-COLLAPSE SIGNATURE. run_12ch_pipeline.sh documents Stage C as bistable with a mode-collapse basin producing 'constant ~54-67 bpm output regardless of input' at ~50% rate. Test whether the adapter under study is such a draw: compute std(emitted)/std(true), the fraction of emissions inside the documented 54-67 bpm band, and -- the sharp test -- whether swapping in a DONOR prefix changes the emitted value. Under mode collapse the model ignores the prefix, so real and donor emissions must be near-identical. Ratio near 0 plus donor-identical emissions confirms collapse; healthy dispersion plus a large donor gap refutes it.
Gate `gate-prior-sampling-arithmetic` (blocking): PRIOR-SAMPLING ARITHMETIC. If the model emits an independent draw from the correct marginal p(hr_peak), its expected MAE equals the GINI MEAN DIFFERENCE of the target distribution, which is mathematically >= the mean absolute deviation from the median for ANY distribution. Such a model is therefore GUARANTEED negative skill. Test whether the observed emission MAE matches the Gini mean difference, and whether shuffling the emitted values against the truth costs anything. A match plus near-zero shuffle cost confirms prior sampling and would make the negative skill pure arithmetic rather than an information limit.
Gate `gate-premise-decontamination` (blocking): PREMISE AUDIT. The -5.88 bpm (-6.48 clean sparse) figure that motivated this entire E24-E33 series must be re-derived on the STRICT-CLEAN population with the baseline median recomputed WITHIN that population. Importing a median across populations is itself a sign-flipping error. Report n and a bootstrap CI at every filtering step; a point estimate on a small subset is not a result. If the sign flips on decontamination the premise is an artifact and must be retracted.
Gate `gate-pairing-and-provenance-audit` (blocking): PAIRING AUDIT. Cached prediction arrays are stored over the TEST SPLIT, so the only valid truth vector is y[te], not y[:n]. Any correlation or per-sample statistic computed under the wrong pairing is invalid and must be recomputed and explicitly retracted rather than silently corrected. Equally, confirm what each cached array actually IS before comparing it to anything: a fitted-probe decode and a native JSON emission are different quantities and comparing them as if they were the same manufactures false contradictions.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
