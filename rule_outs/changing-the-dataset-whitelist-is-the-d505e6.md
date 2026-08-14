---
created_at: '2026-08-08T04:27:51+00:00'
former_ids:
- changing-the-dataset-whitelist-is-the-lever-that-improves-results-in-phase-a-pha
id: changing-the-dataset-whitelist-is-the-d505e6
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
retracted: true
retraction_reason: "RETRACTED 2026-08-08 AS OVER-CLAIMED, by the author, before anyone
  relied on it.\n\nThe claim was written as \"the whitelist is not the lever in Phase
  A, Phase B OR Phase C\". Only\nPHASE A was ever trained as an arm (exp-1min-allowlist-20260808,
  single seed, effect -0.0708 which\nsits INSIDE the documented 0.066 seed spread
  for this profile). Phase B and Phase C were never\ntrained with a changed allowlist
  at all. Their entries rested on CPU measurements of the DATA plus a\nmechanism argument,
  and I presented that as a measured closure. It is not.\n\nEACH SUPPORTING MEASUREMENT
  IS SOUND BUT NARROWER THAN THE CONCLUSION I DREW FROM IT:\n - Phase A: dataset identity
  is 99.7% decodable from per-channel coverage in BOTH allowlists. That\n   shows
  the allowlist does not remove the provenance SHORTCUT. It does NOT show the allowlist
  does\n   not change results -- it also moves sample count (7037 -> 7578), subject
  diversity and batch\n   composition. I conflated \"does not remove the shortcut\"
  with \"does not matter\".\n - Phase B: 99.1% of post-gate samples carry state=NONE
  and every non-NONE label is a 100%-pure\n   dataset identifier. That starves the
  TRIPLET term. Phase B is not only triplets; it also carries\n   a contrastive term
  the allowlist feeds directly, and Phase B measurably adds +0.0096 peak /\n   +0.0064
  hr_mean under the incumbent filter. What it adds under a different filter is unmeasured.\n
  - Phase C: the hr_peak bucket marginal is nearly identical under the 10- and 3-dataset
  filters.\n   That is a statement about the TARGET DISTRIBUTION ONLY. It says nothing
  about attention, emission\n   behaviour or the readout gap, and optihealth_supabase
  is 7.8% of Phase C training drawn from just\n   TWO subjects, which can reshape
  generation without moving the marginal at all.\n\nTWO STRUCTURAL PROBLEMS WITH THE
  WHOLE INFERENCE:\n 1. The pipeline is SEQUENTIAL AND COMPOUNDING (A -> B -> C).
  Reasoning per phase in isolation\n    assumes an independence the architecture does
  not have; a small representational change at\n    Phase A can be amplified or absorbed
  by two downstream stages.\n 2. Every number I used is ENCODER READOUT (hr_peak /
  hr_mean from soft tokens). The deliverable is\n    the LLM's GENERATED output, and
  the end-to-end run showed that is exactly where the failure\n    lives: hr_acc 0.4609
  against a 0.569 always-predict-majority baseline, and sensor attention mass\n    0.1277
  against a 0.2238 uniform baseline. The allowlist's effect on GENERATION is unmeasured.\n\nWHAT
  SURVIVES, AND SHOULD BE RE-FILED NARROWLY: (a) the Phase A single-seed arm did not
  improve\nanything; (b) coverage decodes provenance at ~99.7% equally in both allowlists;
  (c) Phase B's state\nlabels are degenerate; (d) the Phase C bucket marginal is filter-insensitive.
  Those four facts stand\non their own measurements. The sweeping \"not the lever
  in all three phases\" does not.\n\nWHAT WOULD ACTUALLY CLOSE IT: two allowlist arms
  carried through the FULL A -> B -> C chain and\nscored on the corrected P1-P7 suite
  using GENERATION metrics, not encoder readout. Roughly 4 h of\nGPU plus text-embedding
  precomputes. Not run."
ruled_out_at: '2026-08-08T04:27:51+00:00'
topic_tags:
- dataset-whitelist
- infonce
- contrastive
- baseline
- control
---

## Claim
Changing the dataset whitelist is the lever that improves results in Phase A, Phase B or Phase C at 1-minute resolution.

