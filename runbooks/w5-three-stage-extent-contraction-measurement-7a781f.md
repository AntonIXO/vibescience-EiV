---
created_at: '2026-08-20T21:17:39+00:00'
former_ids: []
gates:
- 'BLOCKING AND FIRST: this runbook produces the evidence that decides whether ANY
  of H-W6/H-W7/H-W8 is funded. It is CPU-only. Do not launch a GPU arm from this problem
  before it closes.'
- 'Report eiv_extent_variance_compression_ratio at three stages (naive 1440-mean /
  trained-encoder patch tokens / production post-projector) for all 7 short and 4
  long cohorts, each with mean coverage c alongside. Gate g1-three-stage-compression-measured:
  if the encoder-stage ratio on short cohorts is >= 0.5, H-W5 is REFUTED and the family
  is not funded.'
- Run the RANDOM-INIT encoder at matched geometry (gate g2-random-init-control, bar
  0.15). Without it, 'training destroyed the spread' and 'this pooling never preserved
  it' are indistinguishable and imply opposite next moves. This control has repeatedly
  been the most informative number in this vault.
- Run the masked re-pool headroom arm on the SAME frozen tokens (gate g3-masked-repool-headroom,
  bar 0.60). Zero training required -- it is a post-hoc re-pooling of already-computed
  tokens.
- Report the shuffle control (gate g4). Confirm the ratio does not manufacture apparent
  spread on row-shuffled targets.
- Load encoder weights with strict=True and assert the checkpoint's window_timesteps
  / num_channels match merge_manifest.json before reading anything. A checkpoint trained
  at another resolution loads cleanly against this tensor and silently invalidates
  the run.
hypothesis_id: null
id: w5-three-stage-extent-contraction-measurement-7a781f
notes: []
priority: 1
problem_id: extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling
status: queued
topic_tags:
- extent-heterogeneity
- pooling
- denominator
updated_at: '2026-08-20T21:17:39+00:00'
---

## Prompt
W5 -- THREE-STAGE EXTENT CONTRACTION MEASUREMENT (CPU-only, blocking gate for the whole extent-heterogeneity family). This prompt is SELF-CONTAINED; a fresh session has no context from the one that queued it.

=== WHAT IS ALREADY MEASURED (corpus ac7d2b1c, tensor [12547,1440,12], 2026-08-20) ===
The canonical tensor is population-normalised and the fill value 0.0 IS the corpus median (hr_mean median 73.00 bpm, iqr 23.62). Unmasked pooling therefore computes EXACTLY
    naive_bpm = median + c*(present_bpm - median),   c = fraction of present minutes
Verified to machine precision: regressing naive on c*present gives slope 1.0000, intercept 0.00000, R2 1.00000 on n=12,503 (scratch/w4_extent_premise_audit.py -> logs/w4_extent_premise_audit.json).

Consequence measured at the NAIVE stage (scratch/w4b_variance_compression.py -> logs/w4b_variance_compression.json), as std(naive_bpm)/std(present_bpm) within each cohort:
  cohort                        mean_c   sd_true  sd_naive   ratio
  popane_s4                     0.0187    10.41      0.20   0.0189
  neuro_stress_resilience_hci   0.0226    15.35      0.35   0.0231
  popane_s3                     0.0257     9.61      0.24   0.0251
  case                          0.0285     7.59      0.22   0.0285
  nurse_stress                  0.0419     6.67      0.67   0.1008
  wesad                         0.0831     7.61      0.63   0.0825
  wearable_exam_stress          0.1794     8.13      2.31   0.2838
  lifesnaps                     0.7958     9.29      6.50   0.6992
  pmdata                        0.8703     8.35      7.15   0.8567
  big_ideas_glycemic            0.7581    10.87      7.81   0.7183
  optihealth_supabase           0.6672     9.88      5.01   0.5071
CASE retains 2.85% of its between-subject spread. The ratio equals c to four decimals.

=== WHAT THIS RUNBOOK MUST ESTABLISH ===
Whether that contraction SURVIVES into the representation the model actually consumes. The encoder does NOT compute a naive 1440-mean: TemporalPatching uses patch_len=120, stride=60 -> 23 tokens, so for a 41-minute CASE session only ~1 of 23 patches contains any measurement and the predicted encoder-stage ratio is nearer n_real_patches/23 (~0.04-0.09) than c. Measure, do not assume.

Stage (a) naive 1440-mean -- already done, use as reference.
Stage (b) trained-encoder patch tokens, pooled exactly as the code pools them.
Stage (c) production post-projector soft tokens (channel_fusion + cross_modal projector).
Plus TWO controls: random-init encoder at matched geometry, and a masked re-pool of the SAME frozen stage-(b) tokens.

