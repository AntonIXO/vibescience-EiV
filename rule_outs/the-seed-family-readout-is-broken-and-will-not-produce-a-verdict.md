---
created_at: '2026-09-01T09:01:04+00:00'
former_ids: []
id: the-seed-family-readout-is-broken-and-will-not-produce-a-verdict
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:01:04+00:00'
topic_tags: []
---

## Claim
The v36labels seed family has a readout defect: scratch/_seed_family_report.py reports only seed 42 even though seed 43's captures already exist on disk, so the 2.7 hours of GPU will finish without producing the promotion verdict it was run for.

## Method
Read the readout's seed-selection logic, then took a file census of every expected capture to establish which cells have completed. Ran the readout against the current partial state to confirm it degrades correctly rather than raising. No GPU.

## Evidence
REFUTED -- THE READOUT IS CORRECT AND ITS BEHAVIOUR IS THE INTENDED ONE. It computes `have = [s for s in SEEDS if all(s in per[a] for a in ARMS)]`, i.e. it only reports a seed where BOTH arms have a capture. That is the paired-comparison requirement: a v36labels number at seed 43 against a v28affect01 number at seed 42 would confound the arm with the seed, which is the exact thing the family exists to separate.

File census at 12:00 confirms the state is simply mid-run, not broken:
  emission_v36labels_s43_supa.json    PRESENT
  emission_v36labels_s43_dense.json   PRESENT
  emission_v36labels_s44_*.json       missing  (cell 2 running, step 1400/2600)
  emission_v28affect01_s43_*.json     missing  (cell 3, not started)
  emission_v28affect01_s44_*.json     missing  (cell 4, not started)
So seed 43 has one arm of two. The readout correctly declines to report it.

The cell order is v36-s43, v36-s44, v28-s43, v28-s44, which means NO paired seed completes until cell 3 finishes -- roughly 2 hours in. A reader checking progress before then sees only the s42 row and can mistake a working instrument for a stalled one. That is a reporting-ergonomics issue, not a defect, and it is now on the record so the next check does not re-derive it.

VERIFIED WORKING: run against the current partial state, the readout printed the s42 comparison on both draws with correct paired deltas (supa hr_raw -1.168, dense hr_raw +1.166, matching the recorded figures) and printed 'band nan' where a single seed cannot yield a band. It degrades correctly on missing data rather than raising, so the end-of-family invocation will produce the verdict.

WHAT THE FAMILY WILL ANSWER: whether v36labels' supabase win (-1.168 bpm) and dense loss (+1.166 bpm) hold sign across three seeds. The C-only seed band is 0.80 bpm, narrower than both gaps, which is why re-seeding Phase C alone can decide it at ~40 min per cell instead of ~95 for a full chain.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
