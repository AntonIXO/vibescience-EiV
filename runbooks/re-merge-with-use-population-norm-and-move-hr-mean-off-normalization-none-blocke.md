---
created_at: '2026-08-07T20:55:15+00:00'
gates: []
hypothesis_id: null
id: re-merge-with-use-population-norm-and-move-hr-mean-off-normalization-none-blocke
priority: 10
problem_id: canonical-merge-defaults-to-rolling-per-user-z-score-while-the-phase-c-target-builder-assumes-population-norm
status: queued
topic_tags: []
updated_at: '2026-08-07T20:55:15+00:00'
---

## Prompt
RE-MERGE WITH --use-population-norm AND MOVE hr_mean OFF normalization=none.
BLOCKED ON ANTON'S MACHINE: data/processed/ here holds only lifesnaps, pmdata, wesad, nurse_stress. 9 of 13 datasets have no processed source on this host and the canonical files are owned by 'kirill'. Re-merge must run where the corpus was built.
DO (1) eiv data merge --use-population-norm --recompute-stats (pinned stats are 2026-05-24, fit on raw-ms hrv from a corpus that no longer exists). (2) change hr_mean schema normalization 'none'->'z-score' (schema.py:50) so encoder input is on one scale -- this ALSO turns the missing-data fill from a -4.18 SD outlier on 45.7% of cells into a 0.00 SD no-op. (3) raw bpm stays recoverable: _build_json_slots_target already denormalises z*iqr+median, which works today only for hr_mean by accident (ch0 stats are the identity); normalising ch0 + recomputing stats makes the SAME path correct for every channel. (4) ENFORCE the contract: persist normalization:'population'|'rolling' and refuse json_slots targets when it says rolling.
IF MOVING TO MINUTE AGGREGATION AT THE SAME TIME, FIX FIRST: window_size=96*14 is hardcoded in TIMESTEPS at merger.py:576 and :636, NOT derived from epoch_minutes. At epoch_minutes=1 it spans 22.4 HOURS not 14 days -- about one circadian period -- so the per-user baseline would normalise circadian rhythm away. Use window_size=int(14*24*60/epoch_minutes) or moot it with population norm. epoch_minutes is typed int (cli_data.py:501) so sub-minute is not expressible; 1 s would be T=86400 and ~21599 patches vs today's 23.
AFTER: rerun A->B->C. Expect hr_mean flat (saturated); any gain should appear on the non-HR channels.

## Links
Queued for problem [[canonical-merge-defaults-to-rolling-per-user-z-score-while-the-phase-c-target-builder-assumes-population-norm]].
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
