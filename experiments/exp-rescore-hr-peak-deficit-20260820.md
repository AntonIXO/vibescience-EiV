---
artifacts: []
closed: true
config_note: 'NO NEW GPU -- pooled re-score of EXISTING emission captures, per the
  vault rule that emission captures are multi-question instruments. Script scratch/rescore_hr_peak_deficit.py
  with readings R1/R2/R3 committed PRE-DATA. Artifacts: outputs/probes/v9band04_s4{2,3,4}_emissions_n128.json
  (3 seeds x 128 = n=384) and the v9band family (n=328).


  SUBSTRATE CHANGE DECLARED UP FRONT AND IT MATTERS FOR HOW THIS MAY BE CITED: the
  hypothesis names the `e33` adapter, which predates two corpus rebuilds and no longer
  exists. This scores the hypothesis''s CLAIM (''the deficit does not exist at the
  claimed magnitude, |skill|<2 bpm with CI95 crossing 0, at n>=300'') on TODAY''s
  baseline recipe (v9band04, the family whose encoder comes from the old-corpus v5c
  phase_b and which is the only family currently scoring near the trivial baseline
  on deployment). It is therefore a WEAKER test of the original sentence about e33
  and a STRONGER test of the live question. Strict-clean filter applied (true_peak>=40,
  true_mean>40); 0 rows dropped as impossible on this family, unlike the original
  n=96 which contained 8 true peaks of 0.0 bpm. Strongest trivial baseline recomputed
  WITHIN the pooled population as max(always-median, emitted-hr_raw + median excursion),
  per the diagnostic''s mandate. Paired bootstrap 10,000 resamples for the CI.


  CONTROL WAIVER STATED IN ADVANCE: eiv_gen_peak_skill_bpm declares requires_control=eiv_gen_peak_donor_gap_bpm.
  No donor-prefix capture exists for this family. The control exists to license POSITIVE
  conditioning claims; this run makes a NEGATIVE claim (the deficit is real), so no
  positive claim rides on the missing control.'
cost: null
crash_reason: ''
created_at: '2026-08-20T21:47:41+00:00'
diagnostics_measured:
- after: -2.921
  before: -5.88
  diagnostic_id: eiv_gen_peak_skill_bpm
- after: 0.2571
  before: 0.276
  diagnostic_id: eiv_gen_peak_corr
do_not_retry: []
external_run: logs/rescore_hr_peak_deficit.json
former_ids: []
gate_results: []
git_ref: ''
hypothesis_id: the-hr-peak-native-emission-deficit-264f4f
id: exp-rescore-hr-peak-deficit-20260820
interpretation:
  at: '2026-08-20T21:48:26+00:00'
  failure_mode: claim_false
  lesson: 'The hr_peak emission deficit is REAL AND POWERED, at roughly HALF the magnitude
    the problem was opened on. Pooled v9band04 across 3 seeds, n=384 strict-clean:
    skill -2.921 bpm, bootstrap CI95 [-4.538, -1.299], excluding zero; replicated
    on v9band (n=328, -4.044, CI [-5.830, -2.210]). The hypothesis demanded |skill|<2
    with a CI crossing zero at n>=300 and got the opposite on both counts.


    THE SPLIT THAT MATTERS, and it is why this refutation is not a simple loss: the
    hypothesis''s METHODOLOGICAL diagnosis was correct while its SUBSTANTIVE claim
    was false. It argued the -5.88 headline was an unpowered artifact of n=96. Measured:
    the number really did shrink to -2.92 once the population is clean and large,
    AND the per-seed CIs prove n=128 cannot resolve the sign (s42 CI [-5.924, +0.190]
    crosses zero; s43 and s44 do not). So ''the estimate was unpowered'' and ''the
    deficit is real'' are BOTH true. A future session must not cite this refutation
    as licence to quote -5.88 again -- that figure is retired; -2.92 (CI excluding
    zero) is the live number.


    REUSABLE RULE EARNED: when a hypothesis claims an effect is an artifact of low
    power, the test is not ''does the effect survive'' but ''does it survive AT ADEQUATE
    N'' -- and the honest report has to separate the magnitude correction from the
    sign verdict. Pooling seeds was what converted three ambiguous single-seed estimates
    into one unambiguous one, at zero GPU cost, on captures that already existed.


    SECOND REUSABLE POINT: per-seed spread here (-2.897 / -2.725 / -3.161, range 0.436)
    is 5.6x TIGHTER than the recorded seed band for this diagnostic (2.4238 bpm).
    That band was measured on the pre-band04 family. The band04 recipe evidently stabilised
    this axis substantially without fixing the deficit -- worth re-measuring the band
    on the current family before any future arm prices its MDE against the stale 2.42
    figure.


    WHAT STAYS OPEN: hr_peak remains the slot where the pipeline emits worse than
    a constant while an upstream probe reads it at R2 0.81. lm_head-LoRA is already
    refuted as the readout lever. The parent problem''s framing survives at reduced
    magnitude, and the queued rb-e2 (schema reorder) / rb-e3 (bucket token loss weight)
    runbooks are untouched by this.'
  transfers_to: []
