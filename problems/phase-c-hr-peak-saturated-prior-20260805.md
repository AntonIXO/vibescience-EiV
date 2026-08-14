---
created_at: '2026-08-05T08:58:21+00:00'
former_ids: []
id: phase-c-hr-peak-saturated-prior-20260805
paper_refs: []
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: open
title: 'Phase C: bucket/hr_peak conditioning is ~12x too weak to beat the template
  prior (not a dead slot)'
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
updated_at: '2026-08-09T06:40:00+00:00'
updates: []
---

## Description
=== TWELFTH REVISION 2026-08-09. H-1 (admit dryad_ifh_affect to Phase C) RUN AND CLOSED.
PREREGISTERED GATE FAILED. Two arms, one variable, IDENTICAL PINNED CUT POINTS -- the first
genuinely comparable A/B in this problem's history. ===

--- SETUP ---
  CONTROL      v5c : phase_a_12ch_1min_wl       -> phase_b_12ch_wl       -> lora_proxy_12ch_wl
  INTERVENTION v5d : phase_a_12ch_1min_wl_dryad -> phase_b_12ch_wl_dryad -> lora_proxy_12ch_wl_dryad
Preflight ASSERTED a single variable before launch (scratch/h1_preflight_filter.py): 4097 rows only
in the intervention, all dryad_ifh_affect; ZERO rows only in the control. n 6151->10248, subjects
216->239, days with ANY hrv 0.4926->0.6967. All six stages RC=0 per arm.
BOTH manifests carry hr_buckets tachycardic=157.71853625752556 elevated=126.9999977820953 with the
same source_normalization_sha256, and the probe logged "cut points PINNED in adapter manifest ...
(reproducible)" on both. Previous A/Bs in this problem could not make that statement.

--- PRIMARY GATE: hrv_acc DENOMINATOR >= 60. FAILED, AND IN THE WRONG DIRECTION ---
  v5c control : hrv_total = 60   hrv_acc 0.6500? NO -- 0.4000 = 24/60, majority 0.4000
  v5d +dryad  : hrv_total = 20   hrv_acc 0.6500 = 13/20,             majority 0.6500
The denominator went DOWN 60 -> 20. And on BOTH arms hrv_acc EQUALS its own majority baseline to
full float precision -- skill exactly 0.0000, the same constant-emission signature v4c showed at
16/29. Tripling the HRV-bearing training pool did not produce HRV emission skill.
NOTE the control already reached the >=60 gate from the EVAL REBUILD ALONE (v4c 29, v4w 33, v5c 60),
so the gate was satisfied by instrument repair, not by dryad.

--- hr_acc: NO DIFFERENCE ---
  v5c 0.5234 = 67/128, majority 0.4453, skill +0.0781, z=+1.78 (inside the constant-predictor band)
  v5d 0.5156 = 66/128, majority 0.4453, skill +0.0703, z=+1.60 (inside the band)
ONE sample apart. Two-proportion z = +0.125, p ~ 0.90; the gap is far inside the 0.066 seed spread.

--- THE REAL FINDING, WHICH hr_acc COMPLETELY HID: DRYAD COLLAPSED hr_peak EMISSION ---
Forensic dump of raw generations, SAME 16 eval indices, SAME seed, production prompt
(prompts/json_slots_prompt.md) through the exact bucket_parser generation path
(scratch/h1_forensics_parse_failure.py):
  v5c hr_peak: 10 distinct values over 16 samples -- 101.0, 111.9, 115.0, 123.1, 131.0, 131.5,
               133.0, 133.3, 134.1, 160.0
  v5d hr_peak:  4 distinct values over 16 samples -- {100, 110, 130, 160}, with 130.0 on 11/16
Adding dryad COARSENED the emitted peak onto a round-number lattice. hr_acc is blind to this because
the bucket rule only asks which side of 127.00 / 157.72 the value falls on, and 130.0 sits in the
same bucket as 133.3. A four-value lattice and a ten-value distribution score identically.
STANDING CONSEQUENCE: hr_acc must never be the only emission metric reported. Pair it with the
distinct-value count / std ratio of the emitted hr_peak, which is the diagnostic exp-e34 established
and which this arm would otherwise have passed silently.

--- PARSE FAILURE: 0.0000 -> 0.2422 (31/128) ---
Mechanism read directly from the text: the model does not stop. It re-emits tail slots until
max_new_tokens=128 truncates it mid-string, leaving the JSON unterminated so extract_json returns
None. Observed repeated-key runs like "state_neutral_neutral" x12 and
"rr":"normal","temp":"low","state":"flow" cycling three times in one emission.
Because hr_* keys are rendered FIRST they always survive, while hrv_* sits after hr_peak and is the
first casualty -- which is the mechanical cause of the 60 -> 20 hrv_total collapse, NOT a shortage
of HRV data reaching the probe.
Corroborated by p5: hr_recall_at_1 0.5781 -> 0.0938 (6.2x collapse) while hr_recall_at_3 is
IDENTICAL at 0.7891 on both arms.
On the 16-sample forensic draw the rates were v5c 0/16 and v5d 1/16, i.e. the effect is real but the
128-sample probe figure (0.2422) is the better estimate.

--- ENCODER SIDE: DRYAD TRADES PEAK FOR LEVEL ---
Subject-grouped, n_subjects=131, repaired eval, always-predict-median comparator:
                     hr_mean R2   hr skill bpm   excursion increment R2   hr_peak skill bpm
  v5c phase_a          0.9075        +5.187            0.3621                  +3.914
  v5c phase_b          0.9456        +5.629            0.4139                  +4.375
  v5d phase_a          0.9301        +5.340            0.2493                  +2.495
  v5d phase_b          0.9491        +5.658            0.2906                  +3.080
Level-only control 0.0156 on every row. So dryad IMPROVES hr_mean slightly (R2 +0.0034 at phase B,
inside the 0.066 seed band -> secondary gate PASSES) while COSTING 0.12 of peak-excursion increment
R2 and ~1.3 bpm of hr_peak skill at BOTH stages. That is consistent and directional, not noise-shaped.
MECHANISM HYPOTHESIS, NOT YET TESTED: 63.5% of dryad's present HR cells are HELD (carried forward)
and its density is 0.402. Carried cells flatten within-window variation, which is precisely the
excursion signal. min_hr_coverage currently counts carried values as coverage.

--- VERDICT AND WHAT IT COSTS THE PLAN ---
H-1 REFUTED as an HRV-emission lever. Do not re-run "add dryad to Phase C" as-is.
The measurement was still worth the 4.5 GPU-hours: it produced the first reproducible-by-construction
A/B, and it caught a mode collapse that the project's headline emission metric cannot see.
DO NOT conclude "dryad is bad data". Its hr_peak labels are measured excursions (0/600 held peak
cells, median(peak|all present) == median(peak|unheld) == 124.97). What is not yet separated is
whether the harm comes from its 63.5% HELD cells or from its different peak distribution. The cheap
next test is a dryad arm gated on MEASURED coverage only (exclude carried cells from
min_hr_coverage), which isolates the hold fraction from the dataset identity.

--- SCOPE LIMITS ---
ONE seed per arm; documented seed spread for these profiles is 0.066. The hr_acc null is inside that
band and is therefore "no evidence of difference", never "proven equivalent". The hr_peak collapse
(10 -> 4 distinct values) and the parse-failure jump are much larger than the seed band but rest on
n=16 forensic samples and n=128 probe samples respectively; both should be replicated on a second
seed before the mechanism is treated as established.

#optihealth-eiv #conditioning-collapse #cross-modal

=== ELEVENTH REVISION 2026-08-09. INSTRUMENT REPAIR (P0.1-P0.5) AND RE-SCORE OF THE v4c/v4w A/B.
UNPREREGISTERED: problem evidence, NOT an experiment. Carries NO calibration weight. ===

--- 0. THE SHORT VERSION ---
Every Phase-C emission number on disk was measured through three broken instruments. After repairing
them and RE-SCORING THE SAME TWO ADAPTERS (no retraining, same 512-sample draw), the A/B's headline
metric moves by up to -0.22 absolute and the conclusion changes. NEITHER ARM'S EMISSION BEATS ITS OWN
MAJORITY BASELINE.

--- 1. THREE PROVENANCE BREAKS, ALL MEASURED READ-ONLY BEFORE ANY CODE CHANGED ---

(a) THE EVAL BUNDLE ADDRESSED DAYS THAT NO LONGER EXISTED.
    data/eval_v3_1min was carved from data/canonical at 15:28:38; the corpus was re-merged at 16:36:05.
    build_eval_v3_1min.py COPIED the source merge_manifest.json verbatim, so the eval bundle kept
    advertising a matching git_sha and normalization sha while having diverged. Following source_indices
    into the live corpus: 5/200 rows still matched on (subject_id, start_time), and the median max
    |raw diff| over commonly-measured cells was 67.47 bpm.
    The bundle also carried NO M_held, and src/training/datasets.py:1764 silently degrades to "no carry
    flags" when it is absent, so carried-forward cells counted as measurements.
    Evidence: scratch/p0_2_preflight_eval_staleness.py

(b) THE LABEL AXIS IS A MOVING QUANTILE.
    src/training/corpus_stats.py::hr_aux_stats sets bucket_tachycardic = quantile(peaks, 0.80) and
    bucket_elevated = quantile(peaks, 0.45), cached in hr_aux_stats.json BESIDE THE TENSOR.
    logs/hdataset_ab.log recorded tachycardic>=143 elevated>=109 at EVERY stage of both arms.
    Recomputing on the live corpus gives 157.72/127.00. 143/109 is p66.3/p19.0 of the live peak
    distribution. A moving cut point moves the label axis AND the majority baseline TOGETHER.
    Evidence: scratch/plan_audit_cutpoint_mechanism_20260808.py

(c) THE CORPUS WAS REPLACED MID-RUN.
    X_canonical.npy mtime 16:36:05 falls INSIDE the v4c arm's Phase A window (15:40:10 -> 16:45:15).
    Whether v4c Phase A read the old or the new tensor is not recoverable from the log, so the A/B may
    have had TWO variables rather than one. Separately, merge_manifest.json records git_sha
    a85d085d40ec7a880942c5052323cf8332143b6e, which DOES NOT RESOLVE in this repo after git fetch --all.
    The live corpus cannot be reproduced from any committed tree.

--- 2. WHY THE SHIPPED hr_acc WAS UNINTERPRETABLE ---
v4w p4 hr_acc = 0.6016 = 77/128. Priced against the majority class:
    cut points 143/109   (what the probe used) : majority 0.5840 -> skill +0.0176, z=+0.40
    cut points 157.7/127 (live corpus)         : majority 0.4493 -> skill +0.1523, z=+3.46
    cut points 120/100   (clinical)            : majority 0.7507 -> skill -0.1491, z=-3.90
Constant-predictor 95% band at p=0.584, n=128 is [0.4986, 0.6694]. 0.6016 SITS INSIDE IT.
"v4w is the best Phase C on record" was never supported by its own headline metric.

--- 3. THE RE-SCORE. SAME ADAPTERS, SAME DRAW, REPAIRED INSTRUMENT ---
src.cli probe --probes p2,p3,p4,p6 against the REBUILT data/eval_v3_1min (row identity 0/200 -> 200/200,
max raw diff 0.0000 bpm, norm sha 6f5de9c1b55b on both sides). Cut points in force 157.72/127.00,
printed by the probe and flagged NOT REPRODUCIBLE (both adapters predate cut-point pinning).

                 SHIPPED (stale eval, 143/109)   RESCORED (repaired, 157.7/127)
  v4c hr_acc                    0.5703                        0.3516  = 45/128
  v4w hr_acc                    0.6016                        0.4297  = 55/128
  v4w - v4c                    +0.0313                       +0.0781

Against the majority baseline ON THE IDENTICAL SCORED SUBSET (hr majority 0.4453, n=128):
  v4c hr    skill -0.0938  z=-2.13  BELOW the constant-predictor band [0.359, 0.531]
  v4w hr    skill -0.0156  z=-0.36  INSIDE the band
  v4c hrv   0.5517 = 16/29, majority 0.5517 -> skill EXACTLY 0.0000 (emitting the majority bucket)
  v4w hrv   0.2121 =  7/33, majority 0.7879 -> skill -0.5758, z=-8.09, BELOW band
  v4c state 0.5781 = 74/128, majority 0.5156 -> skill +0.0625, z=+1.41, INSIDE band
  v4w state 0.4531 = 58/128, majority 0.5156 -> skill -0.0625, z=-1.41, INSIDE band
  deep_sleep: NaN, total=0 on BOTH arms -- the slot is never rendered on this population.

v4w vs v4c two-proportion z = +1.28 (p~0.20). The +0.0781 gap exceeds the documented 0.066 seed spread
but is NOT significant at n=128, and both arms sit at or below their own majority baseline.
CORRECT READING: the whitelist bought no measurable emission skill, and no measurable harm either. The
A/B cannot resolve the difference at this n. Any future claim needs more samples or more seeds.

--- 4. p3 HAS NEVER RUN, AND THE CAUSE IS ITS OWN THRESHOLD ---
The rescore reported swap_attempted=1 on a 5955-row pool. p3 paired a day with day-MEAN HR < 70 against
one with day-MEAN > 110, taking min(len(low), len(high), 32) pairs. A day-mean runs over 1440 one-minute
epochs INCLUDING SLEEP, so >110 bpm is pathological rather than "a high day".
MEASURED on eval_v3_1min (n=5955): day-mean p99 = 92.8, max = 140.4, and EXACTLY ONE day clears 110,
against 1926 below 70. Hence pairs=1, swap_total=0, and the old code reported that as
swap_attribution_acc = 0.0.
EVERY "p3 shows no attribution" reading in this project's history is that threshold, not the model --
including the rule-out filed against the Stage-C seed tripwires, which should be re-read in this light.
FIXED (commit d6927e3): p3 now pairs on the PEAK axis, the quantity hr_label_bucket actually grades.
peak<120 selects 1477 days and peak>160 selects 816 on the same pool.
Evidence: scratch/p0_3_why_p3_never_runs.py. NOT YET RE-RUN with the new gates -- that is the next
measurement, and it will be the first causal read on conditioning this project has ever had.

--- 5. WHAT WAS REPAIRED (commits 84b145f, 7217f93, d6927e3) ---
  - eval bundle rebuilt from the live corpus; writes its own eval_manifest.json carrying the source
    tensor sha256/mtime/shape and the per-dataset exclusion. Now carries M_held.
  - cli_probe RAISES on a normalization-space mismatch (train sha vs eval sha). A warning was not
    enough: this went unnoticed for a full A/B cycle.
  - HR bucket cut points are PINNED into the adapter manifest at save time (hr_buckets block with source
    tensor shape, n_samples, norm sha, derivation string); the probe grades from the adapter, which is
    immutable, rather than the corpus, which is not. A save/load round-trip test caught load_manifest
    silently DROPPING the field while all four unit tests passed.
  - p3 and p6 now return None plus *_measured, *_unmeasured_reason and denominators instead of 0.0.
    A genuinely measured 0.0 is preserved.
  - Subject-grouped train/val/test splits now exist (data/canonical/split_subjects.json: 252/35/37
    subjects, 7887/1187/1270 rows, tachycardic marginal 0.665/0.655/0.655). There was NO validation
    split anywhere in this project before this -- no val key in any of 140 W&B runs. This closes the
    TENTH REVISION's caveat 4 blocker for decomposing e40's 49% in-domain-to-held-out retention.
    NOTE THE ACHIEVED BOUND, not a target hit: subjects are atomic and the largest contributes 355 of
    10344 rows (34.3% of a 10% split), so exact 80/10/10 with matched marginals is unreachable. Max row
    fraction error 0.0375, max bucket marginal error 0.0664.

--- 6. WHAT THIS DOES AND DOES NOT CHANGE ---
DOES NOT TOUCH the encoder-side results. Those are measured on the prefix, not on emission, and do not
route through the bucket scorer: p1 subject-grouped hr_mean R2 0.9597, MAE 1.178 vs trivial 6.998
(+5.82 bpm skill), eiv_peak_excursion_increment_r2 0.4350 against a level-only control of 0.0040. The
encoder half remains healthy and v4w genuinely beat v4c on REPRESENTATION.
DOES invalidate every historical p4/p3/p6 number measured against the old eval bundle.
The failure is confined to EMISSION, and the instrument that measures emission was broken.