=== HYPOTHESIS TO SCORE ===
Open the experiment against hypothesis `h-w5-contraction-survives-into-the-91ad0b` (do NOT open a new hypothesis). Its preregistered gates are g1-three-stage-compression-measured (bar 0.5), g2-random-init-control (0.15), g3-masked-repool-headroom (0.60), g4-shuffle-control (0.05). Recover the exact gate ids and bars from /root/vibescience-vault/hypotheses/h-w5-contraction-survives-into-the-91ad0b.md before scoring -- do not trust this prompt's copy.

=== DECISION RULE, PREREGISTERED ===
* Encoder-stage ratio on short cohorts >= 0.5  -> H-W5 REFUTED. The contraction does not reach the representation. Record it and do NOT fund H-W6/H-W7/H-W8 on contraction grounds.
* Ratio low AND masked re-pool lifts it >= 0.60 -> H-W5 SUPPORTED, H-W6 is funded (mask-weighted pooling, ~2h GPU x3 seeds).
* Ratio low AND masked re-pool does NOT lift it -> the information was destroyed at PATCH CONSTRUCTION, not at pooling. The pooling-site fix is dead; file that as a rule_out and re-scope to the encoder input path.
* Trained and random-init differ by >0.15 -> the mechanism claim (architectural, presence-blind pooling) is WRONG and the prescribed fix does not follow.

=== PITFALLS THAT HAVE ALREADY COST THIS PROJECT ===
1. DENORMALISE VIA scratch/canon_utils.channel_denorm ONLY. population_stats.json stores median/iqr as per-channel LISTS, not a name-keyed dict; a bare .get() silently returns (0.0, 1.0) and leaves values normalised while the code believes bpm.
2. Do NOT score this on a MEAN. rule_out `that-the-mean-gap-naive-pooled-mean-present-5bb2e3` records that the mean gap is structurally blind here: it failed its own preregistered bar on 2 of 4 cohorts purely because WESAD (74.7) and CASE (71.5) sit on the corpus median (73.0). Contraction toward a fixed point is invisible to any statistic measured AT that fixed point. Score SPREAD.
3. Report per COHORT with c alongside, never pooled. ratio ~= c is the NULL MODEL; an intervention must beat that, not beat zero.
4. This is NOT window shortening and NOT a patch-grid change. W stays 1440, the grid stays 120m/23tok. `ro-window-shortening-20260805` and `that-the-encoder-s-23-token-patch-grid-is-under-bc9aef` both stand and are not being challenged.

## Preconditions
Corpus data/canonical at git_sha ac7d2b1c (tensor [12547,1440,12]). A Phase-B checkpoint of the current recipe available for the encoder-stage read. CPU sufficient; no GPU required for the raw and re-pool arms. Read scratch/w4_extent_premise_audit.py and scratch/w4b_variance_compression.py first -- they already compute the naive-stage reference and the denormalisation is handled by scratch/canon_utils.channel_denorm.

## Links
Queued for problem [[extent-heterogeneity-short-lab-sessions-are-contracted-onto-the-corpus-median-by-unmasked-pooling]].
Intended gates: `BLOCKING AND FIRST: this runbook produces the evidence that decides whether ANY of H-W6/H-W7/H-W8 is funded. It is CPU-only. Do not launch a GPU arm from this problem before it closes.`, `Report eiv_extent_variance_compression_ratio at three stages (naive 1440-mean / trained-encoder patch tokens / production post-projector) for all 7 short and 4 long cohorts, each with mean coverage c alongside. Gate g1-three-stage-compression-measured: if the encoder-stage ratio on short cohorts is >= 0.5, H-W5 is REFUTED and the family is not funded.`, `Run the RANDOM-INIT encoder at matched geometry (gate g2-random-init-control, bar 0.15). Without it, 'training destroyed the spread' and 'this pooling never preserved it' are indistinguishable and imply opposite next moves. This control has repeatedly been the most informative number in this vault.`, `Run the masked re-pool headroom arm on the SAME frozen tokens (gate g3-masked-repool-headroom, bar 0.60). Zero training required -- it is a post-hoc re-pooling of already-computed tokens.`, `Report the shuffle control (gate g4). Confirm the ratio does not manufacture apparent spread on row-shuffled targets.`, `Load encoder weights with strict=True and assert the checkpoint's window_timesteps / num_channels match merge_manifest.json before reading anything. A checkpoint trained at another resolution loads cleanly against this tensor and silently invalidates the run.`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#extent-heterogeneity #pooling #denominator