key_numbers: {}
observed_effects:
- delta: 2.959
  diagnostic_id: eiv_gen_peak_skill_bpm
  direction: up
- delta: -0.0189
  diagnostic_id: eiv_gen_peak_corr
  direction: down
parent_experiment_id: null
prediction_match:
  overall: false
  per_diagnostic:
    eiv_gen_peak_corr: false
    eiv_gen_peak_donor_gap_bpm: false
    eiv_gen_peak_skill_bpm: false
    eiv_gen_pred_bucket_entropy: false
provenance: {}
recipe_fingerprint: {}
verdict: refutes
---

## Summary
Tests hypothesis [[the-hr-peak-native-emission-deficit-264f4f]].
External run: logs/rescore_hr_peak_deficit.json

## Measurements
- [[eiv_gen_peak_skill_bpm]]: before -5.88 → after -2.921  (Δ +2.959, up ✗ predicted none)
- [[eiv_gen_peak_corr]]: before 0.276 → after 0.2571  (Δ -0.0189, down ✗ predicted up)

**Verdict:** refutes

## Interpretation
**Failure mode:** `claim_false`

The hr_peak emission deficit is REAL AND POWERED, at roughly HALF the magnitude the problem was opened on. Pooled v9band04 across 3 seeds, n=384 strict-clean: skill -2.921 bpm, bootstrap CI95 [-4.538, -1.299], excluding zero; replicated on v9band (n=328, -4.044, CI [-5.830, -2.210]). The hypothesis demanded |skill|<2 with a CI crossing zero at n>=300 and got the opposite on both counts.

THE SPLIT THAT MATTERS, and it is why this refutation is not a simple loss: the hypothesis's METHODOLOGICAL diagnosis was correct while its SUBSTANTIVE claim was false. It argued the -5.88 headline was an unpowered artifact of n=96. Measured: the number really did shrink to -2.92 once the population is clean and large, AND the per-seed CIs prove n=128 cannot resolve the sign (s42 CI [-5.924, +0.190] crosses zero; s43 and s44 do not). So 'the estimate was unpowered' and 'the deficit is real' are BOTH true. A future session must not cite this refutation as licence to quote -5.88 again -- that figure is retired; -2.92 (CI excluding zero) is the live number.

REUSABLE RULE EARNED: when a hypothesis claims an effect is an artifact of low power, the test is not 'does the effect survive' but 'does it survive AT ADEQUATE N' -- and the honest report has to separate the magnitude correction from the sign verdict. Pooling seeds was what converted three ambiguous single-seed estimates into one unambiguous one, at zero GPU cost, on captures that already existed.

SECOND REUSABLE POINT: per-seed spread here (-2.897 / -2.725 / -3.161, range 0.436) is 5.6x TIGHTER than the recorded seed band for this diagnostic (2.4238 bpm). That band was measured on the pre-band04 family. The band04 recipe evidently stabilised this axis substantially without fixing the deficit -- worth re-measuring the band on the current family before any future arm prices its MDE against the stale 2.42 figure.

