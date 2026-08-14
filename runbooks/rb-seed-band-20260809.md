---
created_at: '2026-08-11T21:22:21+00:00'
former_ids: []
gates:
- gate-seed-band-measured
- gate-band-vs-legacy-spread
- gate-no-post-hoc-best-seed
hypothesis_id: with-phase-c-now-seeded-0b5aff2-three-fae38a
id: rb-seed-band-20260809
notes: []
priority: 5
problem_id: phase-c-ran-unseeded-for-the-project-s-27557c
status: done
topic_tags:
- medgemma
- cross-modal
- baseline
- control
updated_at: '2026-08-12T00:11:10+00:00'
---

## Prompt
CONTEXT YOU NEED, RESTATED IN FULL.
Phase C of optiHealth-EiV ran with NO seeding at all until 2026-08-09 (fix commit 0b5aff2):
src/training/pipeline.py::run_lora_proxy_adaptation had zero manual_seed / np.random.seed /
random.seed / torch.Generator across 1156 lines, while Phase A and Phase B both seeded at 42.
Consequence: two adapters with BYTE-IDENTICAL training manifests (v4w and v5c -- same 6-dataset
filter, same profile chain, same sensor_config, same tensor_path; only final_loss differed,
1.2183 vs 0.5821) measured 2.673 bpm apart on emitted hr_peak skill and 0.712 bpm apart on hr_raw
skill, on 48 identical eval samples. Every effect this project has claimed is SMALLER than that:
the H-1 dryad hr_acc delta was 0.0078 (one sample in 128) and the v4c/v4w allowlist gate was
+0.0249 against a +0.03 bar.

TASK. Measure the Phase-C seed band, so future arms have a minimum detectable effect.
Run scratch/run_seed_band.sh: seeds 43 and 44 via profiles lora_proxy_12ch_wl_s43 and
lora_proxy_12ch_wl_s44 (seed 42 is the EXISTING outputs/checkpoints/v5c/phase_c adapter -- reuse it,
do not retrain). All three initialise from outputs/checkpoints/v5c/phase_b so the ONLY variable is
the Phase-C seed. Per seed: Phase C 2600 steps -> probe p2,p3,p4,p6 at 512 samples against
data/eval_v3_1min -> scratch/emission_diversity_audit.py at n=48 with rng seed 0. ~2.5 h total.

WHAT TO REPORT. Per seed, not just the aggregate: emitted hr_peak skill (bpm vs always-predict-
median), hr_raw skill, distinct emitted hr_peak values, top-1 mass, and p4 hr_acc WITH its
denominator and the majority baseline on the identical scored subset. Then the RANGE of each.
Seed 42 reference values already measured: hr_acc 0.5234 = 67/128 vs majority 0.4453; hr_peak skill
-3.260 bpm, 19 distinct values, top-1 mass 0.271; hr_raw skill +1.618 bpm.

THRESHOLDS PREREGISTERED IN exp-seed-band-20260809. PRIMARY: range of hr_peak skill > 1.0 bpm.
SECONDARY: range of p4 hr_acc > 0.03. A SMALL band would falsify the prediction and would be GOOD
NEWS (single-seed arms more trustworthy than feared); in that case the v4w/v5c gap needs another
explanation, the leading candidate being that data/canonical/X_canonical.npy was rewritten at
16:36:05 INSIDE v4c's Phase A window (15:40:10-16:45:15), giving that A/B two variables.

DO NOT promote the best-performing seed as a result. This measures a DISTRIBUTION; selecting its
maximum and reporting it as an arm is exactly the error that produced the 'v4w is our best run'
claim. Close exp-seed-band-20260809 with the three gate outcomes recorded.

AFTERWARDS the queue is RE-DERIVED, not resumed: every remaining plan item must have a predicted
effect exceeding the measured band before it is worth GPU. Two NEW targets were promoted by this
week's forensics and are not in the original plan: (1) the emission STOPPING defect -- v5d re-emitted
tail slots until max_new_tokens truncated the JSON on 24% of samples, invisible to hr_acc;
(2) min_hr_coverage=0.05 is mis-scaled for the 1-minute grid (passes 99.4% of the corpus; lifesnaps'
median hr coverage is 0.9854, not the 0.15 its in-config comment assumes) and min_coverage=0.25 is
near-vacuous (hard floor 0.3333 from the four always-present time encodings). Both are CPU-checkable
before any GPU is spent.

## Preconditions
Phase C seeding fix (0b5aff2) must be in HEAD, and the A/A determinism smoke must have passed (it did: identical final_loss 5.125603199005127 and identical adapter weight sha256 564ce044b0911435f8467125 across two runs of the same profile+seed). All arms must carry pinned hr_buckets with IDENTICAL cut points, or the accuracies are not comparable.

## Links
Queued for problem [[phase-c-ran-unseeded-for-the-project-s-27557c]].
Run as [[with-phase-c-now-seeded-0b5aff2-three-fae38a]].
Intended gates: `gate-seed-band-measured`, `gate-band-vs-legacy-spread`, `gate-no-post-hoc-best-seed`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#medgemma #cross-modal #baseline #control
