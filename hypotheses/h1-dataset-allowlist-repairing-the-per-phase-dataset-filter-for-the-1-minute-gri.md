---
created_at: '2026-08-07T21:46:34+00:00'
gates: []
id: h1-dataset-allowlist-repairing-the-per-phase-dataset-filter-for-the-1-minute-gri
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_heldout_hr_r2
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: ''
problem_id: the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid
problem_tags: []
status: refuted
supersedes: null
topic_tags:
- dataset-whitelist
- epoch-resolution
- infonce
- contrastive
- collapse
- wearables
- baseline
---

## Statement
H1 (dataset allowlist). Repairing the per-phase dataset_filter for the 1-minute grid -
dropping the six datasets that now contribute ZERO usable days, and admitting optihealth_supabase and
mmash which are genuinely >=1-minute native - improves Phase A/B representation quality; and the
mechanism is DIFFERENT in each phase, which is why one allowlist for all three phases is wrong.

MEASURED PREMISE (data level, before any run). Clean days contributed at 1-minute resolution:
lifesnaps 4042, pmdata 2015, optihealth_supabase 452, big_ideas_glycemic 93, mmash 22, and ZERO from
each of wesad, nurse_stress, wearable_exam_stress, neuro_stress_resilience_hci,
brain_wearable_monitoring, consumer_grade_wearables. Those six are 97 of 13909 samples (0.7%) with
HR-channel coverage 0.000-0.157. dryad is 15-MINUTE NATIVE (coverage 0.300 -> 0.067 = exactly 1/15).

WHY, PHASE A (InfoNCE alignment). This is the phase where the allowlist has always mattered most and
the config already records why: full-12 bs=16 gave R2=0.195 "good basin via dataset shortcut",
dropping dryad collapsed to R2=0.016 with cos_sim=1.0, the 9-filter reached 0.378, re-adding pmdata
gave 0/10 good basin, and only the StratifiedDomainBatchSampler made pmdata safe (R2 0.767, 0/6
collapse). The failure mode is the dataset-of-origin shortcut: contrastive learning provably captures
whatever mutual information is shared between the two views and discards the rest, so if
dataset identity is shared between the sensor window and its caption, InfoNCE will happily solve the
batch by recognising provenance instead of physiology. At 1 minute this shortcut gets STRICTLY EASIER,
for a concrete and checkable reason: presence/coverage now separates the datasets almost perfectly
(0.000 to 0.889), and encoder_format_version: with_presence_v1 CONCATENATES THE PRESENCE FLAGS AS
INPUT CHANNELS - the shortcut feature is handed to the model explicitly. Six near-empty datasets are
therefore not harmless padding; they are six near-pure provenance labels in the input.

WHY, PHASE B (triplet orthogonalization). Triplets are mined across states/subjects. A dataset that
supplies no usable HR days still supplies anchors and negatives whose separation is provenance, not
state, so the margin is spent orthogonalizing dataset identity rather than cognitive state. Expected
effect is smaller than Phase A but same sign.

WHY, PHASE C (CLM on soft tokens). Here the allowlist is a TARGET-DISTRIBUTION question, not a
shortcut question: the LM learns the marginal of the emitted numbers, so admitting near-empty days
teaches it to emit the prior of a degenerate cohort. Note Phase A/B and Phase C currently disagree -
optihealth_supabase is excluded from A/B but INCLUDED in Phase C (commit 71dad09) - so the encoder is
aligned on one cohort and grounded on another, and 452 clean days are seen by Phase C with an encoder
that never saw that domain.

FALSIFIABLE ALTERNATIVE I AM EXPLICITLY TESTING AGAINST: the six near-empty datasets are only 0.7% of
samples, so if the allowlist is merely a sample-count question the effect should be ~zero. A material
effect therefore implicates the shortcut mechanism rather than data volume, and a null result is a
genuine and reportable refutation of the shortcut story at 1-minute resolution.

ARMS: (control) incumbent allowlist on the 1-minute tensor; (intervention) allowlist restricted to
datasets with real 1-minute HR coverage {lifesnaps, pmdata, optihealth_supabase, big_ideas_glycemic,
mmash, dreamt}, same seed, same stratified sampler, everything else identical.

## Rationale


## Plan


## Links
Tests problem [[the-15-min-1-min-epoch-migration-recovers-a-large-genuinely-incremental-hr-peak-signal-and-silently-invalidates-the-dataset-allowlist-and-coverage-gates-tuned-for-the-15-min-grid]].
Predicts [[eiv_heldout_hr_r2]] (up).
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_peak_excursion_increment_r2]] (up).

#dataset-whitelist #epoch-resolution #infonce #contrastive #collapse #wearables #baseline