## Method
Preregistered single-variable training arm (exp-1min-allowlist-20260808, refuted at -0.0708 peak increment) PLUS a CPU-only per-phase mechanism audit: subject-grouped RandomForest decodability of dataset identity from coverage/presence/physiology feature sets; the state_label x dataset contingency over the post-gate Phase A/B population; and the hr_peak bucket marginal under each phase allowlist. scratch/h1_shortcut_mechanism.py, scratch/h1_per_phase_mechanism.py.

## Evidence
CLOSES HYPOTHESIS 1 ("does changing the dataset whitelist in all three phases improve results, and
WHY for each phase") WITH A MECHANISM PER PHASE. CPU only, no training, subject-grouped where a split
is involved. Scripts scratch/h1_shortcut_mechanism.py and scratch/h1_per_phase_mechanism.py.
Unpreregistered mechanism audit; the intervention itself was preregistered and refuted in
exp-1min-allowlist-20260808.

ANSWER: NO, in all three phases -- and for a DIFFERENT reason in each. In none of them is the
dataset list the object that controls the outcome.

--- PHASE A (InfoNCE alignment): the allowlist cannot remove the shortcut, because the shortcut is
the COVERAGE SIGNATURE, not the dataset list. ---
Predicting dataset identity, RandomForest, subject-grouped 5-fold:
                                       INCUMBENT (10 ds)         REPAIRED (6 ds)
  majority-class baseline                   0.662                    0.614
  per-channel COVERAGE only (12 feats) acc 0.9972 / bal 0.9675  acc 0.9966 / bal 0.9800
  HR hourly PRESENCE pattern (24)      acc 0.9010 / bal 0.6416  acc 0.9041 / bal 0.6050
  physiology only, HR mean+std (2)     acc 0.6361 / bal 0.1850  acc 0.6710 / bal 0.3616
Provenance is ~99.7% decodable from coverage ALONE and EQUALLY decodable under both allowlists
(0.9972 -> 0.9966 is no reduction). Physiology barely clears the majority baseline. Since
encoder_format_version: with_presence_v1 concatenates the presence flags as input channels, the
near-perfect provenance label is handed to the encoder explicitly. Editing the allowlist changes only
HOW MANY near-perfectly-separable classes exist, never whether the shortcut exists. This is why the
intervention was refuted (-0.0708), and it means the single-seed caveat was not the main story:
the intervention was aimed at the wrong object.

--- PHASE B (triplet orthogonalisation): the allowlist is irrelevant because the OBJECTIVE IS
ALREADY DEGENERATE. This is the most important finding of the audit. ---
Triplets are mined on state_label. Measured over the 7037 post-gate Phase A/B samples:
  state=NONE         n=6973  (99.1% of the corpus)  dominant dataset lifesnaps, purity 0.668
  state=exam_stress  n=  20  purity 1.0000  -> wearable_exam_stress
  state=transient    n=  15  purity 1.0000  -> wesad
  state=neutral      n=  13  purity 1.0000  -> big_ideas_glycemic
  state=n_back       n=   8  purity 1.0000  -> brain_wearable_monitoring
  state=high         n=   6  purity 1.0000  -> nurse_stress
  state=medium       n=   2  purity 1.0000  -> nurse_stress
So 99.1% of samples carry a SINGLE label and therefore supply no genuine positive/negative structure,
and EVERY non-NONE state label is a 100%-PURE DATASET IDENTIFIER. The 0.9% of samples that do carry a
label define triplets whose anchor/positive/negative separation is provenance by construction. The
triplet margin in Phase B cannot be spent on physiological state because there is essentially no
physiological state in the labels; it can only be spent separating datasets. Changing the allowlist
merely changes WHICH provenance labels are available to it.
NOTE this is INDEPENDENT of the HR-gate defect fixed in commit e828b9e: that fix removed 1981
HR-empty days from Phase B, and the label degeneracy remains after it.

--- PHASE C (CLM on soft tokens): the allowlist barely moves the TARGET MARGINAL, because two
datasets dominate either way. ---
hr_peak bucket marginal at the corpus cut points (tachycardic >=145.583, elevated >=112):
  Phase A/B allowlist (10 ds)  n=7037  tachy 0.311  elevated 0.559  normal 0.131  median peak 132.3
  Phase C   allowlist ( 3 ds)  n=7352  tachy 0.311  elevated 0.556  normal 0.133  median peak 132.6
Effectively identical. Per dataset:
  lifesnaps            n=4656  tachy 0.274  median peak 131.1   71 subjects
  pmdata               n=2126  tachy 0.374  median peak 135.1   16 subjects
  optihealth_supabase  n= 570  tachy 0.379  median peak 138.4    2 SUBJECTS
lifesnaps + pmdata are >96% of either filter, so the eight datasets that differ between the phases
cannot move the marginal the LM learns to emit. The ONE material difference is optihealth_supabase,
which is in Phase C but not in Phase A/B, and which contributes 570 days from just TWO people --
7.8% of Phase C's training data drawn from n=2, and simultaneously 6.9% of the strict-clean eval pool.
That is a subject-diversity defect, not an allowlist-composition question.

WHAT THIS MEANS FOR THE RESEARCH PLAN. Hypothesis 1 is CLOSED NEGATIVE with mechanism. The three real
objects it was standing in front of are: (a) the coverage signature as the actual Phase A shortcut
carrier -> now registered as H5; (b) Phase B's missing state labels, which make its entire objective
close to vacuous -> the highest-value unexplored defect found so far; (c) Phase C's n=2 subject
concentration in optihealth_supabase. None of the three is addressed by editing dataset_filter.

## Links
Rules out for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].

