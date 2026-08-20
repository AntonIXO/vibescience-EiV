---
created_at: '2026-08-20T00:38:23+00:00'
former_ids: []
id: that-the-encoder-s-23-token-patch-grid-is-under-bc9aef
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-20T00:38:23+00:00'
topic_tags:
- pooling
- epoch-resolution
- baseline
---

## Claim
That the encoder's 23-token patch grid is under-budgeted vs LSM-2's 144 tokens/channel, and that raising tokens-per-channel (10-min patches) would raise the raw linear peak/excursion content available to the encoder at a fixed 24h window. ELIMINATED at the raw-content stage: the incumbent grid carries the MOST linear peak content of the four geometries and LSM-2's grid the LEAST.

## Method
rb-w3 Stage 1, exactly as the runbook prescribed: CPU-only ridge readout over each candidate grid built directly on raw canonical data — the pre-GPU gate the eiv_peak_excursion_increment_r2 diagnostic exists for. Stage 2 (GPU retrains at the four geometries) is gated NEGATIVE and must not run: the best-candidate geometry is the incumbent.

## Evidence
scratch/w3_stage1_grid.py on the 12,547 corpus strict-clean pool (n=4354 days, 93 subjects, subject-grouped OOF RidgeCV; per-patch raw HR means as features): increment over R2(peak|true mean) — A incumbent 120m/23tok +0.0266; D 60m/47tok +0.0142; C 30m/95tok +0.0115; B LSM-2 10m/144tok -0.0143 (NEGATIVE: worse than the daily mean alone). R2(mean) ~0.999 for all grids (level saturated everywhere, no reallocation confound). Token count and peak content are ANTI-correlated on this pool — finer patches average less noise per token and the ridge cannot recover the max from many noisy means. INSTRUMENT CAVEAT recorded honestly: the stored old-corpus reference (increment +0.0030, R2(peak|mean)=0.4371) does not reproduce on this pool (base here -0.0056) — corpus and pool changed, so cross-session absolute comparisons are void per references/controlled-ab-runs.md; the WITHIN-RUN 4-grid contrast on one instrument is what carries this rule-out. logs/w3_stage1_grid.json.

## Links
Rules out for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].

#pooling #epoch-resolution #baseline