--- 7. OWN-ERROR DISCLOSURE ---
Running the cut-point audit CALLED hr_aux_stats, which REWRITES hr_aux_stats.json as a side effect. The
cache that served 143/109 to the A/B was overwritten (mtime now 2026-08-08 22:39, contents
157.71853625752556 / 126.9999977820953) and cannot be recovered. That is precisely why cut points are now
pinned into the adapter rather than left in a rebuildable side-car.

--- 8. METHOD NOTE FOR THE VAULT ---
This is the FIFTH instrument in this project caught reporting a confident number it never measured:
p3 swap 0.0; p6 shuffle_gap 0.0; the p4 scorer grading against the wrong bucket definition (fixed in
bba9bcf); W&B hr_r2 logging a frozen random head at ~-47 while the same checkpoint probed at 0.9713; and
now the cut points. The common shape is a divide-by-possibly-zero with an `else 0.0`, or a constant read
from a mutable location. Before any comparative claim, verify the instrument CAN produce the number, and
quote every accuracy with its denominator, its majority baseline, and the convention in force -- in the
same sentence.


=== TENTH REVISION 2026-08-07. THE LOSS BALANCE WAS THE BOTTLENECK. exp-e40 (prereg 2/2, verdict supports)
plus exp-e41 held-out audit. THIS IS THE FIRST INTERVENTION IN THIS PROBLEM THAT MOVED THE PRODUCT METRIC. ===

INTERVENTION: profile lora_proxy_12ch_clmbal, IDENTICAL to lora_proxy_12ch except probe_weight 1.0 -> 0.0,
hr_regression_weight 0.1 -> 0.0, lora_warmup_steps_aux_only 100 -> 0. Same 2600 steps, same Phase B checkpoint,
same sampler, same data, one seed. Verified pure CLM at step 0 (loss=1.8532 == llm_loss=1.8532; e33 step 0 was
loss ~988 against llm_loss ~2). Adapter outputs/adapters/medgemma_12ch_e40_clmbal. Train wall time 37 min.

--- hr_raw, strict-clean pmdata n=120, mean-decode, donor-controlled ---
                          e33 (CLM 4.8%)    e40 (CLM 100%)
  real skill vs median        +1.84             +5.03
  donor skill                 +0.11             -2.11
  PREFIX-ATTRIBUTABLE GAP     +1.73             +7.14      (4.1x)
  corr                        +0.724            +0.977     (donor +0.068 both)

--- hr_peak, strict-clean n=100 ---
  PREFIX GAP                  +1.36             +7.28      (5.4x)
  absolute skill              -0.45             +0.29      <- first time hr_peak beats a constant here
  corr                        +0.194            +0.408     (donor -0.075)
  pmf normalised entropy      0.774             0.500
  pmf effective support       69.4/240          15.5/240
  pmf top-1 mass              0.055             0.249

--- exp-e41, HELD-OUT COHORT THE ADAPTER NEVER SAW (big_ideas_glycemic + mmash + optihealth_supabase, n=95,
    always-predict-median MAE 8.53, true hr_raw 66.7 +- 11.9) ---
                          e33               e40
  real skill              -3.823            +3.652
  donor skill             -0.470            +0.155
  PREFIX GAP              -3.353            +3.497
  corr                    -0.276            +0.871
  donor corr              +0.076            +0.233
THE OLD MODEL WAS ANTI-CORRELATED OUT OF DOMAIN (-0.276) AND ITS REAL PREFIX WAS WORSE THAN A DONOR'S
(gap -3.35). The new model generalises to three datasets it never trained on at corr +0.871. A memorisation
story cannot produce that, which is what this audit was run to test. Report as a sign change, not a ratio.

--- WHAT THIS SETTLES ---
1. The readout was never the binding constraint. exp-e36 measured a ridge ceiling of +6.11 bpm at the hr_raw
   emitting token while the head realised +1.73 (28%); after rebalancing, the head realises +7.14 IN-DOMAIN --
   at or above that old ceiling. Nothing about lm_head, tying, geometry, pooling, slots or attention changed.
2. exp-e37's decode-rule finding is now explained. e33 hr_raw spanned +0.85 (argmax) to +1.86 (median), a 2.2x
   swing; e40 spans 0.414 bpm across ALL NINE decode x temperature cells. The decode sensitivity was a SYMPTOM
   of a diffuse conditional produced by a starved objective, not an independent defect. Fix the objective and
   the estimator question dissolves.
3. exp-e39 correctly redirected the effort: the within-CLM token balance was already fine (value tokens carry
   96.2% of CE mass, 65.9% of prefix gradient), so completion-only masking was the wrong lever and the
   BETWEEN-objective balance was the right one.
4. RETAINED CAVEAT: in-domain gap +7.14 vs held-out +3.50 = 49% retention. That drop mixes memorisation of the
   ~53% of the corpus seen once with genuine domain shift, and this project still has no within-domain
   validation split to separate them. Build one before quoting +7.14 as the system's number; +3.50 held-out is
   the defensible figure.
5. hr_peak is still much harder than hr_raw in ABSOLUTE terms (+0.29 vs +5.03) even though both gained ~5x in
   prefix-attributable terms. The order-statistic asymmetry survives this intervention.
6. ONE SEED, screening only. run_12ch_pipeline.sh's ~50% bistability figure was measured at
   hr_regression_weight=0.002; e40 sets it to 0.0, so the basin rate at this configuration is UNKNOWN and
   e40 could be a lucky draw. Confirm on a second seed before treating the magnitude as established.

--- PROCESS DEFECT FOUND AND FIXED (recorded because it nearly produced a false result) ---
scratch/run_e40.sh wrote its eval to a FIXED filename and then `mv`-ed it to a result name. The hr_peak eval was
killed at 40/100 by an unrelated interruption; the shell continued and promoted the PREVIOUS adapter's stale
file as the e40 result. It was caught only because the numbers were bit-identical to e33 (entropy 0.774,
support 69.4, top-1 0.0552, gap +1.36) -- an impossible coincidence across adapters. The JSON's own `adapter`
field then confirmed it read medgemma_12ch_e33.seed_1. FIX now in scratch/run_e40_peak.sh and run_e41.sh:
`rm -f` the target before the run, check the process return code, and ASSERT the adapter field inside the JSON
before promoting. STANDING RULE: every result artifact must carry its own provenance, and a runner must never
promote a fixed-name file it did not just verify.

=== NINTH REVISION 2026-08-06. hr_peak RE-MEASURED UNDER EXPECTATION DECODING (exp-e38). THE ASYMMETRY IS
REAL BUT THE MAGNITUDE OF THE DEFICIT WAS AN ARTIFACT OF TOKEN-LEVEL GREEDY. Prereg 1/3, verdict supports. ===

METHOD FIX THAT THE n=6 SMOKE CAUGHT: the first E38 build teacher-forced the TRUE target as context. Its
DONOR arm scored corr +0.923 / skill +4.30 -- the context contains the true heart_rate_bucket (which encodes
peak>=120 directly) and the true hr_raw, so the apparatus was supplying the answer. This is the exp-e24 trap
recurring. Rebuilt with FREE-RUNNING context: the model generates its own JSON prefix up to '"hr_peak":"',
then the grid is scored. Donor null then behaves correctly (rank corr NEGATIVE, -0.15 to -0.17).

MEASURED: exact grid pmf, 240 candidates 70.0-189.5 step 0.5, quote-terminated to remove the 4- vs 5-character
length bias, free-running context, strict-clean n=100, e33.seed_1. True hr_peak 122.9 +- 21.2, always-median 17.54.
  decode          MAE    skill    corr    real-minus-donor
  argmax        17.63   -0.09   +0.107      +0.44
  mean  T=0.5   17.98   -0.45   +0.194      +1.36
  median T=0.5  17.81   -0.28   +0.173      +1.37
  mean  T=1.0   19.30   -1.76   +0.215      +1.57
  mean  T=2.0   22.00   -4.47   +0.207      +1.36
  DONOR argmax  18.07   -0.54   +0.022
  DONOR mean    19.34   -1.80   -0.173

