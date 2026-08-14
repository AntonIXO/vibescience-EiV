---
created_at: '2026-08-04T22:33:44+00:00'
former_ids:
- the-reported-phase-c-collapse-to-the-same-output-is-not-a-dead-conditioning-path
gates:
- blocking: true
  description: Sparse-group eiv_gen_peak_corr must exceed +0.30 under the training
    mask rule while the buggy eval mask yields |corr| < 0.10 on the identical samples
    and checkpoint.
  diagnostic_id: null
  id: sparse_corr_recovery
  mde: null
- blocking: true
  description: Sparse-group eiv_gen_between_sample_std must exceed 5.0 bpm under the
    training mask rule, versus ~0.0 bpm under the eval mask, demonstrating the constant-output
    collapse is removed.
  diagnostic_id: null
  id: constant_output_removed
  mde: null
- blocking: false
  description: The full-coverage (cov>0.5) group must be unchanged between mask modes,
    since the two rules agree there. This is the internal control.
  diagnostic_id: null
  id: full_group_unchanged
  mde: null
id: the-reported-phase-c-collapse-to-the-272801
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_gen_peak_corr
  direction: up
  magnitude_note: On the sparse (HR coverage <= 0.5) group, generated-vs-true peak
    HR correlation rises from ~0 under the buggy eval mask to clearly positive (>0.3)
    under the training mask rule, same checkpoint, no retraining.
- diagnostic_id: eiv_gen_between_sample_std
  direction: up
  magnitude_note: On the sparse group, between-sample std of generated hr_peak rises
    from ~0 bpm (literal constant output) to >5 bpm.
problem_id: phase-c-soft-token-conditioning-cbf255
problem_tags:
- optihealth-eiv
- conditioning-collapse
status: confirmed
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
The reported Phase C 'collapse to the same output' is not a dead conditioning pathway and not a structural/data limit. It is caused by a train/eval channel_mask semantics inversion that zeroes and attention-masks the HR soft-token block at generation for ~50% of samples. Training uses channel_mask = m_inherited.mean(1) > 0.05 (valid if coverage > 5%); generation uses channel_mask = m_union.mean(1) < 0.5 (valid if coverage > 50%). Since m_union == 1 - m_inherited at mask_ratio=0, channels with coverage in (5%, 50%] are trained as present but severed at inference. Applying the training mask rule at generation, with no retraining, restores conditioning on the affected samples.

## Rationale
Every prior diagnostic that concluded 'conditioning is dead' (E17 knockout, E18 arms, E18b swap-contrastive) either restricted itself to cand_full (HR coverage >= 0.5) or sampled without splitting on coverage, so none of them could observe the severed-prefix regime. E19 independently refuted the competing mean-domination explanation: the prefix has signal/mean 0.556, effective rank 90/191, and centered inter-sample cosine 0.005, i.e. it is richly sample-specific rather than constant. If the prefix is informative but half the samples arrive with the HR block zeroed, a constant output on exactly those samples is the expected consequence.

## Plan
Use scratch/diag_coverage_split.py on outputs/adapters/medgemma_12ch_peak_v1 with a peak-stratified sample, split by HR coverage at the 0.5 boundary, running the identical samples twice: --mask-mode eval (current cli_annotate rule) and --mask-mode train (pipeline training rule). No retraining; the only variable is the channel_mask predicate at generation.

## Links
Tests problem [[phase-c-soft-token-conditioning-cbf255]].
Predicts [[eiv_gen_peak_corr]] (up).
Predicts [[eiv_gen_between_sample_std]] (up).
Gate `sparse_corr_recovery` (blocking): Sparse-group eiv_gen_peak_corr must exceed +0.30 under the training mask rule while the buggy eval mask yields |corr| < 0.10 on the identical samples and checkpoint.
Gate `constant_output_removed` (blocking): Sparse-group eiv_gen_between_sample_std must exceed 5.0 bpm under the training mask rule, versus ~0.0 bpm under the eval mask, demonstrating the constant-output collapse is removed.
Gate `full_group_unchanged` (advisory): The full-coverage (cov>0.5) group must be unchanged between mask modes, since the two rules agree there. This is the internal control.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse
