---
created_at: '2026-08-08T04:25:22+00:00'
former_ids:
- h5-the-shortcut-is-the-coverage-signature-not-the-dataset-list-phase-a-s-contras
gates: []
id: h5-the-shortcut-is-the-coverage-2aaaa0
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: ''
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
problem_tags: []
status: inconclusive
supersedes: h1-dataset-allowlist-repairing-the-per-93cde5
topic_tags:
- dataset-whitelist
- infonce
- contrastive
- collapse
- baseline
- epoch-resolution
- wearables
---

## Statement
H5 (the shortcut is the COVERAGE SIGNATURE, not the dataset list). Phase A's contrastive
objective can identify a window's dataset of origin from its per-channel coverage pattern alone, so
InfoNCE can solve a batch by recognising provenance instead of reading physiology. Suppressing that
signature - not curating the allowlist - is the intervention that should matter.

MEASURED PREMISE (CPU, no training, subject-grouped 5-fold so subject memorisation cannot explain it,
scratch/h1_shortcut_mechanism.py):
  predicting DATASET IDENTITY        INCUMBENT (10 ds)         REPAIRED (6 ds)
  majority-class baseline                 0.662                    0.614
  per-channel COVERAGE only (12)     acc 0.9972 / bal 0.9675   acc 0.9966 / bal 0.9800
  HR hourly PRESENCE pattern (24)    acc 0.9010 / bal 0.6416   acc 0.9041 / bal 0.6050
  physiology only, HR mean+std (2)   acc 0.6361 / bal 0.1850   acc 0.6710 / bal 0.3616
Provenance is ~99.7% decodable from coverage alone and EQUALLY decodable in both allowlists, while
physiology barely clears the majority baseline. encoder_format_version: with_presence_v1
concatenates the presence flags as input channels, so this near-perfect provenance label is handed
to the encoder explicitly.

WHY THIS SUPERSEDES H1 RATHER THAN REPEATING IT. H1 edited the dataset list and was refuted
(increment -0.0708). The mechanism above shows why it had to be: the allowlist changes only how
many near-perfectly-separable classes exist (0.9972 -> 0.9966 is no reduction), never whether the
shortcut is available. An intervention aimed at the wrong object cannot succeed regardless of seed.

INTERVENTION ARMS, in increasing order of cost:
  (a) presence-flag dropout - randomly zero the presence half of the with_presence_v1 input at some
      rate during Phase A, so coverage cannot be relied on;
  (b) coverage-matched batching - compose each contrastive batch from windows of SIMILAR total
      coverage, so coverage cannot separate positives from negatives within a batch;
  (c) drop the presence channels entirely and check what breaks - this is the ablation that tells
      us whether presence carries anything the model genuinely needs.

FALSIFICATION, STATED UP FRONT. If suppressing the coverage signature leaves hr_peak and hr_mean
readout unchanged, then the shortcut was never load-bearing and the whole dataset-provenance family
of explanations - including the config's own recorded "good basin via dataset shortcut" folklore -
should be retired. That is a genuinely useful negative and I will report it as such.

CONTROL THAT MUST SHIP WITH IT: re-run the coverage->dataset decodability probe on the intervened
run. If provenance is still ~99% decodable from the learned embedding, the intervention did not do
what it claims and any readout change is something else.

## Rationale


## Plan


## Links
Tests problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Predicts [[eiv_effective_rank]] (up).
Supersedes [[h1-dataset-allowlist-repairing-the-per-93cde5]].

#dataset-whitelist #infonce #contrastive #collapse #baseline #epoch-resolution #wearables