1. TOKEN-LEVEL GREEDY UNDERSTATED THE HEAD BY ~1.8 bpm. exp-e34 reported hr_peak skill -1.91 (n=312,
   token-by-token greedy). Sequence-level argmax over the same target and adapter gives -0.09, and the donor
   gap replicates (+0.44 vs +0.48). Greedy is not the sequence MAP. Every native-emission number in exp-e34/e35
   inherits this and should be read as a lower bound.
2. THE PREFIX DOES CONDITION hr_peak AT EMISSION: +1.36 bpm real-minus-donor under expectation decoding, with
   a donor arm whose rank correlation is NEGATIVE. exp-e34's "the prefix is nearly inert" is too strong.
3. BUT NO DECODE RULE BEATS always-predict-median ON hr_peak. Best is -0.09, i.e. indistinguishable from the
   trivial predictor. Unlike hr_raw, expectation decoding does NOT help here and is slightly WORSE than argmax.
4. WHY: THE hr_peak PMF IS DIFFUSE. Normalised entropy 0.774, effective support 69.4 of 240 grid points, top-1
   mass 0.055. E[v] is therefore dragged toward the grid centre (129.8), which is also why the mean arm degrades
   monotonically with temperature. NEW STANDING CAUTION: when a pmf is near-uniform over the candidate grid,
   E[v] measures the GRID, not the model. Always report pmf entropy / effective support beside any expectation
   decode. hr_raw's pmf is comparatively sharp, which is why expectation decoding worked there.
5. THE REALISATION FRACTIONS, SIDE BY SIDE, DONOR-CORRECTED:
     hr_raw   prefix-attributable +1.73 bpm  /  ridge ceiling at the same token +6.11  =  28%
     hr_peak  prefix-attributable +1.36 bpm  /  ridge ceiling (exp-e28 LIN_SCALAR) +8.22  =  17%
   So the asymmetry is REAL (17% vs 28%, and corr +0.22 vs +0.80) but it is a difference of DEGREE, not the
   "hr_raw works, hr_peak is dead" dichotomy this problem was built on. Both slots under-use an available
   signal by 3.5-6x; hr_peak simply starts from a larger ceiling and realises less of it.

CONSEQUENCE: the order-statistic explanation for the asymmetry is not refuted, but it now has to explain a
17%-vs-28% realisation gap rather than a sign flip. The dominant term in BOTH slots is the shared readout
under-use, which is the same target rb-e3 / completion-only loss / HL-Gauss bucket emission all attack.

=== EIGHTH REVISION 2026-08-06. THE DECODE RULE WAS A REAL CONFOUND IN MY OWN E34/E35 NUMBERS.
exp-e37 (E-0, literature-driven: Lukasik arXiv:2403.04182, OmniPred arXiv:2402.14547). Prereg 1/3. ===

MY E34/E35 MEASUREMENTS USED do_sample=False, i.e. THE MODE. Greedy is Bayes-optimal for exact-match loss and
for nothing else; for MAE the optimal decode is the MEDIAN of the induced distribution. I measured the head's
argmax and reported it as 'the head does not use the signal'. That was under-specified.

MEASURED: exact grid-restricted pmf p(hr_raw | sensors), 110 candidates 45.0-99.5 step 0.5, deterministic
(no sampling), teacher-forced context, strict-clean n=120, always-predict-median MAE 6.59 bpm.
  decode        MAE    skill    corr    std     pred_mean
  argmax       5.74   +0.85   +0.523   6.79      70.3      <- what E34/E35 reported
  mean  T=0.5  4.75   +1.84   +0.724   3.65      70.9
  median T=0.5 4.74   +1.86   +0.693   4.20      70.9
  mean  T=2.0  5.43   +1.16   +0.795   2.04      71.4      <- best correlation
  DONOR argmax 9.06   -2.47   -0.093   6.86      70.3
  DONOR mean   6.48   +0.11   +0.122   2.32      71.2
True hr_raw 70.2 +- 7.9 bpm.

WHAT IS NOW ESTABLISHED
1. EXPECTATION DECODING IS WORTH ~1.0 bpm AND +0.20-0.27 CORRELATION, FOR FREE, NO TRAINING.
2. MY PREDICTED MECHANISM IS REFUTED. I argued a ~13 bpm downward location bias would defeat any decode rule.
   Under teacher-forced context the head is essentially UNBIASED (pred_mean 70.3 vs true 70.2). The 13 bpm
   offset seen in exp-e34 is a property of FREE-RUNNING GENERATION CONTEXT, not of the conditional distribution.
3. THE CONTROL DISCIPLINES THE HEADLINE. The donor arm is centred at 70.3 too, so the good CENTRING is
   context-driven, not prefix-driven. Only the real-minus-donor gap is attributable to the sensor:
   +1.73 bpm under mean decoding (1.84 - 0.11). Quote that, not +1.86.
4. THE GAP SURVIVES, SMALLER AND BETTER MEASURED. exp-e36's ridge on the SAME hidden state at the SAME token
   achieves skill +6.11 bpm (MAE 1.48 vs 7.59). Optimal decoding realises +1.73 of it. So the head recovers
   roughly 28% of what its own final-layer representation supports. 'The head does not use the signal' is
   WRONG AS STATED; 'the head under-uses it by ~3.5x' is what the data support.
5. std IS A BAD COLLAPSE DIAGNOSTIC. std(argmax) 6.79 > std(mean[v]) 2.92 while corr goes 0.523 -> 0.769.
   Dispersion and discrimination move in OPPOSITE directions. Any future 'output std is too low' argument
   must be paired with a correlation and a donor arm.

CONSEQUENCES FOR THE RECORD
* Every native-emission number in exp-e34 and exp-e35 is an ARGMAX number and must be labelled as such. They
  are not wrong, but they are a lower bound on what the head's distribution contains. The exp-e34 verdict
  (hr_peak skill -1.91 bpm, CI excluding zero) should be re-run under expectation decoding before any further
  mechanism work rests on it.
* exp-e35's gain sweep conclusion ('salience is not the binding constraint') is UNAFFECTED in direction -- it
  compared argmax to argmax across alphas -- but its absolute numbers inherit the same caveat.
* The methodological lesson generalises past this project: WHEN THE METRIC IS A DISTANCE (MAE/RMSE), THE DECODE
  RULE IS PART OF THE MODEL. Reporting greedy output against a distance metric measures a different estimator
  than the one the loss implies.

SCOPE LIMITS: teacher-forced context (not free-running), so absolute skills are not comparable to exp-e34's
free-running numbers; grid-restricted to 45.0-99.5 at 0.5 bpm; hr_raw only, one adapter (e33.seed_1), n=120.

=== SEVENTH REVISION 2026-08-06. PROBE INSTRUMENTATION REBUILT AND THE ATTENTION QUESTION SETTLED ON hr_raw.
Unpreregistered instrument work + measurement: problem evidence, NOT an experiment, no calibration weight. ===

--- WHY: the existing p7 could not answer the question it was being used to answer ---
It averaged attention over ALL response positions on num_samples=5 (driver default), and its legacy key
response_to_sensor_attention is a per-COLUMN mean (total/S, S=96) that had been read against a whole-span
uniform baseline. New probe scratch/probe_e36_hr_raw_stages.py measures at the EXACT token whose next token is
the first digit of hr_raw, n=200 (n_test=60), n_attn=32, with donor-prefix, zeroed-prefix and shuffled-target
controls on the SAME samples and SAME position. Fixes shipped alongside:
  * src/probes/attention_map.py: sample cap 10 -> 64; legacy per-column key renamed
    response_to_sensor_attention_PER_COLUMN_do_not_compare_to_uniform.
  * src/probes/shuffle_control.py (p6): ground truth switched from the hardcoded daily-MEAN ladder to
    datasets.hr_label_bucket on the daily PEAK -- the label the model is actually trained on. This is the
    defect exp-e33f measured at 19.4% agreement and is why p6 read exactly 0.0/0.0 on every model ever probed.

--- STAGE 1. ATTENTION AT THE hr_raw EMIT TOKEN (n=32, 34 layers, head-averaged) ---
  mean over layers 0.0298 = 0.15x uniform (uniform = S/L = 0.1964)
  BEST layer        0.3943 = 2.01x uniform, at LAYER 0
  L0 0.394 | L1 0.047 | L2 0.055 | L4 0.077 | L17 0.0021 | L33 0.0073
