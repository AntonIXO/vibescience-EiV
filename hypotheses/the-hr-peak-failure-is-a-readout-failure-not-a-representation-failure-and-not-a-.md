---
created_at: '2026-08-05T17:54:27+00:00'
gates:
- blocking: true
  description: 'PRIMARY / DECISIVE. eiv_lm_hidden_peak_r2 at the final layer, hr_peak
    position, INCREMENT over R2(peak | true daily mean alone) must exceed +0.25 on
    the strict-clean population. This is the fork: PASS => the peak survives the forward
    pass and arrives at the output position, so the defect is in the readout and rb-e4-lora-lm-head
    is precisely targeted. FAIL => the peak is destroyed inside the LM, head-targeted
    LoRA cannot recover what never arrives, and E4 must be DEMOTED rather than run.
    Either outcome is decision-useful; there is no null result here.'
  id: gate-lm-hidden-peak-increment
- blocking: true
  description: POSITIVE CONTROL, guards against a broken probe. The SAME probe at
    the hr_raw position must recover hr_mean at R2 >= 0.60 at the final layer. hr_raw
    is the slot that demonstrably conditions at generation (+0.740 corr, +4.27 bpm
    skill vs constant). If even hr_raw is unreadable at the output position, the measurement
    apparatus is broken (wrong token index, wrong hidden state, wrong mask) and NO
    conclusion may be drawn about hr_peak. Read this gate FIRST.
  id: gate-hr-raw-positive-control
- blocking: true
  description: NEGATIVE CONTROL. A shuffled-target Ridge on the identical features
    must score |R2| < 0.05. With d_model=2560 and n~1000 the probe is in the p>n regime;
    without this control a high R2 could be memorisation.
  id: gate-shuffle-null
id: the-hr-peak-failure-is-a-readout-failure-not-a-representation-failure-and-not-a-
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_lm_hidden_peak_r2
  direction: up
  magnitude_note: 'PRIMARY. Predicting the final-layer hidden state at the hr_peak
    emission position retains the peak with an INCREMENT over the true-mean control
    of at least +0.25 (raw R2 >= 0.45). Explicitly committing to the HIGH branch of
    the fork: the prefix carries it at increment +0.72, and the same forward pass
    demonstrably delivers hr_raw well enough to generate it at corr +0.740, so I expect
    degradation but not destruction. If the increment lands below +0.25 the hypothesis
    is REFUTED and E4 must be demoted, not run.'
- diagnostic_id: eiv_gen_peak_corr
  direction: none
  magnitude_note: No generation is performed in this experiment and no training occurs,
    so the generated-peak correlation must be unchanged from the recorded baseline.
    Recorded as an explicit no-change to prevent any later re-reading of this probe
    as a generation result.
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: confirmed
supersedes: null
topic_tags:
- cross-modal
- medgemma
- sensor-text
- collapse
---

## Statement
The hr_peak failure is a READOUT failure, not a representation failure and not a forward-pass failure: the peak information survives the frozen LM's forward pass and is still linearly present in the residual stream AT THE TOKEN POSITION WHERE THE MODEL EMITS hr_peak, but the frozen output head cannot convert it into the emitted digits against the template prior.

GROUNDS. The production projector output -- the exact tensor that becomes the soft-token prefix -- carries hr_peak at Ridge R2=0.8084 with an increment of +0.7211 over knowing the true daily mean (strict-clean n=1641, shuffled-target control -0.0315, presence-zeroed control +0.7261). At the other end of the pipe, generated hr_peak has NEGATIVE skill against an always-predict-median constant (-5.88 bpm full coverage, -6.48 bpm clean sparse). The information is present at the input to the LM and absent from its output. The segment in between has never been measured.

PREDICTION (committed before running): probing the frozen LM's final-layer residual stream at the hr_peak emission position, teacher-forced, will recover the true peak with an increment over the true-mean control of AT LEAST +0.25. I am explicitly committing to the HIGH branch: I expect the peak to arrive largely intact, because the same forward pass delivers hr_raw well enough to generate it at corr +0.740.

CONSEQUENCE IF CONFIRMED: the defect is localised to the readout, rb-e4-lora-lm-head-20260805 (LoRA on the LM head / final layers) becomes the precisely-targeted arm and should be promoted to the top of the queue with a mechanistic justification rather than a 'lever we have not tried yet' one.

CONSEQUENCE IF REFUTED (increment < +0.25): the peak is destroyed INSIDE the frozen LM's forward pass. Adapting the head cannot recover information that never reaches it, so E4 should be DEMOTED rather than run, and the remaining candidates are the ones that change what enters the stack -- prefix basis alignment (E20 measured cos(prefix HR direction, LM native numeric direction) = -0.015) or zero-init gated cross-attention (arXiv:2303.16199). A refutation here would also be the first direct evidence for the architecture-inadequacy reading, which the problem record has repeatedly and correctly refused to grant on indirect evidence.