WHAT STAYS OPEN: hr_peak remains the slot where the pipeline emits worse than a constant while an upstream probe reads it at R2 0.81. lm_head-LoRA is already refuted as the readout lever. The parent problem's framing survives at reduced magnitude, and the queued rb-e2 (schema reorder) / rb-e3 (bucket token loss weight) runbooks are untouched by this.

## Notes
THE CLAIM IS REFUTED, BUT HALF ITS DIAGNOSIS WAS RIGHT — read both halves or this record misleads.

PREREGISTERED READING R2 FIRED. Pooled v9band04 (3 seeds, n=384, strict-clean, 0 rows dropped as impossible):
  MAE model 19.867 | always-median 16.946 | hr_raw-derived 17.652
  strongest trivial = always-median
  SKILL -2.921 bpm, bootstrap CI95 [-4.538, -1.299], DOES NOT cross zero
  corr(emit, true) +0.2571, distinct emissions 51/384
The hypothesis demanded |skill| < 2.0 bpm with a CI95 crossing zero at n>=300. It got n=384, |skill| 2.92, and a CI entirely below zero. The deficit is REAL and POWERED on the current recipe. Replicated on the sibling v9band family: n=328, skill -4.044, CI95 [-5.830, -2.210], also excluding zero.

WHAT THE HYPOTHESIS GOT RIGHT, and it is not trivial: the ORIGINAL -5.88 bpm figure was indeed inflated. Measured today it is -2.92, i.e. the catastrophe was roughly HALF what the problem was opened on, and the per-seed CIs show exactly the instability the hypothesis diagnosed — s42 CI [-5.924, +0.190] CROSSES ZERO, s43 [-5.351, -0.133] and s44 [-5.933, -0.385] do not. So at n=128 a single seed genuinely cannot resolve this, which was the hypothesis's methodological point and it stands. Pooling three seeds is what makes the sign unambiguous. The correct summary is: the estimate was unpowered AND the deficit is real; those are not in conflict.

SEED-BAND CHECK, because the vault's own rule demands it: eiv_gen_peak_skill_bpm carries a measured seed band of 2.4238 bpm (sigma 1.2119, k=2). The pooled point estimate -2.921 exceeds that band, and the pooled CI width (3.24 bpm) is comparable to it — so this is a real effect by the vault's own MDE rule, not draw luck. Per-seed values -2.897 / -2.725 / -3.161 are tightly clustered (range 0.436), far tighter than the historical 2.42 band, which is itself evidence the band04 recipe stabilised this axis even though it did not fix the deficit.

CONTROL WAIVER, answering the recorded warning rather than leaving it silent: eiv_gen_peak_skill_bpm declares requires_control eiv_gen_peak_donor_gap_bpm, which was not measured because no donor-prefix capture exists for this family. The control's role is to license POSITIVE conditioning claims. This run makes a NEGATIVE claim (the model is worse than a constant), and a donor control cannot rescue a negative — if anything the missing control means the true conditioning could only be WEAKER than measured. No positive claim rides on it.

SUBSTRATE CAVEAT, stated so this cannot be over-cited: the hypothesis names the e33 adapter, which no longer exists. This scores its CLAIM on today's baseline family, not the original artifact. Anyone wanting to know what e33 specifically did must re-run e33, which is not possible on this corpus.

CONSEQUENCE: the parent problem's premise (hr_peak conditioning is ~12x too weak to beat the template prior) SURVIVES at reduced magnitude. hr_peak remains the one slot where the pipeline emits worse than a constant while a probe reads it at R2 0.81 upstream. The readout-side levers (lm_head LoRA) are already refuted; the live candidates remain a numeric head or ordinal binning, and the two queued runbooks rb-e2-schema-reorder and rb-e3-bucket-token-loss-weight are unaffected by this result.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
