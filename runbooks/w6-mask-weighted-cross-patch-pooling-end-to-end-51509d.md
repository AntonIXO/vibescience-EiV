---
created_at: '2026-08-20T21:19:27+00:00'
former_ids: []
gates:
- 'PRECONDITION: w5 closed with H-W5 SUPPORTED (gate g3-masked-repool-headroom passed,
  i.e. masked re-pooling of frozen tokens lifts short-cohort ratio >= 0.60). If g3
  failed, the information was destroyed at patch construction and this arm is NOT
  funded -- abandon and re-scope to the encoder input path.'
- 'g1-dense-no-harm is BLOCKING: eiv_dense128_hr_raw_mae_bpm must not regress beyond
  1.0 bpm. Mask-weighting moves the output of EVERY input with a missing channel,
  which is nearly all of them.'
- 'g2-seed-band-respected: >=3 seeds, per-seed values and the worst seed reported.
  The supabase MAE seed band is 6.29 bpm and EXCEEDS the 4.02 trivial baseline.'
- 'g3-single-variable-diff: yaml-diff the profile bodies; only the pooling mode, seed
  and notes may differ.'
- 'g4-excursion-guard: eiv_peak_excursion_increment_r2 must not fall by more than
  its recipe band 0.0457.'
- Validate the scorer on the BASELINE artifacts first -- reproduce v10conly's recorded
  numbers before scoring the new arm. A scorer bug found after scoring the new arm
  poisons the comparison.
hypothesis_id: null
id: w6-mask-weighted-cross-patch-pooling-end-to-end-51509d
notes: []
priority: 2
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
status: queued
topic_tags:
- extent-heterogeneity
- pooling
- padding
- deployment-distribution
updated_at: '2026-08-20T21:19:27+00:00'
---

## Prompt
W6 -- MASK-WEIGHTED CROSS-PATCH POOLING (end-to-end arm). SELF-CONTAINED prompt; run only after W5 passes.

=== THE CHANGE ===
Intervention `eiv-mask-weighted-patch-pooling`. Today cross_modal.py:323 calls adaptive_avg_pool2d over ALL patches regardless of presence. Replace it with a presence-normalised pool: weight each patch token by its fraction of measured (M_mask) minutes, normalise by the sum of those weights, so a pure-fill patch contributes 0 instead of contributing the fill value (which IS the corpus median under population normalisation).

=== READ THE SOURCE COMMENT FIRST -- IT ANTICIPATES THIS ARM ===
cross_modal.py:260-270 records that presence-weighting was considered and deliberately NOT done, because it 'would move the output of every input that has a missing channel, which is every checkpoint and every recorded result in the repo', and ends: 'If it is wanted, it is its own change with its own control arm.' This runbook IS that control arm. The blast radius is real and is exactly why the dense no-harm gate is blocking.

=== SCORE AGAINST ===
Hypothesis `h-w6-mask-weighted-pooling-restores-short-9da5b6`. Do NOT open a new hypothesis. Recover exact gate ids and bars from /root/vibescience-vault/hypotheses/h-w6-mask-weighted-pooling-restores-short-9da5b6.md -- the inline copies in this prompt can drift.
Primary effect: eiv_extent_variance_compression_ratio UP on short cohorts (measured base 0.019-0.28, bar >=0.60), reported per cohort with mean coverage c alongside as the null model.
Secondary: eiv_dense128_hr_raw_mae_bpm direction NONE (honest none -- mask-weighting is nearly a no-op on dense days), eiv_supabase_hr_raw_mae_bpm directional only and explicitly underpowered.

=== WHAT WOULD REFUTE IT ===
If the dense population regresses beyond 1.0 bpm, the contraction was LOAD-BEARING: the model had learned to expect the diluted scale and correcting it invalidates the learned calibration. That is a plausible outcome and it refutes the arm even if the short cohorts improve. Report it as such rather than proposing a variant.
If short-cohort ratio rises but nothing downstream moves, spread was not the binding constraint -- retire the extent family in favour of label supply (see arXiv:2405.09563 on stressor-type matching).

=== DO NOT CONFUSE WITH THE DEAD POOLING ARMS ===
`ro-dual-avg-max-pooling-downstream-20260805` (delta +0.0090) and `ro-avgmax-pooling-production-site-20260815` (gap +0.0235) killed avg->max/avgmax at this same site. Those change WHICH ORDER STATISTIC is pooled while staying presence-blind; all of avg/max/avgmax contract identically toward the fill value. This arm keeps the operator and changes the DENOMINATOR. State that distinction explicitly in the close notes so the next session does not read this as a re-run.

## Preconditions
H-W5 supported with g3 passed. GPU free (~2h per arm x3 seeds on the RTX 5070, <11.25 GiB). A fixed Phase-B checkpoint so the only variable is the Phase-C pooling. Baseline is the RECIPE (v10conly), not a stored checkpoint.

## Links
Queued for problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Intended gates: `PRECONDITION: w5 closed with H-W5 SUPPORTED (gate g3-masked-repool-headroom passed, i.e. masked re-pooling of frozen tokens lifts short-cohort ratio >= 0.60). If g3 failed, the information was destroyed at patch construction and this arm is NOT funded -- abandon and re-scope to the encoder input path.`, `g1-dense-no-harm is BLOCKING: eiv_dense128_hr_raw_mae_bpm must not regress beyond 1.0 bpm. Mask-weighting moves the output of EVERY input with a missing channel, which is nearly all of them.`, `g2-seed-band-respected: >=3 seeds, per-seed values and the worst seed reported. The supabase MAE seed band is 6.29 bpm and EXCEEDS the 4.02 trivial baseline.`, `g3-single-variable-diff: yaml-diff the profile bodies; only the pooling mode, seed and notes may differ.`, `g4-excursion-guard: eiv_peak_excursion_increment_r2 must not fall by more than its recipe band 0.0457.`, `Validate the scorer on the BASELINE artifacts first -- reproduce v10conly's recorded numbers before scoring the new arm. A scorer bug found after scoring the new arm poisons the comparison.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#extent-heterogeneity #pooling #padding #deployment-distribution
