---
created_at: '2026-09-01T14:12:14+00:00'
former_ids: []
id: editing-a-queued-drivers-flag-repairs-the-queued-run
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T14:12:14+00:00'
topic_tags: []
---

## Claim
Fixing a driver's bad CLI flag on disk repairs the queued run that uses it, because the run has not started yet.

## Method
Compared the driver's git history and file mtime against the unit's start time and the actual error in its Phase A log.

## Evidence
REFUTED BY v39scaled, WHICH DIED ON THE FLAG THAT WAS ALREADY FIXED ON DISK.

TIMELINE, all verified:
  ~11:54  eiv-v39 started; bash opened scratch/run_v39_scaled.sh and parked in its GPU wait loop
  12:21   commit a01cad4 'fix: v39 called a nonexistent CLI flag' rewrote --checkpoint-dir -> --output-dir; file mtime 12:21:03
  16:37   the wait loop finally released, Phase A ran, and logs/v39_phase_a.log reads:
            'Error: No such option: --checkpoint-dir'
          PHASE A RC=2, g0 FAIL, chain stopped before Phase B

The file on disk today contains --output-dir at lines 79 and 101. The run used --checkpoint-dir. Both are true: bash reads a script INCREMENTALLY from a byte offset, and the interpreter had already been executing for 27 minutes when the edit landed. It kept its own view.

SO THE FIX WAS REAL AND ARRIVED TOO LATE TO MATTER. The guard I built the same day (driver_flag_guard.py) reports run_v39_scaled.sh as 'train phase-a 2 flags OK' -- correctly, because it reads the CURRENT file. A static check of a file cannot see what a running interpreter already read.

COST: 4h 43min of wall time holding a queue slot, 12.9s of CPU. No GPU was spent -- the g0 geometry gate did its job and stopped the chain before Phase B, so the failure was cheap in the resource that matters. The gate fired on eff_rank = 0 because _token_geometry_probe.py could not find phase_config.json for a checkpoint that was never written; that is the gate working, not a second defect.

THE RULE, and it generalises past this repo: EDITING A SCRIPT THAT IS ALREADY RUNNING DOES NOT FIX THAT RUN, and worse, it removes the evidence -- the file now looks correct while the log shows an error that no longer exists anywhere on disk. When a queued driver has a defect, the only reliable repair is to STOP THE UNIT, edit, and restart it. I recorded 'bash re-reads a running script from the current file offset' as a reason NOT to edit a live driver earlier today, applied it correctly to v37, and did not apply it to v39 -- whose driver I had already edited before that rule was written down.

WHAT SURVIVES OF v39's QUESTION: nothing was tested. The scaled-label-clause hypothesis (stating-label-clauses-on-a-shared-axis-removes-522954) is still open and still worth an arm, on a driver started AFTER its own fix.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