## Rationale
MOTIVATION. Every hypothesis in this problem so far has been aimed at either the encoder side or the output, and the encoder-side story has now been eliminated twice. ro-dual-avg-max-pooling-downstream-20260805 measured the production projector output (the exact tensor that becomes the prefix) at hr_peak R2=0.8084, increment +0.7211 over knowing the true mean, on a strictly clean population, with three falsification controls passing (shuffled target -0.0315, random-init 0.5929 < trained 0.7211, presence-zeroed 0.7261 so it is read from value channels). Meanwhile rb-e0 measured hr_peak generation skill as NEGATIVE against an always-predict-median constant: -5.88 bpm at full coverage, -6.48 bpm on clean sparse.

So the information is IN the prefix and NOT in the output. Nothing has ever measured the segment between them. That is the only unexamined link left, and it is cheap to look at.

WHY THIS IS NOT A RE-RUN OF ANYTHING RULED OUT. It does not touch the window (ro-window-shortening), the slot count (ro-sensor-slot-granularity, independently re-confirmed at k=1/2/4/8), the pooling operator (ro-dual-avg-max-pooling-downstream), the training marginal, prompt skew, or zero contamination. It is a NEW measurement location, not a new configuration.

WHY IT IS NOT THE SWAP-DELTA METRIC. Teacher-forced swap delta was invalidated as a cross-slot comparator: it ranked the working slot (hr_raw) BELOW the collapsed one. This probe is a direct linear readout of a hidden state, not a logprob comparison, and it is used WITHIN one slot against a mean-only control plus a within-model positive control on hr_raw. It is not proposed as an optimisation target.

LITERATURE. ICLR 2024 'When Do Prompting and Prefix-Tuning Work?' proves an additive prefix cannot change relative attention over content tokens, only add a fixed bias. E20 separately measured cos(prefix HR direction, LM native numeric direction) = -0.015, i.e. near-orthogonal encoding. Both predict attenuation through the stack. This probe measures how much actually survives, which is the number neither result supplies.

## Plan
CPU-light GPU probe, ~15 min, NO training run. Script scratch/diag_e24_lm_hidden_peak.py.

1. POPULATION: strict-clean filter established by ro-dual-avg-max-pooling-downstream-20260805 -- no zero-bpm present steps, daily mean > 40 bpm, coverage > 0.9. NOT the legacy coverage>0.9 & mean>20 filter (59.1% zero-contaminated). Target n ~ 800-1000 for probe power; subsample if larger.

2. FORWARD PASS: load adapter medgemma_12ch_e23v2 (the BASELINE, the checkpoint whose mechanism evidence survived its controls -- not arm1, whose mechanism inverted). Build the soft-token prefix exactly as scratch/diag_e23L_teacher_forced_fixed.py does (channel_validity_mask + channel_fusion + projector + positional codebook + prepare_sensor_conditioned_embeddings). TEACHER-FORCE the true target JSON string so token positions are deterministic and identical across samples in structure.

3. READ POSITION: locate the token index immediately BEFORE the first digit of the hr_peak value (i.e. the position whose output distribution produces that digit), and likewise for hr_raw. Capture output_hidden_states=True and keep the residual stream at that index for a set of layers (e.g. 0, 25%, 50%, 75%, final).

4. PROBE: RidgeCV, 70/30 split seed 0, predicting true hr_peak (and true hr_mean at the hr_raw position). Report raw R2 AND the mandatory increment over R2(peak | true mean alone, 1 feature). Report the shuffled-target control on identical features.

5. RECORD: close_experiment with all three gates; write the per-layer table into the problem record. Then update the runbook queue: promote or demote rb-e4-lora-lm-head-20260805 according to the primary gate, and say plainly which.

NOTE ON METHOD SCOPE: this measures PRESENCE at the output position, not usability -- the same caveat that applies to every probe in this problem. A PASS does not prove the head can be trained to read it; it proves the information is there to be read. That distinction is stated up front so a PASS is not over-claimed.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_lm_hidden_peak_r2]] (up).
Predicts [[eiv_gen_peak_corr]] (none).
Gate `gate-lm-hidden-peak-increment` (blocking): PRIMARY / DECISIVE. eiv_lm_hidden_peak_r2 at the final layer, hr_peak position, INCREMENT over R2(peak | true daily mean alone) must exceed +0.25 on the strict-clean population. This is the fork: PASS => the peak survives the forward pass and arrives at the output position, so the defect is in the readout and rb-e4-lora-lm-head is precisely targeted. FAIL => the peak is destroyed inside the LM, head-targeted LoRA cannot recover what never arrives, and E4 must be DEMOTED rather than run. Either outcome is decision-useful; there is no null result here.
Gate `gate-hr-raw-positive-control` (blocking): POSITIVE CONTROL, guards against a broken probe. The SAME probe at the hr_raw position must recover hr_mean at R2 >= 0.60 at the final layer. hr_raw is the slot that demonstrably conditions at generation (+0.740 corr, +4.27 bpm skill vs constant). If even hr_raw is unreadable at the output position, the measurement apparatus is broken (wrong token index, wrong hidden state, wrong mask) and NO conclusion may be drawn about hr_peak. Read this gate FIRST.
Gate `gate-shuffle-null` (blocking): NEGATIVE CONTROL. A shuffled-target Ridge on the identical features must score |R2| < 0.05. With d_model=2560 and n~1000 the probe is in the p>n regime; without this control a high R2 could be memorisation.

#cross-modal #medgemma #sensor-text #collapse #optihealth-eiv #conditioning-collapse #data-contract-violation