NUANCED, AND IT PARTLY VINDICATES THE "LOW ATTENTION" INTUITION: attention to the sensor block is
2.01x uniform at LAYER 0 and then collapses to <=0.4x uniform for all 33 remaining layers. The LM reads the
prefix essentially ONCE, at the bottom of the stack, and barely revisits it. What is NOT supported is
"the model ignores the soft tokens" -- 2x uniform at L0 is the opposite of ignoring, and stages 2-3 show the
content arrives and persists to the top.

--- STAGE 2. CAUSALITY: the prefix demonstrably shapes that hidden state ---
  cos(real, zeroed-prefix) 0.9589   relative L2 change 0.2862
  cos(real, donor-prefix)  0.9937   relative L2 change 0.1016

--- STAGE 3. INFORMATION: hr_raw is decodable AT THAT TOKEN, and it is not context leakage ---
  RidgeCV on the final hidden state, 140 train / 60 test:
    real prefix     R2 0.8161   MAE 1.481 bpm
    donor prefix    R2 0.1369   MAE 6.983 bpm
    zeroed prefix   R2 0.1335   MAE 6.943 bpm
    shuffled target R2 0.0097
    always-predict-median MAE 7.590 bpm  ->  probe SKILL +6.110 bpm
  THE CONTROL IS LOAD-BEARING AND IT WORKS: the teacher-forced context contains heart_rate_bucket, which is
  derived from the same HR channel, so the context leaks. The donor arm carries the IDENTICAL leaking context
  with a different prefix and reaches only R2 0.137. The prefix increment is +0.679 R2. Quote the increment.

--- STAGE 4. USAGE: the model's own head, same position, same samples ---
  native free-running hr_raw emission (exp-e34, seed_1): MAE 11.53 bpm, corr +0.550,
  always-predict-median 6.80 bpm  ->  skill -4.73 bpm.
  SO: RIDGE READS 1.48 bpm OFF THE VERY HIDDEN STATE FROM WHICH THE MODEL'S OWN HEAD EMITS 11.53 bpm.
  The probe beats a constant by +6.1 bpm; the model loses to the same constant by -4.7 bpm. ~10 bpm of hr_raw,
  present at the emitting token, unused by the emitter. This replicates on hr_raw the localisation exp-e34
  established on hr_peak, so it is not an order-statistic artifact -- it holds for a plain daily MEAN.

--- CONSEQUENCE FOR THE ARCHITECTURE QUESTION ---
"Make the LLM attend to the soft tokens" is the wrong objective: at L0 it already attends at 2x uniform and the
content provably reaches the top of the stack. The tractable objective is to make the OUTPUT HEAD use what
arrives. The one genuinely new attention finding worth acting on is the L0-only reading pattern -- if the
prefix is only consulted at the bottom, an interface that re-injects it deeper (gated cross-attention at
multiple layers, LLaMA-Adapter arXiv:2303.16199) targets a measured property rather than a guessed one.

=== SIXTH REVISION 2026-08-06. THE PREMISE IS NOW MEASURED AT POWER, AND THE DIAGNOSIS INVERTS. exp-e34-powered-native-emission (rb-e34) executed on two seeds. Prereg hypothesis REFUTED 0/4. ===

--- WHAT WAS MEASURED (NATIVE EMISSION, free-running generate + parse; never a fitted probe) ---
Strict-clean pool n=1546, random n=320/seed, donor arm = prefix of a different sample on the same target,
baseline median recomputed within population, paired bootstrap CI95.
                                  seed_1 (n=312)            seed_3 (n=290)
  hr_peak MAE                     19.968                    19.371
  always-predict-median           18.055                    17.491
  SKILL                           -1.912 [-3.216,-0.607]    -1.880 [-2.936,-0.811]   CI EXCLUDES ZERO, BOTH
  donor-prefix gap                +0.484 [-0.220,+1.162]    +0.724 [+0.100,+1.338]
  corr(pred,true)                 +0.126                    -0.023
  emitted std / true std          4.52/22.19 = 0.204        4.14/21.67 = 0.191
  distinct emitted values         5 (130 bpm on 245/312 = 78.5%)   17 (span 100-116)
  bucket entropy                  0.102 (302/312 tachycardic)      0.000 (290/290 elevated)
  hr_raw control                  MAE 11.53 vs median 6.80         MAE 6.03 vs median 6.65 (corr 0.55/0.57)

--- 1. THE DEFICIT IS REAL AND POWERED, BUT ~3x SMALLER THAN THE PROBLEM'S FOUNDING NUMBER ---
-1.9 bpm replicated on two independent seeds with CI excluding zero, not -5.88. The rule_out that -5.88
is not quotable STANDS and is corroborated. Ten experiments were sized against a 3x-inflated effect.

--- 2. THE DECISIVE NEW FACT: NATIVE EMISSION IS MODE-COLLAPSED; THE FITTED DECODE FROM THE SAME
       HIDDEN STATES IS NOT. ---
  NATIVE (this experiment):  std ratio 0.20, 5 distinct values, donor gap +0.48 bpm, skill -1.91
  FITTED  (exp-e26/e33, same adapter family, same token position): std ratio 0.9999, 129 distinct
          values, donor gap +9.916 bpm, skill +4.23 bpm CI [2.49,5.98]
Same features, same position; the difference is entirely WHO READS THEM. ~10 bpm of hr_peak information
is PRESENT at the emission position and UNUSED by the model's own head. This is an OPTIMISATION failure,
not an information ceiling -- exactly the caveat exp-e28 attached to its own conclusion ("fitted readouts
on frozen features ... do not prove end-to-end training would find the same solution"). E28's headline
"the cap is INFORMATION, not readout capacity" is correct ONLY for the 10.27->5.69 bpm span it tested; it
does not license the 19.97 bpm the system actually emits.

--- 3. PARTIAL RETRACTION REQUIRED ON rule_out 'the-hr-peak-native-emission-deficit-can-be-quoted-as...' ---
Its clause (a) "MODE COLLAPSE -- REFUTED EMPHATICALLY (std ratio 0.9999, 0% in the 54-67 band,
129 distinct values)" was measured on probe_e26 fr_pred -- a FITTED classifier decode. On NATIVE emission
mode collapse is CONFIRMED on both seeds. The rest of that rule_out (figure not quotable, prior-sampling
refuted, contamination does not flip the sign) survives.
ALSO: the pipeline's collapse detector band "constant ~54-67 bpm" (run_12ch_pipeline.sh:69-75) is the
WRONG DETECTOR. Both observed attractors sit at ~130 and ~110 bpm. A band-based tripwire cannot see this.
Use std(emitted)/std(true) and the donor gap instead.

--- 4. THE E23 SAMPLER FIX LANDED AND DID NOT PREVENT RE-SATURATION ---
Realised training marginal 0.164/0.289/0.309/0.238 (healthy), yet emission saturates onto ONE label per
seed -- and onto a DIFFERENT label per seed. Saturation is therefore NOT caused by the sampler marginal.
gate-prediction-entropy is now 0 for 4. Per rb-e4's own stopping rule, the architecture question has earned
a real hearing -- but see 5: the setup has cheaper, larger defects that must be excluded first.

--- 5. SETUP DEFECTS MEASURED THIS SESSION (unpreregistered; problem evidence, no calibration weight) ---
(a) THE GENERATION OBJECTIVE IS 3-5% OF THE LOSS. Parsed from all three e33 seed logs:
    total_loss = llm_loss + probe_weight*probe_loss (+0.1*hr_aux), probe_weight=1.0 and the probe MSE is on
    UNNORMALISED bpm-scale targets. CLM share: median 4.78/4.74/3.36% per seed; 0.61-2.48% over the first
    500 steps. ~95% of the objective is an auxiliary regression of hr/hrv/deep-sleep from LLM hidden states.
    CAVEAT, STATED: loss-magnitude share is not gradient share (different heads, different pathways).
    rb-e33 gradient attribution is the correct confirmation and should now be UNBLOCKED.
