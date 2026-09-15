---
created_at: '2026-09-04T23:58:37+00:00'
former_ids: []
id: nurse-stress-scored-solo-is-not-a-usable-free-living-momentary-state-cell-for-any-readout
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-04T23:58:37+00:00'
topic_tags:
- wearables
- baseline
- control
- optihealth
---

## Claim
That nurse_stress (E4, observer-coded stress_level 0/1/2 -> flow/stress rule, 102 windows / 15 subjects) can serve, scored ALONE with a clock control, as the free-living momentary-state cell for a representation claim -- i.e. that its label is readable across subjects by any readout once the shift schedule is priced.

## Method
scratch/g5_pool_ceiling.py --pool clean --datasets nurse_stress (lifts the pooled-exclusion for a solo cell), 1,685 flow-vs-stress transition rows over 21 windows / 12 subjects, GroupKFold(5) by subject, 100 linear / 48 tree within-window label permutations per arm, --fold-seeds 6, 36 arms (handcrafted features at 3 spans, raw grid, clock, Pulse-PPG and PaPaGei embeddings with their random twins, unions). exp-p1b-ppgfm-sensorfm-bar-readouts-20260905, logs/g5_ppgfm_nurse_stress.json.

## Evidence
Every zero-parameter arm reads BELOW chance subject-held-out: raw_shallow/ridge AUC 0.2945 (over null -0.096, z -3.4), raw_shallow/gb 0.335, raw_multiscale/gb 0.369 (-0.119), raw_grid/gb 0.281 (-0.095, z -5.8) -- the same HR/HRV features that read 0.98 on WESAD and 0.69 on the clean pool. The label's sign flips between subjects (12 subjects, 21 windows: ~2-3 subjects per fold), so held-out folds invert. Fold-to-fold sd 0.02-0.09 against the registered free-living sigma 0.0133. Clock alone: AUC 0.642 but -0.033 over its own within-window null (the null carries the schedule). FM arms straddle chance and sit at their twins (Pulse-PPG msl/gb +0.024 vs twin +0.032); the largest reading in the table is PaPaGei's RANDOM twin (union +0.15, z +4.3). A gate written as 'beat the features by 0.027' passes on this cell for any arm near zero, which is the NEXT.md §4 literal-text trap. Consequence: Track P has NO usable free-living cell (dryad 19.6 Hz, LifeSnaps no waveform, nurse_stress inverted); a free-living PPG-FM claim needs a new corpus (WHOOP 4.0 at 437 Hz once captured, with EMA) and is not made on this record.

## Links
Rules out for problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].

#wearables #baseline #control #optihealth