**RETRACTED:** RETRACTED 2026-08-08 AS OVER-CLAIMED, by the author, before anyone relied on it.

The claim was written as "the whitelist is not the lever in Phase A, Phase B OR Phase C". Only
PHASE A was ever trained as an arm (exp-1min-allowlist-20260808, single seed, effect -0.0708 which
sits INSIDE the documented 0.066 seed spread for this profile). Phase B and Phase C were never
trained with a changed allowlist at all. Their entries rested on CPU measurements of the DATA plus a
mechanism argument, and I presented that as a measured closure. It is not.

EACH SUPPORTING MEASUREMENT IS SOUND BUT NARROWER THAN THE CONCLUSION I DREW FROM IT:
 - Phase A: dataset identity is 99.7% decodable from per-channel coverage in BOTH allowlists. That
   shows the allowlist does not remove the provenance SHORTCUT. It does NOT show the allowlist does
   not change results -- it also moves sample count (7037 -> 7578), subject diversity and batch
   composition. I conflated "does not remove the shortcut" with "does not matter".
 - Phase B: 99.1% of post-gate samples carry state=NONE and every non-NONE label is a 100%-pure
   dataset identifier. That starves the TRIPLET term. Phase B is not only triplets; it also carries
   a contrastive term the allowlist feeds directly, and Phase B measurably adds +0.0096 peak /
   +0.0064 hr_mean under the incumbent filter. What it adds under a different filter is unmeasured.
 - Phase C: the hr_peak bucket marginal is nearly identical under the 10- and 3-dataset filters.
   That is a statement about the TARGET DISTRIBUTION ONLY. It says nothing about attention, emission
   behaviour or the readout gap, and optihealth_supabase is 7.8% of Phase C training drawn from just
   TWO subjects, which can reshape generation without moving the marginal at all.

TWO STRUCTURAL PROBLEMS WITH THE WHOLE INFERENCE:
 1. The pipeline is SEQUENTIAL AND COMPOUNDING (A -> B -> C). Reasoning per phase in isolation
    assumes an independence the architecture does not have; a small representational change at
    Phase A can be amplified or absorbed by two downstream stages.
 2. Every number I used is ENCODER READOUT (hr_peak / hr_mean from soft tokens). The deliverable is
    the LLM's GENERATED output, and the end-to-end run showed that is exactly where the failure
    lives: hr_acc 0.4609 against a 0.569 always-predict-majority baseline, and sensor attention mass
    0.1277 against a 0.2238 uniform baseline. The allowlist's effect on GENERATION is unmeasured.

WHAT SURVIVES, AND SHOULD BE RE-FILED NARROWLY: (a) the Phase A single-seed arm did not improve
anything; (b) coverage decodes provenance at ~99.7% equally in both allowlists; (c) Phase B's state
labels are degenerate; (d) the Phase C bucket marginal is filter-insensitive. Those four facts stand
on their own measurements. The sweeping "not the lever in all three phases" does not.

WHAT WOULD ACTUALLY CLOSE IT: two allowlist arms carried through the FULL A -> B -> C chain and
scored on the corrected P1-P7 suite using GENERATION metrics, not encoder readout. Roughly 4 h of
GPU plus text-embedding precomputes. Not run.

#dataset-whitelist #infonce #contrastive #baseline #control
