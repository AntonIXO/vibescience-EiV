---
ambition_class: mechanism
created_at: '2026-09-01T21:15:45+00:00'
former_ids: []
gap_closure_estimate: 0.4
gates:
- blocking: true
  description: BLOCKING, CHEAP, and it runs before Phase B so a bad chain costs one
    stage instead of three. eff_rank >= 7.0 on the Phase A checkpoint via scratch/_token_geometry_probe.py.
    A veto on collapse, not a direction -- v31detached held eff_rank 10.106 and still
    emitted 10.080 bpm against a 5.961 constant, so a PASS here says nothing about
    quality.
  diagnostic_id: eiv_effective_rank
  id: g0-geometry-veto
  mde: null
- blocking: true
  description: 'BLOCKING. v41''s split-honest held-out hr_raw MAE, on the 1,485-row
    val+test pool, must not exceed arm A0''s by more than the mde. THE BAR IS A0''s
    MEASURED NUMBER, NOT A CONSTANT -- the superseded gate''s 2.602 was a 23-row subset
    and its prescribed replacement 2.969 is 97.7% in-sample, so neither is a floor
    for anything. mde 0.7 bpm is carried over from the superseded hypothesis and is
    explicitly PROVISIONAL: no split-honest repeat-run sigma exists yet, and per-family
    sd varies 58x across recipes in this project, so it must be replaced by a measured
    floor once a split-honest family closes at >=3 seeds.'
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  id: g1-no-harm-vs-the-split-honest-comparator
  mde: 0.7
- blocking: true
  description: BLOCKING, and read BEFORE g1. Emitted/true scale ratio inside [0.85,
    1.35] on the held-out pool. Outside the band the MAE is not comparable to anything,
    which is the trap v36labels (1.59, 2.08) and v31detached (2.00) both fell into
    while looking healthy in-sample.
  diagnostic_id: eiv_gen_between_sample_std
  id: g2-calibration-in-band
  mde: 0.15
- blocking: false
  description: 'THE QUESTION ITSELF; advisory on promotion, decisive on direction.
    Momentary state over_null > +0.02 on the clean transition pool. A NULL here with
    g1 and g2 passing is a real answer, not a failure: it means the chain is a good
    HR reader that does not read state, and the honest response is to DROP the state
    slot from the emitted schema rather than ship a noise generator. Floor caveat:
    eiv_momentary_state_auc_over_null currently carries an ASSUMED noise floor after
    10 evaluations (doctor flags it), so measure and register a real one from this
    arm''s own repeats before promoting on it.'
  diagnostic_id: eiv_momentary_state_auc_over_null
  id: g3-does-momentary-state-survive-training
  mde: 0.02
id: v41-pl60-state-chain-rebased-and-split-honest
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_momentary_state_auc_over_null
  direction: up
  magnitude_note: 'PRIMARY, and the reason the arm exists. Momentary state over_null
    > +0.02 on the CLEAN transition pool (nurse_stress excluded per exp-047''s do_not_retry
    -- 43% of the full pool is its shift-schedule labels and even zero-parameter arms
    lose significance there). For scale: E29 measured the raw patch grid at +0.0309
    with ZERO learned parameters, and W-SWEEP''s handcrafted raw_shallow bound reaches
    +0.0666, while the best trained head to date reaches +0.0218. A trained chain
    landing below E29''s zero-parameter grid readout has destroyed information.'
- diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: none
  magnitude_note: NO-HARM, not an improvement target. v41's split-honest held-out
    hr_raw MAE must not be worse than arm A0's by more than the band, on the identical
    1,485-row pool. This replaces the superseded g1's constant bar of 2.602 / 2.969,
    both of which are in-sample.
- diagnostic_id: eiv_gen_between_sample_std
  direction: none
  magnitude_note: CONTROL, read FIRST. Emitted/true sd ratio inside [0.85, 1.35].
    This is where v30state died (2.77) and v36labels died (1.59, 2.08) while looking
    acceptable in-sample.
problem_id: the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all
problem_tags:
- optihealth-eiv
- subject-heldout
- matched-baseline
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: a-held-out-scored-chain-combining-every-079038
topic_tags:
- deployment-distribution
- optihealth
- readout
---

## Statement
v41 RE-BASED. Same scientific claim as the hypothesis it supersedes -- that combining every separately-confirmed state lever (PL60/ST30 patch geometry, patch-axis state supervision at state_patch_weight 0.5, soft state targets) makes the momentary state signal survive training -- but with gates that are no longer anchored to a number that turned out to be in-sample, and with the chain made split-honest.

WHY IT IS SUPERSEDED RATHER THAN EDITED. Its g1 read "held-out dense hr_raw MAE <= 2.602 (the incumbent's 3-seed held-out mean)". Two separate defects, found in that order:
  (1) 2.602 was the 23-row held-out SUBSET of an unrestricted 128-row draw, not the 1,485-row pool. Re-basing to the pooled 2.969 was the fix the previous session prescribed.
  (2) 2.969 is ALSO not a held-out number. Rule-out `that-the-scorer-s-split-heldout-flag-makes-the-d88faa`: `--split heldout` filters the eval side only, `split` is a Phase C parameter defaulting to None, 6 of 96 profiles set it, and 125 of the 128 rows in every incumbent capture are subjects the model trained on. So the re-basing the previous session prescribed would have moved the bar from one in-sample number to another.

