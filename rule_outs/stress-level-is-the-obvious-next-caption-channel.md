---
created_at: '2026-09-01T08:44:10+00:00'
former_ids: []
id: stress-level-is-the-obvious-next-caption-channel
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:44:10+00:00'
topic_tags: []
---

## Claim
stress_level is the obvious next caption channel to add to _label_clauses(), being by far the largest uncovered label channel (5,331 windows over 3 datasets, against 1,665 windows for each of the five pmdata channels already carried).

## Method
Joined label_events.parquet to sample_metadata dataset ids, tabulated per-dataset row/window counts and value distributions for stress_level, and computed pairwise Jaccard overlap of the rounded value supports to test whether a quoted value identifies its source. No GPU.

## Evidence
SIZE IS REAL, BUT A NAIVE CLAUSE WOULD BE A DATASET INDICATOR, NOT A LABEL. Measured on the current corpus (scratch/_stress_level_caption_feasibility.py):

  dataset             rows   windows   min    p50    max   distinct
  dryad_ifh_affect   4,373    3,564   0.00   0.72   2.00        91
  nurse_stress       6,151      102   0.00   2.00   2.00         3
  pmdata             1,685    1,665   0.00   3.00   5.00         6

Three incompatible scales under one name, exactly as labels.yaml warns ('Scale is per-source; see `range` on each dataset mapping'). Pairwise Jaccard over rounded values: dryad vs nurse 0.033, dryad vs pmdata 0.032, nurse vs pmdata 0.500 -- the first two are effectively disjoint. A caption clause quoting the native number therefore lets the model read PROVENANCE off the label, which is the shortcut this project already measured at bal-acc 0.9945 against 0.1250 chance.

THE SHAPE OF THE DISTRIBUTIONS MAKES IT WORSE, NOT BETTER. nurse_stress supplies 50% of the ROWS but only 102 of the 5,331 windows, and carries just 3 distinct values with a median pinned at its ceiling (2.0 of a 0-2 range). dryad_ifh_affect supplies 67% of the WINDOWS with 91 distinct values on a 0-2 scale. So the channel's row count and its window count point at different datasets, and a clause built on rows would be dominated by a corpus that is near-constant at its maximum.

WHAT WOULD ACTUALLY BE REQUIRED: CLAUDE.md names the two transforms and states they are not interchangeable. Units are NOT shared here (0-2 vs 0-2 vs 0-5 with disjoint supports), so the correct instrument is rank WITHIN SUBJECT (SubjectQuantileScaler), not scale_to_declared_range. That is a different arm from 'add a clause', and it inherits the CASE failure mode recorded in CLAUDE.md -- where every session fits inside one patch, a patch mean of within-subject ranks is 0.5 for every subject.

NOT REFUTED, RESCOPED. stress_level remains the largest reachable channel and is worth carrying; the claim that it is a drop-in addition to _label_clauses() is what fails. Any arm must normalise per subject first and state which dataset supplies its window mass.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
