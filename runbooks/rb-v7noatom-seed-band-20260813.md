---
created_at: '2026-08-13T08:01:58+00:00'
former_ids: []
gates:
- g1-all-seeds-floor-rate-below-0.05-blocking
- g2-band-reported-with-per-seed-values
- g3-no-best-seed-promotion
hypothesis_id: the-atom-fix-a71034c-removed-the-d96b6e
id: rb-v7noatom-seed-band-20260813
notes: []
priority: 1
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
status: done
topic_tags: []
updated_at: '2026-08-13T10:08:22+00:00'
---

## Prompt
E0 — MEASURE THE v7noatom BASELINE SEED BAND (control experiment; must run BEFORE any arm claims a win over v7noatom).

CONTEXT (self-contained). Repo /root/optiHealth-EiV, branch fix/soft-token-norm-scale. v7noatom (profile lora_proxy_12ch_wl_noatom, seed 42, init outputs/checkpoints/v5c/phase_b, atom fix a71034c) is the DESIGNATED DEPLOYMENT BASELINE — by STRUCTURE (floor-emission rate 0.000 where every pre-fix seed was >=0.21; annotate-path panel MAE 7.31 vs v4w 11.14, commit 89c7f82), NOT by score (its supabase MAE 11.54 is a single seed; the PRE-fix family's seed-only band on this metric was 11.37 bpm, seeds 42/43/44 -> 20.83/19.35/9.45, commit c62b983). Trivial supabase-median baseline: 4.02 bpm on the frozen 200-row draw.

STEPS
1. Add profiles lora_proxy_12ch_wl_noatom_s43 / _s44 to configs/training.yaml — byte-identical to lora_proxy_12ch_wl_noatom except seed and output dir (outputs/checkpoints/v7noatom_s43 / _s44). Diff the profile bodies before launch (manifest-diffing pitfall).
2. Train both (Phase C only, init outputs/checkpoints/v5c/phase_b; ~2.5h each on the 5070; export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True; check internal *_RC= markers in logs, not the wrapper exit code).
3. Score each on the FROZEN 200-row supabase draw: uv run python -u scratch/emission_diversity_audit.py --adapter outputs/checkpoints/<arm>/phase_c --tag <arm> --n 200 --dataset optihealth_supabase --out outputs/probes/<arm>_emissions_supabase.json. Also 128-row dense capture for the no-dense-regression companion.
4. Record per-seed: supabase MAE, floor-emission rate (exact-30 count), escaped-floor conditional MAE, dense-128 MAE. Compute band = max-min across seeds 42/43/44 (seed 42 = existing v7noatom, do NOT retrain it).
5. Record eiv_supabase_seed_band_mae_bpm via record_diagnostics; git add -f probe JSONs; commit.

GATES to commit at run time: (g1, blocking) all 3 seeds floor-rate < 0.05 — a re-flooring seed falsifies the structural-fix account and REVOKES the baseline designation; (g2) band width reported with per-seed values regardless of size; (g3) no post-hoc best-seed promotion — v7noatom seed 42 stays the named baseline checkpoint even if 43/44 score better.

WHY PRIORITY 1: every queued arm (deployment-band mass, EOS supervision, dynamic window) gates against v7noatom; without this band none of them can state a minimum detectable effect. Cost ~5h GPU total.

## Preconditions
GPU idle; outputs/checkpoints/v5c/phase_b present; data/eval_v3_1min bundle intact; commit 6cb9b97 or later checked out.

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
Run as [[the-atom-fix-a71034c-removed-the-d96b6e]].
Intended gates: `g1-all-seeds-floor-rate-below-0.05-blocking`, `g2-band-reported-with-per-seed-values`, `g3-no-best-seed-promotion`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