THE BAR IS NOW A MEASURED SPLIT-HONEST COMPARATOR, not a constant. Experiment exp-t1-tan-ablation-heads-20260901 established the pool and the instrument; its sibling arm A0 (unit eiv-t1llm, band04 + `split: train` off the frozen incumbent Phase B, scored `--split heldout` on the same 1,485-row pool) is the first split-honest LLM number this project will have. g1 is stated against THAT, so both sides of the comparison honour the split.

COMMITTED PREDICTION, unchanged in substance from the superseded hypothesis: momentary state over_null > +0.02 on the clean transition pool (nurse_stress excluded), with held-out hr_raw and calibration not degraded relative to the split-honest comparator.

WHAT MAKES THIS RUNNABLE AT ALL. The superseded hypothesis could not be tested: its Phase A OOMed at step 0 asking for 1013.4 MiB = one fp32 projector output at the composition-sum batch of 92 and 47 patches. Hypothesis `pl60-fits-once-the-batch-92-projector-activation-is-chunked` adds `sensor_micro_batches: 4`, which chunks that tensor to 253.4 MiB and is proven arithmetically inert (dropout off: exact forward, 2.7e-07 relative gradients; dropout on: residue 1.00-1.11x a redrawn-dropout control). v41 must not launch until that hypothesis's g1 and g2 are recorded.

WHAT I AM DELIBERATELY NOT CARRYING OVER. The PL120 fallback. v30state (PL120 + state_patch_weight 0.1 + soft) read held-out dense 19.935 at scale 2.77, and exp-v22/v29 measured heavier heads costing 1.5-1.8 eff_rank. If PL60 does not fit even with micro-batching, the answer is to expose LLM gradient checkpointing, not to fall back to a geometry that already failed with these heads.

## Rationale
NEXT.md's own instruction was to re-base v41's g1 "via the vault (update_problem / a superseding hypothesis), never by editing the hypothesis file". The re-basing it prescribed (2.602 -> 2.969) is necessary but not sufficient, because the leak audit run tonight shows 2.969 is in-sample too. A gate anchored to an in-sample constant would let v41 pass or fail for reasons that have nothing to do with state.

## Plan
PRECONDITION: hypothesis `pl60-fits-once-the-batch-92-projector-activation-is-chunked` must have g1 (Phase A completes at PL60) and g2 (the Phase C cell settled either way) RECORDED. Unit eiv-t2smoke is running that now.
THEN: full A->B->C at PL60 with `sensor_micro_batches: 4` on Phase A and B, and `split: train` injected into all three stages via a temporary profile config (never by editing configs/training.yaml, which the guard tests treat as a recorded artefact). Single seed for the gate; a 3-seed family only if g1 and g2 pass.
SCORING: `--split heldout` on the 1,485-row pool, plus the momentary-state readout on the clean transition pool with nurse_stress excluded.
COMPARATOR: arm A0 from exp-t1, which is the same band04 recipe under the same split on the same pool -- so v41 vs A0 differs in patch geometry and state supervision only.

## Links
Tests problem [[the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all]].
Predicts [[eiv_momentary_state_auc_over_null]] (up).
Predicts [[eiv_splithonest_heldout_hr_raw_mae_bpm]] (none).
Predicts [[eiv_gen_between_sample_std]] (none).
Gate `g0-geometry-veto` (blocking): BLOCKING, CHEAP, and it runs before Phase B so a bad chain costs one stage instead of three. eff_rank >= 7.0 on the Phase A checkpoint via scratch/_token_geometry_probe.py. A veto on collapse, not a direction -- v31detached held eff_rank 10.106 and still emitted 10.080 bpm against a 5.961 constant, so a PASS here says nothing about quality.
Gate `g1-no-harm-vs-the-split-honest-comparator` (blocking): BLOCKING. v41's split-honest held-out hr_raw MAE, on the 1,485-row val+test pool, must not exceed arm A0's by more than the mde. THE BAR IS A0's MEASURED NUMBER, NOT A CONSTANT -- the superseded gate's 2.602 was a 23-row subset and its prescribed replacement 2.969 is 97.7% in-sample, so neither is a floor for anything. mde 0.7 bpm is carried over from the superseded hypothesis and is explicitly PROVISIONAL: no split-honest repeat-run sigma exists yet, and per-family sd varies 58x across recipes in this project, so it must be replaced by a measured floor once a split-honest family closes at >=3 seeds.
Gate `g2-calibration-in-band` (blocking): BLOCKING, and read BEFORE g1. Emitted/true scale ratio inside [0.85, 1.35] on the held-out pool. Outside the band the MAE is not comparable to anything, which is the trap v36labels (1.59, 2.08) and v31detached (2.00) both fell into while looking healthy in-sample.
Gate `g3-does-momentary-state-survive-training` (advisory): THE QUESTION ITSELF; advisory on promotion, decisive on direction. Momentary state over_null > +0.02 on the clean transition pool. A NULL here with g1 and g2 passing is a real answer, not a failure: it means the chain is a good HR reader that does not read state, and the honest response is to DROP the state slot from the emitted schema rather than ship a noise generator. Floor caveat: eiv_momentary_state_auc_over_null currently carries an ASSUMED noise floor after 10 evaluations (doctor flags it), so measure and register a real one from this arm's own repeats before promoting on it.
Supersedes [[a-held-out-scored-chain-combining-every-079038]].

#deployment-distribution #optihealth #readout #optihealth-eiv #subject-heldout #matched-baseline