(b) THE HEADLINE HEALTH METRIC CANNOT RESPOND TO PHASE C. p1 hr_r2 = 0.9254 / hr_mae = 4.6142 are
    BYTE-IDENTICAL across all three seeds because lora_proxy_12ch sets freeze_encoder=true and does not set
    train_projector; temporal_encoder.pt, cross_modal_projector.pt and channel_fusion.pt are md5-identical
    across 12ch_v1, e23, e23v2 and all three e33 seeds. p1 measures the Phase B checkpoint. It is also
    approximately what (a) spends 95% of the gradient optimising.
(c) BUDGET: 2600 micro-steps x batch 2 x ga 4 = 650 optimizer updates = 0.75 epochs over 6944 samples;
    the first 100 steps run with LoRA frozen and CLM weighted 0.0.
(d) NO VALIDATION SPLIT EXISTS ANYWHERE. No val-loss key in any of 77 W&B runs; no adapter-level held-out
    cohort on disk. Every eval population, including this one, is drawn from the training domains.
    THIS EXPERIMENT INHERITS THAT LIMITATION -- and it makes the result worse, not better: the model fails
    on data it was trained on.
(e) THE BISTABILITY MITIGATION IS UNCALIBRATED. run_12ch_pipeline.sh documents ~50% success "at the tuned
    hr_regression_weight=0.002"; the live profile runs 0.1 (50x) and the rate was never re-measured.
    Observed at 0.1: 0 of 3 usable. Header claims default N_SEEDS_C=4; code default is 1.
(f) TWO SEED-SELECTION GATES ARE DEAD, CONFIRMING exp-e33f: p3 swap_attribution_acc is 0.0 in EVERY probe
    JSON on disk back to June -- it has never once been measurable; p6 returns 0/0/0 everywhere.
    NUANCE WORTH RECORDING: the driver's verdict "all 3 seeds collapsed" was reached for INVALID reasons
    but is CORRECT. A broken thermometer read "sick" on a genuinely sick patient. The p2 ablation family
    (output_diff_rate_zero 1.0, monotonic_scale_response 0.84-0.89) called seeds 1 and 3 conditioned and is
    also misleading: it proves the output TEXT moves with the prefix, not that hr_peak tracks it.

--- 6. WHAT THIS DOES AND DOES NOT SETTLE ---
SETTLED: the encoder/projector/soft-token pathway delivers hr_peak to the emission position (R2 0.685,
donor null -0.001, fitted decode +4.2 bpm). The failure is downstream of that, in what Phase C training
teaches the head to do. "The architecture cannot carry the signal" is not supported.
NOT SETTLED: whether an end-to-end objective CAN find the readout. That has never been tested, because no
Phase C run has yet given the CLM term majority gradient share, a full epoch, a validation split, or an
adaptable output head. Declaring the architecture dead on the current evidence would be declaring it dead
on a training configuration that spends 95% of its objective elsewhere.

--- 7. QUEUE ---
rb-e34 COMPLETE. rb-e33 gradient attribution -> UNBLOCK and promote (it now has a trustworthy target and a
specific quantity to attribute). rb-e4-lora-lm-head remains the best mechanism arm and is now much better
motivated: the readout is provably the unused stage. Before any of them, the cheap setup arm nobody has
run: rebalance probe_weight / hr_regression_weight so CLM is not 3-5% of the objective, add a held-out
split, and train >1 epoch. seed_2 eval still running for completeness.
Artifacts: scratch/eval_e34_native_emission.py, outputs/probes/e34_native_seed_{1,3}.json (+ _per_sample),
logs/e34_native.log, scratch/_analysis_local.md, scratch/_analysis_wandb.md.

SCOPE: residual defect after the train/eval channel_mask inversion was fixed. Three 2600-step runs plus corrected re-analyses.

=== FIFTH REVISION 2026-08-05 (evening). THE DEFECT IS NOW LOCALISED TO THE READOUT. No training run was spent. E1 is REFUTED (rule_out ro-dual-avg-max-pooling-downstream-20260805); new experiment exp-e24-lm-hidden-peak-20260805 closed with verdict SUPPORTS. Full evidence lives in those two records -- this banner is the synthesis. ===

--- THE PIPELINE IS NOW MEASURED AT EVERY STAGE, ON ONE CLEAN POPULATION ---
  1. soft-token PREFIX (production projector output)   hr_peak R2 0.8084, increment +0.7211 over true mean
  2. after 34 FROZEN LM layers, at the exact token
     position that emits hr_peak                       R2 0.6850, donor-prefix null -0.0014
  3. the EMITTED value                                 NEGATIVE skill vs always-predict-median
                                                       (-5.88 bpm FULL, -6.48 bpm clean SPARSE)
Present at the input, present at the output POSITION, absent from the output VALUE. The loss is at
the FINAL LINEAR STEP -- not the representation, not the forward pass. First direct localisation.

--- E1 (eiv-dual-avg-max-patching) REFUTED AT THE PRODUCTION SITE ---
Incumbent avg-pool is NOT peak-blind: increment +0.4067 vs the intervention's +0.4157 (delta +0.0090,
gate FAILED). Max-only is WORSE than avg-only, and the double-width arm is worse than the matched one.
The prior Step-1 probe that motivated this arm pooled the RAW ENCODER output to ONE vector and never
ran the projector. Stage sweep, strict-clean: encoder +0.1694 vs PRODUCTION +0.7211 at matched k=4;
k itself moves <0.02, independently re-confirming ro-sensor-slot-granularity-20260805.
STANDING RULE: a probe of the encoder output is NOT evidence about the soft-token prefix. Replicate
cross_modal.py:218-281 exactly (post query_proj + cross-attention + RMSNorm, k=4 slots/channel,
unit-RMS renorm) before claiming anything about the representation.

--- EVERY PRIOR PROBE POPULATION IN THIS PROBLEM WAS ZERO-CONTAMINATED ---
The legacy filter (M_mask coverage>0.9, mean>20) leaves days that are PARTLY zero, because M_mask marks
zeros as PRESENT: 2369/4010 (59.1%) contain >=1 zero-bpm "present" step, mean zero fraction 0.334,
population hr_mean 44.7 bpm -- not physiology. corr(zero_fraction, excursion) = +0.3848.
USE THE STRICT FILTER: no zero-bpm steps at all AND mean>40 AND cov>0.9 -> n=1641, hr_mean 69.9,
hr_max 120.3, excursion 50.4. The control R2(max|true mean) is 0.4899 contaminated vs 0.0873 clean, so
numbers from the two populations are NOT comparable. Implemented in scratch/probe_e1_step2_deconfound.py.

--- A GATE-DESIGN ERROR OF MINE, RECORDED RATHER THAN HIDDEN ---
E24's headline (final-layer increment +0.7476) was WITHDRAWN by its own control: placing the read head
requires teacher-forcing the answer prefix, which contains the true bucket label and true hr_raw, and a
ZEROED prefix still scores 0.7560. I had written the survival rule as (A-B) > +0.15 AND C > control+0.15,
but (A-B) is a REDUNDANCY test, not a presence test -- when the context alone reaches 0.756 no second
source can add much regardless of what it carries. The honest figure is the neutral-context arm:
R2 0.6850 with a donor-prefix null of -0.0014 (per-sample effect +0.6864) and shuffled-target -0.0125.
LESSON, generalising the fourth revision's: an incremental-over-trivial control is not enough when the
MEASUREMENT APPARATUS itself injects the answer. Every teacher-forced probe needs a zeroed-conditioning
arm and a donor-conditioning arm, or it is measuring its own scaffolding.

--- THE FOURTH REVISION'S "HONEST CEILING" IS RETRACTED (same stage+population artifact) ---
The fourth revision warned: "even avg+max concat reaches only R2=0.2968 on EXCURSION (max minus
mean) ... expect hr_peak to improve, not to be fixed", and speculated the real peaks live at
sub-epoch resolution already averaged away by the 15-min canonical schema. That pessimism was
measured on ENCODER features on the CONTAMINATED population -- both errors at once. Re-measured:
  stage / population                       R2->excursion   increment over true mean
  encoder,    contaminated (reproduces 4th)    0.2698            +0.1259
  encoder,    strict clean                     0.1569            +0.1556
  PRODUCTION, contaminated                     0.8271            +0.6832
  PRODUCTION, strict clean                     0.7926            +0.7912
