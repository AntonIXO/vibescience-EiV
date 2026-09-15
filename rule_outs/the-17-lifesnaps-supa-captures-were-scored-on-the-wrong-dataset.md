---
created_at: '2026-09-01T10:51:31+00:00'
former_ids: []
id: the-17-lifesnaps-supa-captures-were-scored-on-the-wrong-dataset
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:51:31+00:00'
topic_tags: []
---

## Claim
The 17 lifesnaps-labelled *_supa captures were scored on the wrong dataset -- the scorer's --dataset filter failed or the drivers asked for the wrong population.

## Method
Read the drivers' --dataset flags and the scorer's filter, checked EV's git history, compared the eval bundle's mtime against each capture's, then reproduced the deterministic rng(0) draw for both today's supabase and lifesnaps pools and compared index ranges against the old capture.

## Evidence
NEITHER. THE EVAL BUNDLE WAS REBUILT UNDER THEM ON 2026-08-30, AND THEIR STORED idx VALUES NOW ADDRESS DIFFERENT WINDOWS (scratch/_capture_join.py, scratch/_supa_capture_census.py).

The drivers are correct: run_allowlist_ab.sh, run_c_audits.sh and the rest all pass `--dataset optihealth_supabase` and write `*_supa.json`. The scorer is correct too -- it filters md_rows by r.get('dataset') == a.dataset, raises if the pool is empty, and prints the pool size. EV = Path('data/eval_v3_1min') has not changed since 0b5aff2 (2026-08-11).

WHAT CHANGED IS THE BUNDLE ITSELF:
  data/eval_v3_1min/sample_metadata.json  mtime 2026-08-30 18:18
  data/eval_v3_1min/X_canonical.npy       mtime 2026-08-30 18:18

And the capture dates split exactly across it:
  ab_ctrl_supa      2026-08-20 03:03   -> now reads lifesnaps
  w6avg_s44_supa    2026-08-21 03:02   -> now reads lifesnaps
  v28affect01_supa  2026-08-31 09:24   -> reads optihealth_supabase
  v36labels_supa    2026-09-01 03:47   -> reads optihealth_supabase

Every capture written BEFORE the rebuild is mislabelled when read against the CURRENT bundle; every capture written after is correct.

PROVEN BY REPRODUCTION, not inferred from dates. The scorer's draw is deterministic (default_rng(0) over the dataset pool):
  today's supabase pool 230 -> rng(0) draw spans idx 4663..4892
  ab_ctrl_supa capture      -> idx 4241..4470
  today's lifesnaps rng(0)  -> idx 604..4616
The old capture matches NEITHER of today's draws. Its indices are from the pre-rebuild indexing, where 4241..4470 was the supabase block. It really was scored on supabase; those windows are simply not at those positions any more.

SO THE CORRECT STATEMENT IS NARROWER AND DIFFERENT FROM MY LAST TWO ENTRIES: the 17 old captures were scored on the RIGHT population. What is broken is the JOIN -- their idx values cannot be resolved against the current bundle, so any analysis that attaches per-row context to them (coverage, dataset, subject) silently addresses the wrong windows. Their MAE, corr and scale numbers, which need no join, are valid supabase numbers.

THEREFORE THE FLOOR IS NOT INVALIDATED THE WAY I SAID. sigma 0.8601 was pooled from MAEs, and MAEs come straight from each capture's own rows. Those 17 captures are supabase captures. The floor stands as a supabase quantity.

WHAT DOES NOT SURVIVE: the census's population labels, my 'the floor was measured on lifesnaps' entry, and every coverage/dataset column I printed for a pre-2026-08-30 capture. Also the hr_peak coverage regressions, already retracted once for the wrong-bundle join -- they were doubly wrong.

THE DURABLE RULE, and it is sharper than the one I wrote an hour ago: a capture's idx is an index into the bundle AS IT EXISTED AT CAPTURE TIME. Re-merging invalidates every stored index in every prior capture, silently. Captures should record their bundle's identity (the merge_manifest hash would do) so a stale join fails loudly instead of resolving to the wrong rows. Until they do, only join captures written after the most recent bundle mtime.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
