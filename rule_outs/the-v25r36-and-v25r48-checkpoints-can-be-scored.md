---
created_at: '2026-09-01T10:11:16+00:00'
former_ids: []
id: the-v25r36-and-v25r48-checkpoints-can-be-scored
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:11:16+00:00'
topic_tags: []
---

## Claim
The v25r36 and v25r48 checkpoints already exist on disk and should be scored before any new GPU is spent locating where Phase B recovers between 25 and 30 anchors.

## Method
Listed the contents of each checkpoint directory rather than checking the directory exists, then read each arm's driver log tail and the drivers themselves.

## Evidence
I WROTE THAT ONE STEP AGO FROM A DIRECTORY LISTING, AND THE DIRECTORIES ARE EMPTY. `ls outputs/checkpoints/v25r36/` and `.../v25r48/` return NOTHING -- the paths exist because the driver runs `mkdir -p` before training, so an empty directory is the signature of a run that died, not of a run that finished.

WHAT ACTUALLY HAPPENED TO BOTH:
  logs/v25r36_driver.log, 14 lines total:
    === [04:22:11] v24 PHASE B (batch 92) ===
    === warm RC=0 ===
    === PHASE B RC=1 ===
    PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True to avoid fragmentation...
    === PHASE C RC=1 ===
    ABORT: outputs/adapters/v25r36_c/manifest.json missing -- Phase C did not produce a bundle
  logs/v25r48_driver.log does not exist at all.

So v25r36 died of the SAME OOM as the 92 arm, 31 seconds after starting, and v25r48 never ran.

THE REASON IS IN THE DRIVER, AND IT EXPLAINS THE NAMES: run_v25r_36.sh line 19 echoes '(batch 92)' and line 23 runs `--profile phase_b_12ch_v25bigbatch`. So does run_v25r_30.sh, and so does run_v25_bigbatch.sh. The three drivers are COPIES that differ only in output paths -- none of them sets a batch size, because batch_size lives in the PROFILE, and there is exactly one profile. The '30', '36', '48' in the filenames were the INTENDED sweep values, hand-edited into the profile between runs and never recorded anywhere else.

WHICH MEANS THE SWEEP DOES NOT EXIST. There is one scored arm (v25r30, supabase corr 0.717) whose profile TODAY reads batch_size 30, and two named-but-empty siblings. The recovery point between 25 and 30 anchors cannot be located from disk, and the '36' and '48' arms cannot be scored because they produced nothing to score.

METHOD FAILURE WORTH NAMING: I read `ls -d outputs/checkpoints/v25r36 v25r48`, saw both paths, and recorded them as existing checkpoints -- in a rule_out whose whole point was that a previous session had left a claim unverified. A directory is not an artefact. The check is `ls` INSIDE it, or the driver's exit line. Same shape as this morning's guard that reported ALL CLEAR after checking zero drivers: an affirmative-looking result from an empty set.

CONSEQUENCE, and it is the useful part: the profile carries batch_size 30 today and NOTHING in the repo records that 25, 36, 48 or 92 was ever tried. A hand-edited profile between runs leaves no trace once the run dies -- which is exactly how '92' ended up in a hypothesis statement while 30 shipped in every arm from v27 to v40.

## Links
Rules out for problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].
