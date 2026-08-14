---
created_at: '2026-08-12T08:57:29+00:00'
id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
paper_refs: []
problem_tags: []
status: open
title: 'Phase-C arms fail on the deployment distribution (optihealth_supabase): hr_raw
  floor atom at 30.0 plus unlearned sparsity handling'
topic_tags:
- deployment-distribution
- optihealth
- collapse
- baseline
updated_at: '2026-08-12T08:57:29+00:00'
---

## Description
MEASURED 2026-08-12 (commit 0940a9e, outputs/probes/{v4w,v5c,v5d}_emissions_supabase.json, 200 identical-draw supabase rows, production prompt, greedy decode).

THE FAILURE. On the production annotate-path population (optihealth_supabase, mean HR coverage 0.789, truth HR mean 58.3 bpm, never <45 bpm):
- v4w hr_raw MAE 16.72 (trivial supabase-median baseline 4.02, skill -12.70), emits exactly "30" on 119/200
- v5c MAE 20.83 (skill -16.81), "30" on 150/200
- v5d MAE 25.05 (skill -21.03), "30" on 179/200
Arm ranking v4w > v5c > v5d is the REVERSE of the dense 128-row draw (where v5c beat v4w 108/128, p<1e-4). Anton's field annotations detected this before the instrument did, because the eval draw was 97% lifesnaps+pmdata (84/40/3 of 128) while supabase is 223/6151 eval rows.

ROOT CAUSE 1 (proven mechanism, fix in flight): the hr-augmentation clamp in src/training/datasets.py mapped the below-window tail of the uniform target_peak draw onto lo_peak EXACTLY, so the augmented daily mean landed at exactly min_valid_hr_bpm=30.0 on ~36.5% of augmented samples => ~14.6% of ALL hr_raw LM targets were the literal string "30.0" (scratch/floor_atom_estimate.py; prob=0.40, range 40-182.72). The supabase days are the lowest-HR sparsest days, nearest the attractor. Conditional on ESCAPING the floor, the arms read supabase fine: v4w n=81 MAE 4.20 corr 0.839; v5c n=50 MAE 3.65 (beats trivial 4.39); v5d n=21 MAE 6.26.

ROOT CAUSE 2 (separate defect, verified on a different regime): coverage dilution. Encoder zero-fills masked cells in normalized space (zero = population median 71.8), so a day with HR coverage c pools to 71.8 + c*(truth-71.8). Verified on wearable_exam_stress sample 10313: coverage 0.129, predicted 77.7, emitted 82.0 (~91% of the 36-bpm error). REFUTED as the supabase mechanism: regression of signed error on (cov-1)*(truth-med) gives slope -1.5/-0.47/+0.42 vs predicted 1.0, R2<=0.11. Two defects, not one.

ALSO OBSERVED on production rows: state slot is a near-constant prior with arm-dependent polarity (v5c "stress" 139/200, v4w "neutral" 198/200, v5d "flow" 65/200); both arms confabulate sleep/rr/temp slots on days where those channels are absent (though sparse days can drop schema slots entirely -- conditional slot emission partially works).

GOAL: restore deployment-distribution hr_raw accuracy to v4w's level (MAE <=6.14, its dense-draw level) and better, with floor-emission rate <10%, without regressing the dense population. The standing eval defect (draws must stratify or restrict to supabase) is part of this problem.

#deployment-distribution #optihealth #collapse #baseline

## EVIDENCE STATE 2026-08-13 (supersedes stale lines above; exp-v7noatom-seed-band-20260813)

ROOT CAUSE 1 (floor atom) is FIXED AND STRUCTURALLY VERIFIED: a71034c truncated-uniform
draw; exact-30 emissions 0/200 on ALL of seeds 42/43/44 of lora_proxy_12ch_wl_noatom
(g1 blocking PASS). v7noatom (seed 42) is the DESIGNATED DEPLOYMENT BASELINE by
structure, not score (docs/BASELINE.md, commit 637a78a).

The GOAL line "restore to v4w's level (MAE <=6.14)" is OBSOLETE: v4w is a dead
recipe (all three mechanistic accounts falsified; annotate-path panel v4w 11.14 vs
v7noatom 7.31, commit 89c7f82). The yardstick is now the v7noatom FAMILY BAND:
seeds 42/43/44 supabase MAE 11.54/15.78/9.49, band 6.29 bpm (pre-fix 11.37),
dense-128 2.89/3.65/3.38, trivial 4.02. Diagnostic eiv_supabase_seed_band_mae_bpm.
Any new arm must beat 9.49 (best baseline seed) at median across >=3 seeds.

ROOT CAUSE 3 (NEW, measured, dominant residual): the post-fix augment law still
injects a SUB-45 BPM POISON TAIL — P(hr_raw target < 45) = 0.0729, almost entirely
augmentation (E[P(aug<45)]=0.1774; natural 0.0032) — while deployment truth is NEVER
<45. Each seed crystallizes an arbitrary sub-45 prototype (42: 38.9x111; 43:
31.1x61+31.3x47; 44: 31.6x62 — 43/44 sit just above the deleted 30 floor) and emits
<45 on 33-60% of deployment rows. Band starvation compounds it: P(target in [45,65])
= 0.2017 vs deployment demand 0.8290 (supply/demand 0.243; scratch/band_supply_demand.py,
commit 744bbd7; forensics commit e8837a8). The seed lottery and the deployment MAE
failure share this root. Attack queued: rb-deployment-band-mass-20260813 (E1) —
remove the sub-45 tail AND add [45,65] mass in _sample_hr_augment_peak, no new atom,
CPU supply-check before GPU, 3 seeds, gates include P(emit<45)<0.10 all seeds and
seed-band-DOWN as the shared-root-cause test.