(control R2(exc|true mean) = 0.1439 contaminated, 0.0014 clean.)
The soft-token prefix carries the excursion at increment +0.7912, not +0.13. The 15-min schema has
NOT averaged the peak away, and the "fix belongs in the canonical merge" speculation is withdrawn.
This STRENGTHENS the readout localisation: what reaches the LM is not a degraded peak estimate that
generation then fails to exploit -- it is a near-complete one.

--- QUEUE ---
rb-e4-lora-lm-head-20260805 PROMOTED to priority 15 (top). No longer "the next untried lever" but the arm
the evidence points at. Premise verified in code: src/training/lora.py:15-23 DEFAULT_TARGET_MODULES covers
q/k/v/o_proj + gate/up/down_proj and EXCLUDES lm_head; pipeline.py:2896 calls attach_lora_adapters with no
target_modules override and no profile key plumbs one through, so it needs a small config change, not a
YAML edit. resolve_target_modules() SILENTLY SKIPS unknown names -- a typo yields a run that trains nothing
new and reads as a null; assert the resolved list before step 0.
rb-e1 -> abandoned. rb-e0, rb-e5 -> complete.

--- SCOPE LIMITS ---
1. PRESENCE IS NOT USABILITY. Ridge has a free scale; a frozen head does not. E4 can still fail.
2. hr_raw sits at R2 0.9905/0.9701 at the SAME position and DOES generate; hr_peak sits at 0.8029/0.6850
   and does not. This probe cannot tell whether that gap is what separates usable from unusable.
3. Measured on ONE checkpoint (e23v2); mechanism evidence is not inheritable across checkpoints.
4. gate-prediction-entropy has still passed 0 of 3. If E4 fails it too that is four, and the architecture
   question deserves a real hearing rather than a fifth cheap lever.

=== FOURTH REVISION 2026-08-05 (later same day). NO NEW TRAINING RUN. Cheap CPU ridge-probe analysis of the ENCODER REPRESENTATION ITSELF, prompted by two user hypotheses: (i) is a 4B LLM simply unable to compute max/min, a 'count the r's in strawberry' language limitation? (ii) is a 24h window too much context, and should the window and/or sensor-slot polling be shortened? BOTH ANSWERED NO, and the second is an active TRAP. These are unpreregistered measurements recorded as problem evidence, NOT as an experiment -- no hypothesis was committed in advance, so they carry no calibration weight. ===

--- THE SLOT ASYMMETRY IS AN ORDER-STATISTIC PROBLEM, NOT AN LLM REASONING PROBLEM ---
hr_raw (a daily MEAN) conditions at generation corr +0.740 full-coverage; hr_peak (a daily MAX) at only +0.276. TemporalPatching (src/encoders/temporal.py, patch_len=8 stride=4 over T=96 -> 23 patches) AVERAGES 8 timesteps = 2 hours; the projector then applies adaptive_avg_pool2d (src/projectors/cross_modal.py:260) and correlation_head.py:107 uses x.mean(dim=2). Averaging is a linear operator over the window; a max is an order statistic. Within-patch extremes are destroyed at patch construction and no downstream reader recovers them.

Ridge probes, n=4010 clean fully-observed days (coverage>0.9, mean>20 bpm to exclude the zero cluster), 70/30 split, RidgeCV:
  23 avg-pooled patches   R2->MEAN 0.9305   R2->MAX 0.4401
  23 max-pooled patches   R2->MEAN 0.8689   R2->MAX 0.5066
  avg+max concat          R2->MEAN 0.9348   R2->MAX 0.5153

DECISIVE CONTROL -- almost all of that R2->MAX is fake:
  R2(max | TRUE MEAN alone, a single feature) = 0.4371
So 23 avg-pooled patches add +0.0030 over simply knowing the average. The encoder is not estimating peaks; it is predicting 'peak ~ level + constant'. On the pure excursion target (max minus mean, mean 56.2 std 19.1 bpm): avg 0.1685, max 0.1273, avg+max concat 0.2968 (+76% over avg alone). New diagnostic eiv_peak_excursion_increment_r2 registered to make this control mandatory; raw R2->max must never be quoted without it.

SCOPE CORRECTION on an earlier overstatement: 'average pooling CANNOT represent a max' is too strong -- a nonlinear head could take a max ACROSS the 23 patch means. The irrecoverable loss is WITHIN-patch (8 timesteps averaged to one number). The claim is bounded to that.

--- SENSOR-SLOT COUNT IS NOT THE BOTTLENECK (rules out one user hypothesis) ---
Sweeping projector slots at fixed 24h window: 23 -> 0.4659, 12 -> 0.4330, 8 -> 0.4460, 4 -> 0.4528, 2 -> 0.4548, 1 -> 0.4573 R2->MAX. Compressing 23 slots to ONE costs ~0.009. Tuning slot count or polling granularity cannot recover information already deleted upstream. Do not spend an arm on it.

--- SHORTENING THE WINDOW IS A METRIC TRAP (rules out the other user hypothesis) ---
Sub-windows of fully-observed days, same probe:
  window   R2->MAX   control R2(max|mean)   INCREMENT   tachycardic base rate
  24h      0.4659    0.4606                 +0.0053     0.183
  12h      0.4173    0.3465                 +0.0708     --
  6h       0.3997    0.3740                 +0.0258     0.055
  4h       0.4746    0.4650                 +0.0096     --
  2h       0.6715    0.6715                 +0.0000     0.023
  1h       0.8385    0.8385                 +0.0000     0.014
Raw R2->MAX nearly doubles from 24h to 1h, but the control rises IDENTICALLY: every point of gain is 'max converges to mean in a short window'. Incremental information above level is EXACTLY ZERO at 1-2h. A shortened window would produce a beautiful hr_peak correlation reflecting no peak detection whatsoever -- the same class of false-PASS that zero-HR contamination produced earlier in this problem.

It also destroys the label distribution the bucket rule depends on: hr_peak>=120 tachycardic base rate collapses 18.3% -> 1.4%, elevated 30.0% -> 4.0%. Three of four classes go nearly extinct, converting a conditioning-collapse problem into a class-imbalance problem while eiv_gen_pred_bucket_entropy would likely IMPROVE. RULED OUT -- do not re-litigate.

--- LLM-CAPABILITY EXPLANATION REJECTED ---
The 'strawberry' analogy does not transfer: hr_peak is computed upstream and handed to the LM as a soft token, so the LM performs no max computation and no character-level counting. Prefix-tuning literature establishes frozen LMs read continuous prefixes, and the SAME LM already reads hr_raw from the SAME prefix at corr +0.740. The bottleneck is upstream encoding, not downstream token reading.

--- NEXT ARM, RE-RANKED ---
New registered intervention eiv-dual-avg-max-patching (concatenate max-pooled patch features alongside avg-pooled ones) now outranks the schema reorder, because it is supported by direct measurement of the representation rather than by reasoning about generation order. Gate it CPU-side on eiv_peak_excursion_increment_r2 rising materially above +0.0030 on real encoder features BEFORE spending a 2241s training run. Preferred over learned/gated attention pooling as a first move: softmax is a convex mixture approaching argmax only at sharp temperature (arXiv 'Softmax is not Enough for Sharp Size Generalisation'), and would have to learn peak selection through a frozen LLM with no direct supervision -- the same speculative-architecture move Arm 1 punished. CBAM-style dual avg+max statistics are established precedent. Attention pooling remains the fallback.

HONEST CEILING, STATED UP FRONT: even avg+max concat reaches only R2=0.2968 on excursion, and the increment over mean is small at EVERY window (best +0.071 at 12h). Expect hr_peak to improve, not to be fixed. Since the bucket rule keys on hr_peak>=120, a modest numeric gain may not move the categorical slot at all. Real peaks may live at sub-epoch resolution that the 15-min canonical schema already averaged away -- if so the fix belongs in the canonical merge, linking this to problem canonical-mask-marks-degenerate-zero-channels-as-present-20260805.

OPEN THREAD NOT YET INVESTIGATED: logs/arm1_attnfix.log shows aux_loss=nan at EVERY logged step of the completed Arm 1 run (e.g. step=2560 loss=5.5481 llm_loss=0.6668 probe_rmse_norm=2.2079 aux_loss=nan hr_aux=0.0628). The run completed with other loss terms finite. If that auxiliary term was silently dead the whole run, every arm on profile lora_proxy_12ch_attnfix has been training without it, which changes how prior results should be read.

