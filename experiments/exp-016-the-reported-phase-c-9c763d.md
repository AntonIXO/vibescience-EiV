---
artifacts: []
closed: true
config_note: 'Ran scratch/diag_coverage_split.py twice on outputs/adapters/medgemma_12ch_peak_v1,
  peak-stratified n=24/group, split by HR coverage at 0.5. ONLY variable: the channel_mask
  predicate at generation (--mask-mode eval = m_union.mean(1)<0.5 i.e. coverage>50%,
  vs --mask-mode train = coverage>5% matching pipeline.py:3055). No retraining, identical
  checkpoint and samples. Then applied the fix across all 9 production inference sites
  via a shared channel_validity_mask() helper (CHANNEL_VALIDITY_MIN_COVERAGE=0.05)
  in src/cli_annotate.py, and re-ran the full stratified generation eval (scratch/eval_hr_stratified.py,
  6/cell, 48 samples) before and after the fix.'
crash_reason: ''
created_at: '2026-08-04T22:43:51+00:00'
diagnostics_measured:
- after: 0.615
  before: -0.0
  diagnostic_id: eiv_gen_peak_corr
- after: 14.3
  before: 0.0
  diagnostic_id: eiv_gen_between_sample_std
do_not_retry: []
external_run: 'local: outputs/probes/e21_coverage_split.json + outputs/probes/e21_coverage_split_trainmask.json'
former_ids:
- the-reported-phase-c-collapse-to-the-same-output-is-not-a-dead-conditioning-path-exp-2026-08-04t22-43-51
gate_results:
- evidence: ''
  gate_id: sparse_corr_recovery
  passed: true
- evidence: ''
  gate_id: constant_output_removed
  passed: true
- evidence: ''
  gate_id: full_group_unchanged
  passed: true
git_ref: ''
hypothesis_id: the-reported-phase-c-collapse-to-the-272801
id: exp-016-the-reported-phase-c-9c763d
key_numbers: {}
observed_effects:
- delta: 0.615
  diagnostic_id: eiv_gen_peak_corr
  direction: up
- delta: 14.3
  diagnostic_id: eiv_gen_between_sample_std
  direction: up
parent_experiment_id: null
prediction_match:
  overall: true
  per_diagnostic:
    eiv_gen_between_sample_std: true
    eiv_gen_peak_corr: true
provenance: {}
verdict: supports
---

## Summary
Tests hypothesis [[the-reported-phase-c-collapse-to-the-272801]].
External run: local: outputs/probes/e21_coverage_split.json + outputs/probes/e21_coverage_split_trainmask.json

## Measurements
- [[eiv_gen_peak_corr]]: before -0.0 → after 0.615  (Δ +0.615, up ✓ predicted up)
- [[eiv_gen_between_sample_std]]: before 0.0 → after 14.3  (Δ +14.3, up ✓ predicted up)

## Gates
- `sparse_corr_recovery`: PASS
- `constant_output_removed`: PASS
- `full_group_unchanged`: PASS

**Verdict:** supports

## Notes
All three preregistered gates passed. Root cause of the Phase C 'collapse to the same output' is a train/eval channel_mask semantics inversion, not a structural/data limit.

MECHANISM: training gates channel validity on m_inherited.mean(1) > 0.05 (m_inherited: 1=PRESENT, so valid if coverage>5%); all 9 inference sites gated on m_union.mean(1) < 0.5 (m_union: 1=MISSING, so valid if coverage>50%). At Phase C mask_ratio=0, m_union == 1 - m_inherited exactly, so any channel with coverage in (5%, 50%] was trained as PRESENT but at generation had its soft-token slots zeroed AND attention-masked. Blast radius: 49.8% of all samples on the HR channel.

WHY IT SURVIVED THREE PRIOR INVESTIGATIONS: scratch/_diag_common.py:87 restricts diagnostics to cand_full (hr_cov >= 0.5) to avoid 'trivially ignored' samples. That is exactly the half of the corpus where the two mask rules AGREE. E17 (attention knockout), E18 (three training arms) and E18b (swap-contrastive) were all measured in the only regime where the bug is invisible, then generalized corpus-wide into a 'structural/data limit' FINAL VERDICT recommending the bridge be abandoned. The documented ~50% Phase-C 'bistability' and hr_regression_weight sensitivity are plausibly the same 50/50 coverage split appearing as randomness.

SECONDARY FINDING (E19/E20): the competing mean-domination hypothesis was refuted with a text-token control. The prefix has signal/mean 0.556, effective rank 90/191, centered inter-sample cosine 0.005, and is 2.1x MORE variable than real MedGemma text embeddings. Also, decomposing within-sample vs between-sample variation showed the LM was ALWAYS discriminating full-coverage samples (hr_peak between-sample spread 23 bpm vs 2 bpm steering noise, ratio 11.6). 'Collapses to the same output' was partly a measurement-framing error from pooling those two sources of variation.

END-TO-END EFFECT (scratch/eval_hr_stratified.py, 48 samples, before vs after the code fix): lifesnaps predicted peak range went from a literal constant [112,112] to [94,137]; sparse tachy bucket accuracy 0.00 -> 0.33; sparse high140+ accuracy 0.00 -> 0.67 with MAE 41.4 -> 27.8; tachycardic samples correctly labeled 2/24 -> 8/24; +30 bpm shift response +1.3 -> +6.4 bpm with responsive fraction 0.06 -> 0.33.

HONEST LIMITS: this fix does NOT make Phase C good. Overall peak corr is still only ~0.08 and MAE ~23 bpm; the +30 bpm shift response is +6.4 vs an ideal +30; full-coverage peak corr is +0.175 and was never affected by this bug. Those are real residual problems. The claim established here is narrower and specific: the reported CONSTANT-OUTPUT collapse was an inference bug affecting half the corpus, and the evidence base for declaring the bridge structurally dead is invalid. Next target per E20: prefix HR direction has cos = -0.015 with the LM's native numeric direction (orthogonal encoding), plus the ICLR 2024 result that an additive prefix provably cannot change relative attention over content - both argue for zero-init gated cross-attention (LLaMA-Adapter, arXiv:2303.16199) rather than more additive-prefix tuning.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse
