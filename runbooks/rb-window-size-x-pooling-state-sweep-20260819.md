---
created_at: '2026-08-19T20:41:21+00:00'
former_ids: []
gates: []
hypothesis_id: w-sweep-rb-window-size-x-pooling-state-sweep-73469a
id: rb-window-size-x-pooling-state-sweep-20260819
notes:
- at: '2026-08-19T21:20:56+00:00'
  text: '2026-08-19: executed same day as registered (exp-049, verdict supports).
    All cheap axes done; the conditional expensive branch (new PL30/ST15 or PL240
    training) NOT triggered as specified — the best cell (W30/max on existing PL60
    chains) already cleared g1, and the honest bound (raw_shallow W30 +0.067) shows
    the gap is supply, not geometry. Do not train new patch geometries on this corpus
    for state.'
priority: 4
problem_id: peri-ema-window-destroys-momentary-labels-20260815
status: done
topic_tags:
- state-supervision
- pooling
- epoch-resolution
- control
updated_at: '2026-08-19T21:20:56+00:00'
---

## Prompt
WINDOW-SIZE x POOLING SWEEP FOR MOMENTARY STATE — pooled-estimator design, NOT more single-seed arms. Repo /root/optiHealth-EiV, RTX 5070.

CONTEXT A FRESH SESSION NEEDS. exp-047 (test_invalid) and exp-048 (refutes) established on the 12,547-corpus clean transition pool (nurse_stress excluded; rule-out that-the-full-corpus...d55a19): (1) the trained per-patch state head at PL60/ST30 shows a weak POSITIVE effect on 10/10 (seed x stage) cells, sign-test p~=0.03, mean over_null ~+0.006, but only 1/5 seeds clears z>=2 — BELOW single-seed resolution; (2) PL120 is weaker at both stages; (3) the E29-era result that the signal lives at W=120 was measured with W (the EVAL window) fixed while today's arms varied PL (the SUPERVISION patch) — the two axes have never been swept independently; (4) avg-pooling over covering patches is the only read tested; the pooling-operator record (references/pooling-operator-excursion-lever.md, avg loses ~0.40 R2 excursion vs avg+max on the HR axis) has never been applied to the STATE read.

DESIGN — three cheap axes on EXISTING checkpoints first (CPU/GPU-encode only, no training):
  Axis 1 EVAL WINDOW W: score the trained heads and token probes at W in {30, 60, 120, 240} min (current fixed W=120). W is an eval parameter — zero new training.
  Axis 2 HEAD-SCORE POOLING over covering patches: mean (current) vs max vs mean+max of P(flow). Also zero new training.
  Axis 3 POOLED ESTIMATOR ACROSS SEEDS — the actual fix for the resolution problem: treat the 5 PL60 seeds as repeated measures; per-seed over_null with a COMMON permutation set (same rng seeds 1000..1199), then (a) mean-of-seeds vs pooled null-of-means, (b) Wilcoxon over per-window paired scores aggregated across seeds. This directly tests the exp-048 residual ('weak consistent ~+0.006 effect') with ~sqrt(5) tighter resolution.
ONLY IF an (W, pooling) cell separates from the current (120, mean) read by more than the pooled band: train NEW patch geometries (e.g. PL30/ST15 = 95 patches — check VRAM at batch 4-8; PL240/ST120 = 11 patches) at >=3 seeds and re-score.

GATES to commit at run time: g1 (blocking) pooled 5-seed estimator on the BEST preregistered cell clears its pooled null at p<0.01; g2 (blocking) the same estimator on the HARD-control checkpoints (phase_a_12ch_analog_hard_s42/43 exist) stays under half the effect — attribution guard; g3 (non-blocking) W-response shape reported (does over_null peak at the W the supervision patch matches?); g4 (honesty) raw_shallow re-scored at each W on the same rows — the handcrafted bound moves with W too and every claim is priced against it.

PITFALLS. Within-window permutation ONLY (subject/day/clock confounds); recompute the null per (W, pooling, estimator) cell — nulls ranged 0.47-0.57 today; the trained-head null is ~2.5x tighter than refit-probe nulls (score with fixed logits + label perms, never refit); nurse_stress stays excluded; PL60 B stages need batch 8 (b16 OOMs); quote every cell with n, null mean+-sd, z, estimator, and W.

## Preconditions
Corpus data/canonical [12547,1440,12] sha ac7d2b1c (or later, re-measure references if changed). Checkpoints already on disk: PL60 A+B x seeds 42-46 (outputs/checkpoints/phase_a_12ch_analog_patch60_s*, b_patch60_s*), PL120 A+B x 42/43 (phase_a_12ch_analog_patch_hs_s*, b_patch120_s*). Instrument: scratch/b_wave_eval.py (trained-head, fixed scores + label perms) and scratch/a1_eval_state_encoders.py (token probes, RF+ridge). Clean pool ONLY (nurse_stress excluded, rule-out d55a19). Pipeline already supports patch_len/stride as profile keys (no re-merge) and saves state heads at A and B (2026-08-19 fixes).

## Log
- **2026-08-19T21:20:56+00:00** — 2026-08-19: executed same day as registered (exp-049, verdict supports). All cheap axes done; the conditional expensive branch (new PL30/ST15 or PL240 training) NOT triggered as specified — the best cell (W30/max on existing PL60 chains) already cleared g1, and the honest bound (raw_shallow W30 +0.067) shows the gap is supply, not geometry. Do not train new patch geometries on this corpus for state.

## Links
Queued for problem [[peri-ema-window-destroys-momentary-labels-20260815]].
Run as [[w-sweep-rb-window-size-x-pooling-state-sweep-73469a]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#state-supervision #pooling #epoch-resolution #control