ALSO PENDING (blocking, no GPU): scratch/eval_e23_gates.py persists only aggregates, not per-sample predictions, which blocks offline decomposition. The Arm 1 SPARSE hr_raw corr of +0.965 is SUSPECTED TO BE A BIMODALITY ARTIFACT -- that eval population has min 0.0, p25 23.1, median 67.5 bpm, i.e. a zero cluster plus a physiological cluster, so the correlation may measure 'which clump' rather than HR tracking. Recompute excluding true hr_mean<20 before any claim resting on it stands. Related: on the FULL split the honest skill margin is modest -- model MAE 5.91 vs always-predict-median MAE 8.24 on target std 10.5 bpm.

=== THIRD REVISION 2026-08-05. Arm 1 (unfreeze projector, lever c) has now been RUN and closed as exp-e23-arm1-unfreeze-projector-20260805: verdict directional_only, hypothesis inconclusive. Prior revisions retracted a 'training instability' framing and then a 'zero prefix dependence / architecture doomed' framing. Current evidence below. ===

HEADLINE UNCHANGED: this is a SIGNAL-STRENGTH problem, not a dead-pathway problem. But the supporting evidence now rests on the BASELINE adapter only; Arm 1 did NOT rescue it and in fact inverted the mechanism.

--- ARM 1 IMPROVED EVERY GENERATION METRIC AND STILL FAILED ---
This is the key operational lesson: surface metrics alone would have scored it a clear success.
  pred_bucket_entropy  ~0 (91/91 tachy) -> 0.370   (gate 0.50, FAIL)
  peak_corr            +0.159 -> +0.604            (gate 0.30, PASS)
  peak pred_std        2.6 -> 27.8 sparse / 7.0 full
  hr_raw corr FULL     +0.604 -> +0.740 (MAE 6.9 -> 5.9)
  hr_raw corr SPARSE   +0.909 -> +0.965 (MAE 17.7 -> 8.7)
  bucket_acc           0.253 -> 0.354
Regime split: SPARSE cov<=0.5 entropy 0.537 (would pass alone); FULL cov>0.5 entropy 0.073 with 47/48 still tachycardic. The residual collapse is now COVERAGE-DEPENDENT and WORSE where HR data is more complete -- the opposite of a missing-information story.

--- THE MECHANISM CHECK KILLED IT ---
E23M/E23N re-run on arm1 with identical seed, samples and donors as baseline. The preregistered mechanism gate was paired-swap delta >= +2.5 nats. Observed -1.128: not a shortfall, a SIGN INVERSION.
  swap_delta_mean  +0.418 -> -1.128  (matched prefix now scores the true label WORSE than a mismatched one)
  null_delta_mean  -1.750 -> +1.090  (a norm-matched RANDOM gaussian prefix scores the true label BETTER than the real encoder output)
  ratio            +0.082 -> -0.399
  bucket dose slope  r=+0.204 p=9.7e-05 -> r=-0.073 p=0.166, non-monotone (near -0.698, mid +0.037, far -1.030)
  bucket bootstrap CI [+0.021,+0.605] -> [-1.327,+0.187] INCLUDES 0, Cohen d -0.191
  hr_raw dose slope  r=+0.073 p=0.165 -> r=+0.113 p=0.033, monotone (+0.129/+0.237/+0.478); CI [+0.057,+0.502] EXCLUDES 0

THE TWO SLOTS SWAPPED ROLES. Baseline: the CATEGORICAL slot carried the significant HR-graded dose slope and hr_raw did not. Arm 1: exactly reversed. Consistent with unfreezing the projector REALLOCATING per-sample sensor signal to the numeric slots -- precisely where all the generation gains appeared -- rather than amplifying categorical conditioning.

A POSITIVE null_delta IS A MIS-SIGNED REGIME, NOT A WEAK ONE. It forbids attributing arm1's entropy/peak_corr gains to improved sensor conditioning at the bucket slot. STANDING RULE: every future arm must report null_delta SIGN and the dose-response slope alongside its generation metrics.

--- THE BASELINE CLAIM SURVIVED ITS OWN DECISIVE CONTROL AND IS NOW STRONGER ---
E23N on e23v2 (60 samples x 6 donors = 360 pairs): bucket delta +0.313, bootstrap 95% CI [+0.021,+0.605] EXCLUDES zero (the earlier n=40 single-donor CI [-0.034,+0.855] did NOT), bootstrap one-sided p=0.0191, Cohen d 0.271, Wilcoxon p=0.0502, and a MONOTONE dose-response r=+0.204 p=9.7e-05 (near |dHR| 5.1bpm -> +0.078; mid 19.9 -> +0.208; far 50.9 -> +0.642). A binary swap can be moved by any generic mismatch; a slope graded by |dHR| cannot. On e23v2 the categorical slot genuinely reads HEART RATE. Averaging 6 donors per unit removed the donor noise that made the n=40 estimate fragile.

--- NEW METHOD RESULT THAT INVALIDATES A PLANNED APPROACH ---
Teacher-forced paired-swap delta does NOT rank slots by generation quality. On e23v2 the slot that demonstrably WORKS in free generation (hr_raw, corr +0.604/+0.909) had the WEAKER delta (+0.161) and a NON-significant dose slope (p=0.165), while the degenerate bucket slot had the stronger, significant one. Measured per-slot gap 0.51x baseline / -0.50x arm1. CONSEQUENCE: do not optimise swap-delta as a surrogate for generation conditioning, and abandon the plan to calibrate 'enough to drive generation' against a known-working slot -- that benchmark does not exist, because the metric is not monotone in the property of interest. The 12.2x figure remains a valid characterisation of the baseline gap but is NOT a safe optimisation target.

--- SCRIPT BUG FOUND AND FIXED ---
scratch/diag_e23M_effect_vs_prior.py computed required_gain_x = prior/max(effect,1e-6); a negative effect clamped the denominator to 1e-6 and printed '~2825027.1x gain required'. Divide-by-epsilon artifact with no meaning, must never be cited. Patched to return None unless the effect exceeds 1e-3.

STILL RULED OUT BY DIRECT MEASUREMENT (do not re-litigate): training marginal (elevated 30.75/tachy 25.50/resting 22.00/brady 21.75 measured live); representation limit (Ridge hr_peak R2=0.753, hr_mean R2=0.992, hr_std R2=0.969, n=600); train/eval prompt skew (byte-identical); sampler/target partition mismatch (real, fixed, did not resolve); zero-HR contamination (real, 1830 samples, fixed, mainly faked a passing peak-corr gate +0.680 -> +0.159 honest); training instability (e23v2 stable, collapses harder); PLUS from the fourth revision: window shortening, sensor-slot/polling granularity, and the LLM-cannot-compute-max framing.

LEVER STATUS: (c) unfreeze projector = REFUTED as a route to categorical conditioning, but a genuine win for the NUMERIC slots (hr_raw, peak) and worth keeping for that alone. UNTESTED: (a) value_loss_weight concentrated on the bucket token; (b) LoRA on the LM head / final layers, currently not targeted; (e) shorten/reorder the JSON schema so the bucket is not slot 1, cutting the prior it must beat; (f) NEW, now top-ranked: eiv-dual-avg-max-patching. Next arm should target the FULL-coverage regime specifically, since that is where the attractor survived. Zero-init gated cross-attention (arXiv:2303.16199) remains a candidate; architecture inadequacy remains NOT established.

GATING LESSON: eiv_gen_pred_bucket_entropy caught the original collapse while eiv_gen_peak_corr reported a false PASS (zero-HR contamination). Now extended: BOTH generation gates can move favourably while the underlying conditioning inverts. Pair distributional + correlation gates with a MECHANISM gate (swap delta sign, null_delta sign, dose slope), evaluate label-stratified, and split by HR coverage at 0.5. FOURTH-REVISION EXTENSION: the same false-PASS pattern exists for REPRESENTATION metrics -- always report an incremental-over-trivial-predictor control (eiv_peak_excursion_increment_r2) rather than a bare R2, because a config change can raise raw R2 while driving the incremental signal to exactly zero.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
