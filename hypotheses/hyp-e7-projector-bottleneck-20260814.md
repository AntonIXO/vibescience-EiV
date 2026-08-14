---
created_at: '2026-08-14T08:01:25+00:00'
gates:
- blocking: true
  description: 'BLOCKING decision gate, two-sided by design: increment(best soft-token
    readout: pooled, slot-mean+max, or slot-PCA) < 0.15 CONFIRMS the projector as
    the lossy stage (encoder-side same-pool reference 0.5004). If instead it lands
    >= 0.35, the hypothesis is REFUTED and the lever moves to the LM readout — also
    a decisive, useful outcome. 0.15-0.35 = partial loss, report as such.'
  id: g1-projector-lossy
- blocking: false
  description: 'Informational: increment at the PRE-pooling per-slot representation
    (slot-mean+max concat over 96 soft tokens) vs the POST _masked_mean_soft_tokens
    pooled vector, to separate ''projector cross-attention destroys it'' from ''the
    final mean-over-slots pooling destroys it''.'
  id: g2-pool-vs-slots
id: hyp-e7-projector-bottleneck-20260814
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: down
  magnitude_note: 'Committed: the projector is the lossy stage. From encoder-side
    0.5004 (same pool/folds), I predict the BEST projector-output readout lands BELOW
    0.15 (g1 confirm zone), with the pooled variant lowest. Basis: generation-side
    hr_peak dense corr is only 0.17-0.35 and skill negative while encoder supply is
    0.50; the 15-min-era suspect was the learned pool->sensor_slots stage; cross-attention
    over 128 learnable text prototypes is a smoothing/attention-pooling operator with
    no max-preserving path (multimodal-ai lecture 07: additive/attention pooling cannot
    express extreme-value selection without a multiplicative or max path).'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- cross-modal
- pooling
- medgemma
- collapse
- sensor-text
---

## Statement
H-E7 (localization): the CrossModalProjector is the stage that destroys the peak-excursion information — the subject-grouped increment R2 measured at the projector's soft-token output is a small fraction of the 0.5004 measured at its input (post-fusion tokens) on the identical pool and folds.

## Rationale
E6 (parent) measured encoder+fusion supply at 0.5004 increment — above even the raw per-patch-max arm — while generation-side peak skill is negative on all seeds. The chain encoder(OK, 0.50) -> projector(?) -> LM(negative) has exactly one unmeasured stage at 1-min: the CrossModalProjector output. eiv_lm_hidden_peak_r2's design note says HIGH-vs-LOW at the LM localizes readout-vs-destroyed; measuring the projector output first is cheaper (no LM forward) and splits the same question one stage earlier. This is the E6 verdict's named next step, executed immediately.

## Plan
E7 probe scratch/e7_projector_bottleneck_probe.py, same pool/folds/seed as E6 (strict-clean eval_v3_1min, n=4000, GroupKFold(5) by subject, RidgeCV). Forward encoder+fusion+PROJECTOR of v9band04_s42. Feature arms: (a) pooled soft tokens via _masked_mean_soft_tokens [2560]; (b) slot mean+max concat [5120]; (c) top-256 PCA of flattened slots (fit per train fold). Targets/controls identical to E6 (raw daily peak, r2(peak|true mean) control, same increments). Reference constants from E6 on the same pool: encoder all-channel avg increment 0.5004, raw supply 0.4847. Verdict routes: g1 CONFIRM (<0.15) -> projector surgery arm justified (e.g. max-preserving pooling into slots / excursion-dedicated slot) as the NEXT GPU arm on the v9band04 recipe; g1 REFUTE (>=0.35) -> LM-side readout is the loss, revisit rb-e4-lora-lm-head with the E26 partial-credit scoping; middle -> both stages leak, quantify split via g2.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_peak_excursion_increment_r2]] (down).
Gate `g1-projector-lossy` (blocking): BLOCKING decision gate, two-sided by design: increment(best soft-token readout: pooled, slot-mean+max, or slot-PCA) < 0.15 CONFIRMS the projector as the lossy stage (encoder-side same-pool reference 0.5004). If instead it lands >= 0.35, the hypothesis is REFUTED and the lever moves to the LM readout — also a decisive, useful outcome. 0.15-0.35 = partial loss, report as such.
Gate `g2-pool-vs-slots` (advisory): Informational: increment at the PRE-pooling per-slot representation (slot-mean+max concat over 96 soft tokens) vs the POST _masked_mean_soft_tokens pooled vector, to separate 'projector cross-attention destroys it' from 'the final mean-over-slots pooling destroys it'.

#cross-modal #pooling #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
