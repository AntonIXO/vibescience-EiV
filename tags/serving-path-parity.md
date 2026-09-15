---
aliases:
- train-serve-mismatch
- serving-path-drift
- silent-fallback
- train-eval-skew
axis: topic
created_at: '2026-08-27T21:36:15+00:00'
id: serving-path-parity
---

## Description
A defect where the computation performed at SERVING/INFERENCE time differs from the one performed at TRAINING time, with no error raised, so a trained capability is silently not exercised at deployment. Distinct from `deployment-distribution` (which is about the POPULATION differing between benchmark and product) -- this tag is about the CODE PATH differing while the population is held fixed. Canonical instances in optiHealth-EiV: (1) E22's channel_mask semantics inversion (training read 1=PRESENT with a >0.05 gate, inference read 1=MISSING with a <0.5 gate, zeroing HR on 49.8% of samples at generation); (2) H-W10's patch_pool_mode / patch_presence gap (masked_avg trained but served as plain avg because the mode was not persisted in the manifest and no inference site supplied patch_presence, while the projector silently falls back to a presence-blind average when it is None). SIGNATURE TO LOOK FOR: two instruments measuring the same intervention disagree in SIGN, and they differ in exactly one argument or one gate rule. The instrument that PASSED is usually the one that supplied the missing argument. STANDING RULE: an optional argument that changes a trained operator's semantics must either be persisted and restored, or its absence must RAISE -- never silently degrade.

*Axis:* `topic`

## Aliases
- train-serve-mismatch
- serving-path-drift
- silent-fallback
- train-eval-skew