## EVIDENCE STATE 2026-08-13 (evening) — BASELINE RE-DESIGNATED: v9band04 recipe

- E1b (`exp-e1b-band04-20260813`, `directional_only`): band law at hr_augment_prob
  0.4 → supabase MAE 2.85/3.18/3.45 (ALL seeds beat trivial 4.02, project first),
  seed band 0.60 bpm, floor and P(emit<45) = 0.000. Dense 3.08/4.73/2.80 — the
  every-seed recovery gate failed on seed 43 only. E1's prob-0.8 recipe is
  dominated on every axis and retired.
- **Baseline is now the v9band04 RECIPE, named instantiation seed 42** (supabase
  2.85, dense 3.08). v7noatom superseded. `docs/BASELINE.md` rewritten (0eec98f).
- Seed policy: band 0.60 → single-seed comparisons valid for effects >~1.5 bpm;
  sub-bpm claims need seeds or paired sign tests.
- E2 (EOS supervision) ABANDONED — premise false: EOS already supervised on every
  json_slots target (gemma_runtime.py:610-614); v9band04 stops itself (984/984
  parses, 0 loops, max 96/128 tokens). Rule-out filed.
- Batched capture generate() ruled out as a free speedup: 31/32 texts differ at
  batch 8 (cuBLAS reduction order flips argmax on lattice near-ties). Quotable
  captures = batch_size 1. Side finding: emissions sit on near-ties between
  quantized prototypes — independent crystallization evidence.
- Remaining frontier on THIS problem: 2.85 vs trivial 4.02 leaves ~1.2 bpm of
  headroom on hr_raw; the bigger open surfaces are the categorical slots (state
  never read; hrv), encoder excursion representation (hr_peak skill still
  negative), and `rb-dynamic-window-20260812`.

## EVIDENCE 2026-08-13 (late) — SLOT SCORING on existing v9band04 captures (CPU-only)

Scorer `scratch/score_slots_deployment.py` over all 6 captures (984 emissions, 0 new GPU).

**STATE slot: NOT a constant prior — it is read, but through hr level.**
- Dense-128: real variation, entropy ~1.0 bit (s42 neutral 72 / stress 54 / flow 2;
  s43 stress 88 / neutral 39; s44 67/61). On supabase: 94-99% neutral.
- state co-varies with the model's OWN hr_raw emission (flow 69-75 > stress 63-77 >
  neutral 57-58 on supabase) -> state ~ f(hr level). Supabase hr sits in 45-65 ->
  everything maps to neutral. Consistent with double-dissociation finding: each arm
  is a threshold comparator under its own training rule. No supabase state ground
  truth exists (labels: {}) so accuracy is unmeasurable; only the mechanism is.
**HRV_RAW: exact replay of the pre-band-law hr_raw pathology (all 3 seeds):**
- Dense: corr ~0.60, skill +2.8..+3.5 ms vs trivial 18.22, std_ratio 0.75-0.92 —
  genuinely read.
- Supabase: attractor 69.1 ms on 126-168/197 rows, corr ~0, skill -0.19..-0.50 vs
  trivial 8.31 (never beats), std_ratio 0.02-0.29. Truth: median 72.0, std 10.6.
- Denominator: 197/200 rows have hrv truth; dense only 62/128 (coverage).
- Hypothesis filed: `hrv-raw-fails-on-deployment-for-the-same-reason-hr-raw-did-pre-...`
  (hrv band law = E1b analog). This is the top GPU candidate on this problem.
**RR / TEMP: constant "normal" on supabase (top1 1.00); mild variation on dense
only (temp low@0.72 on s43 dense). No deployment truth channel — unscoreable.**

## 2026-08-13 (late) — E3 refuted; state & peak levers ruled out; E4 preregistered

**E3 hrv band law ([[exp-e3-hrv-band-20260813]], v10hrv): ALL 4 GATES FAIL.** supa hrv MAE 12.64 (trivial 8.31, baseline 8.62); dense hrv skill +3.52→−11.93 (law destroyed genuine dense reading, emits ~84 ms vs truth p50 45.3); BLOCKING g3 fail: hr axes damaged (supa 2.85→3.67, dense 3.08→5.44); floor 0 preserved. Post-mortem (`scratch/e3_postmortem.py`): realized marginal matches design (p50 63.2 vs 63.8, clamp 11%) — the law executed correctly and still failed. **Law: target reshaping requires a strongly-read channel** (hr corr 0.9 → worked; hrv corr 0.60 → decoupled target from the model's correlates).

**Rule-outs filed (CPU, existing captures):**
- state slot carries no affect-label signal: perm p≥0.29 all seeds/labels; corr(label, emit_hr)≤0.09 bounds out any hr-thermometer law. Polarity is a seed lottery (s42 31n/3s, s43 26s/8n, s44 30n/4s on identical rows).
- hr_peak target-law lever dead: dense corr 0.17–0.35 (vs hr 0.9) — representation never carried the peak; nothing for a law to unlock.

**Next: [[e4-encoder-excursion-avgmax-20260813]]** — avg+max concat pooling; blocking CPU ridge gate (increment ≥0.25) before any GPU; full A→B→C retrain. Baseline unchanged: `lora_proxy_12ch_wl_band04`.
