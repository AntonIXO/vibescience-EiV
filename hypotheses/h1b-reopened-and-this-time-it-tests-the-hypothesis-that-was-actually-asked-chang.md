---
created_at: '2026-08-08T08:18:23+00:00'
gates: []
id: h1b-reopened-and-this-time-it-tests-the-hypothesis-that-was-actually-asked-chang
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: none
  magnitude_note: ''
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: ''
- diagnostic_id: eiv_gen_pred_bucket_entropy
  direction: none
  magnitude_note: ''
problem_id: the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid
problem_tags: []
status: proposed
supersedes: h1-dataset-allowlist-repairing-the-per-phase-dataset-filter-for-the-1-minute-gri
topic_tags:
- dataset-whitelist
- infonce
- contrastive
- baseline
- control
- epoch-resolution
- wearables
---

## Statement
H1b (REOPENED, and this time it tests the hypothesis that was actually asked).
Changing the dataset whitelist CONSISTENTLY IN ALL THREE PHASES (A, B and C) changes the pipeline's
GENERATED output. Preregistered before running, because the previous attempt was neither.

WHY THIS IS BEING REOPENED RATHER THAN LEFT CLOSED. The prior rule_out
("changing-the-dataset-whitelist-is-the-lever...") has been RETRACTED by its author as over-claimed.
It asserted a negative for all three phases on the strength of ONE Phase A arm plus a mechanism
argument. Two things were wrong with that:
  1. THE ARM DID NOT TEST THE HYPOTHESIS. exp-1min-allowlist-20260808 changed dataset_filter in
     PHASE A ONLY and then ran nothing downstream -- no Phase B, no Phase C, no generation. The
     question asked was about all three phases. A weaker experiment was run and the stronger claim
     was reported.
  2. THE EVIDENCE WAS THE WRONG KIND. Every number was ENCODER READOUT (hr_peak / hr_mean from
     pooled soft tokens). The deliverable is what the LLM EMITS, and the end-to-end run showed that
     is exactly where the system fails: p4 hr_acc 0.4609 against an always-predict-majority baseline
     of 0.569 on the same pool -- ten points BELOW a constant predictor -- with p7 sensor attention
     mass 0.1277 against a 0.2238 uniform baseline. Nothing measured so far speaks to whether the
     allowlist moves THAT.

DESIGN. Two arms, filter changed in all three phases, everything else identical, one seed each:
  CONTROL  : phase_a_12ch_pmdata -> phase_b_12ch -> lora_proxy_12ch   (incumbent filters)
  REPAIRED : the >=1-minute-native allowlist {lifesnaps, pmdata, optihealth_supabase,
             big_ideas_glycemic, mmash, dreamt} applied in A, B AND C.
Score on GENERATION first (corrected p4 hr_acc vs the 0.569 majority baseline; p7 attention mass vs
the 0.2238 uniform baseline; p5 retrieval), and carry eiv_peak_excursion_increment_r2 for continuity
with the existing encoder-side record.

MY ACTUAL PRIOR, STATED HONESTLY: I expect this to be NULL, because four narrow facts survived the
retraction and all point that way -- (a) dataset identity is ~99.7% decodable from per-channel
coverage in BOTH allowlists, so the provenance shortcut is untouched by curation; (b) Phase B's
triplet labels are degenerate (99.1% state=NONE, every non-NONE label a 100%-pure dataset
identifier); (c) the Phase C hr_peak bucket marginal is nearly identical under both filters
(tachycardic 0.311 vs 0.311, median peak 132.3 vs 132.6) because lifesnaps+pmdata are >96% of
either; (d) the Phase A-only arm already came back negative. I am preregistering "no change" as the
honest expectation. THE POINT OF RUNNING IT IS THAT A MEASURED NULL ON GENERATION METRICS IS
EVIDENCE, WHILE AN INFERRED NULL FROM DATA-LEVEL PROXIES IS NOT.

WHAT WOULD MAKE ME WRONG, AND WHY IT IS PLAUSIBLE: the pipeline is SEQUENTIAL AND COMPOUNDING, so a
representational change too small to see at Phase A can be amplified across two further stages. And
the cohort difference is not only a marginal difference: optihealth_supabase supplies 570 days from
just TWO subjects (7.8% of Phase C training), which can reshape generation behaviour without moving
any target marginal at all. Either mechanism would produce a real effect that every data-level proxy
I measured would have missed.

BLOCKER THAT MUST BE CLEARED FIRST. p3 counterfactual (swap_measured=False, swap_total=0) and p6
shuffle control (both arms 0.0) are VACUOUS. They are precisely the probes that would attribute any
generation change to the conditioning rather than to a shifted output prior. Without them a positive
result cannot be interpreted causally and a null cannot be distinguished from a dead probe path.

COST: ~4 h GPU for both arms, plus one text-embedding precompute per distinct dataset_filter because
the cache key includes it.

## Rationale


## Plan


## Links
Tests problem [[the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid]].
Predicts [[eiv_peak_excursion_increment_r2]] (none).
Predicts [[eiv_heldout_hr_r2]] (none).
Predicts [[eiv_gen_pred_bucket_entropy]] (none).
Supersedes [[h1-dataset-allowlist-repairing-the-per-phase-dataset-filter-for-the-1-minute-gri]].

#dataset-whitelist #infonce #contrastive #baseline #control #epoch-resolution #wearables
