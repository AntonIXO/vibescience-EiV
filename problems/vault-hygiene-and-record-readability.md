---
created_at: '2026-08-14T20:24:32+00:00'
former_ids: []
id: vault-hygiene-and-record-readability
paper_refs: []
problem_tags:
- optihealth-eiv
status: open
target: null
title: vault hygiene and record readability
topic_tags: []
updated_at: '2026-09-01T10:17:03+00:00'
updates:
- at: '2026-09-01T08:02:17+00:00'
  text: "AUDITED 2026-09-01 (scratch/_vault_hygiene_audit.py, scratch/_vault_provenance_audit.py,
    scratch/_mcp_cwd_probe.sh). FIVE of the six defects are fixed. The sixth is not
    the defect this record describes -- it MUTATED into a different one that is live
    right now.\n\nFIXED, measured on the 317-record vault:\n 1. ID chaos -- 4 records
    over 60 chars, not 89, and only 3 near the 80 cut (all three are legitimately
    long PROBLEM titles, not truncated slugs).\n 2. Verdict semantics -- the `null`
    verdict now exists and is used on 9 experiments; powered nulls are no longer mislabelled
    directional_only.\n 3. Status rot -- problems 3 open / 8 parked / 11 resolved
    (was 15/17 open), runbooks 18 abandoned / 17 done / 2 queued / 1 running (was
    10 queued with no staleness signal). Six of today's parks came from this session's
    audits.\n 4. `notes` as a dumping ground -- 138/138 closed experiments (100%)
    carry artifacts, key_numbers, do_not_retry, cost AND interpretation as structured
    fields.\n 6. Tag sprawl -- 75 registered tags with merge_tags available; the vocabulary
    can now shrink.\n\nDEFECT 5 HAS MUTATED AND IS LIVE. The record's version -- '_git_ref
    ran git inside the VAULT, so a run on fix/soft-token-norm-scale was logged as
    main@d3f379f' -- is fixed: core.py:574 now has an explicit _code_repo() with a
    docstring naming that exact failure, and only 2 records still start with `main@`.
    But the replacement silently records NOTHING:\n\n  git_ref coverage by month:
    2026-07 14/14 (100%), 2026-08 12/123 (10%), 2026-09 1/1\n  Of the 16 experiments
    created in this session, 11 carry git_ref='' -- including exp-v22, v24, v25, v25r,
    v25r30, v26, v27, v28, v29, v30 and v31.\n\nROOT CAUSE, localised: _code_repo()
    reads VIBESCIENCE_CODE_REPO and falls back to Path.cwd(). Measured on the live
    server process -- VIBESCIENCE_CODE_REPO is UNSET, and the server's cwd is not
    a git repository at all ('fatal: not a git repository'). So _git_ref()'s `if not
    (branch and sha): return \"\"` fires on every call and the provenance field is
    written empty.\n\nWHY AN EMPTY REF IS WORSE THAN A WRONG ONE IN PRACTICE: a wrong
    ref is detectably wrong -- 'main@' on a checkout that is on fix/soft-token-norm-scale
    is a visible contradiction, which is how the original defect was caught. An empty
    one just drops the link between a verdict and the code that produced it, and nothing
    downstream complains. Eleven of this campaign's verdicts currently cannot be tied
    to a commit except by their close-time notes.\n\nFIX, one line of configuration:
    set VIBESCIENCE_CODE_REPO=/root/optiHealth-EiV in the MCP server's environment.
    Verified that the value would be correct -- git -C /root/optiHealth-EiV reports
    fix/soft-token-norm-scale@9299890. Not applied from this session because it edits
    the MCP server's launch environment rather than this repo; flagged for Anton.\n\nWORKAROUND
    IN FORCE MEANWHILE: pass git_ref explicitly to start_experiment. The five experiments
    in this session that DO carry a ref (v31-chain, v32, v33, v34, v36) are exactly
    the ones where it was passed by hand."
- at: '2026-09-01T09:41:18+00:00'
  text: "GIT_REF RELIABILITY MEASURED, AND THREE RECORDED REFS POINT AT COMMITS THAT
    DO NOT EXIST IN THE CODE REPO (scratch/_git_ref_reliability.py).\n\nSTATE OF THE
    AUTOMATIC PATH: still dead. The live MCP server (pid 751) carries VIBESCIENCE_VAULT
    and nothing else -- VIBESCIENCE_CODE_REPO is unset, so _code_repo() falls back
    to Path.cwd(), which is not a git repository, and _git_ref() returns empty.\n\nCOVERAGE
    BY DAY, which shows exactly what the workaround is worth:\n  2026-08-20  0/11
    \   2026-08-21  0/4     2026-08-24  0/1\n  2026-08-27  0/1     2026-08-29  0/4
    \    2026-08-30  0/13\n  2026-08-31  4/15 (27%)          2026-09-01  1/1 (100%)\nEvery
    ref on record from 08-31 onward was typed BY HAND. The zeros are what forgetting
    looks like, and they are the norm, not the exception.\n\nNEW FINDING -- THREE
    REFS NAME COMMITS THAT ARE NOT IN optiHealth-EiV:\n  d3f379f   exp-e14-within-subject-affect-20260814\n
    \ 66544f1   exp-dcl-softtoken-resolved-via-fp32-supersession-20260818\n  66544f1
    \  exp-multipositive-dcl-resolved-via-spectral-supersession-20260818\n  (12 refs
    checked with git cat-file, 3 unresolvable)\n\nd3f379f RESOLVES INSIDE THE VAULT'S
    OWN GIT REPO. That is the ORIGINAL defect's signature, preserved in the record:
    _git_ref used to run git inside self.vault.root, so a run made on fix/soft-token-norm-scale
    was logged as the VAULT's HEAD. This problem's description names d3f379f explicitly
    as the example -- and here it is, still sitting in exp-e14 as that experiment's
    provenance. 66544f1 resolves in neither repo, most likely a vault commit since
    garbage-collected or rewritten.\n\nSO THE PROVENANCE FIELD HAS THREE STATES, NOT
    TWO: correct (9 of 12 checked), EMPTY (the current failure, ~90% of August), and
    CONFIDENTLY WRONG (3 records pointing into the wrong repository). The third is
    the worst kind -- it looks like provenance and resolves to a real object in a
    repo nobody would think to check.\n\nDO NOT TRUST A git_ref ON A PRE-2026-08-31
    RECORD WITHOUT RESOLVING IT. `git cat-file -e <sha>^{commit}` in /root/optiHealth-EiV
    is the check; if it fails, try the vault repo before assuming corruption.\n\nTHE
    FIX IS STILL ONE ENV VAR: VIBESCIENCE_CODE_REPO=/root/optiHealth-EiV in the MCP
    server's launch environment. Verified the value resolves correctly. It edits the
    server's environment rather than this repo, so it needs Anton -- flagged, not
    applied."
- at: '2026-09-01T10:13:57+00:00'
  text: "PROFILE-NOTE DRIFT IS NOT A CLASS -- CHECKED ACROSS ALL 93 PROFILES (scratch/_profile_note_drift.py).
    The v25bigbatch defect (notes claimed 'batch_size 25 -> 92' while the profile
    carried 30, and every arm v27..v40 inherited the 30) was found by hand, so the
    question was whether the config is riddled with them.\n\n  profiles                                                        93\n
    \ with notes                                                      93  (every one)\n
    \ numeric 'KEY A -> B' claims naming a key the profile RESOLVES     8\n  MISMATCHES
    \                                                      0\n\nSO THE ANSWER IS NO,
    WITH A CAVEAT WORTH MORE THAN THE RESULT: only 8 claims were checkable. 93 profiles
    carry notes and nearly all describe their variable in PROSE ('tests whether the
    auxiliary triplet term is what makes fresh new-corpus Phase B degrade C') rather
    than in the machine-checkable 'KEY A -> B' idiom. The scanner verifies the 8 stated
    numerically; the rest are unverifiable by construction.\n\nVALIDATED THE SCANNER
    AGAINST THE KNOWN DEFECT rather than trusting a zero, because a zero from a broken
    matcher is exactly this morning's ALL-CLEAR-after-checking-nothing failure. Ran
    the same regex over HEAD~1's training.yaml, before today's notes fix: it matches
    'batch_size 25 -> 92', resolves batch_size in the profile, and reads the value
    as 30 -- i.e. it WOULD have flagged the one real defect. The scanner also exits
    RC=4 with 'CHECKED NOTHING' if its claim count reaches zero, so a future regex
    breakage cannot present as a clean bill.\n\nWHAT THIS LEAVES: the config's numeric
    claims are consistent with its values. Prose notes remain unverifiable, which
    is a limit of the idiom rather than a defect to fix -- and the one defect that
    DID exist was numeric, so the checkable subset is also where drift is likely,
    since a number is what changes when an OOM forces a retreat."
- at: '2026-09-01T10:17:03+00:00'
  text: "DISK WAS AT 100% WITH 551 MB FREE, WHICH WOULD HAVE KILLED CELL 4 AND EVERY
    QUEUED ARM. Found while checking whether the seed family could finish -- nothing
    in this campaign watches disk, so no alert fired.\n\n  /dev/nvme0n1p5  113G total,
    112G used, 551M free, 100%\n  outputs/checkpoints 21G | outputs/adapters 13G |
    logs 41M\n\nA Phase C adapter bundle is 282 MB, so 551 MB left room for ONE more
    cell. Cell 4 plus six queued arms would have failed at save time -- and a run
    that dies while writing has spent its full GPU cost and produced nothing.\n\nTHE
    CULPRIT WAS ONE DIRECTORY AT 2.9 GB, ten times any other:\n  outputs/checkpoints/v11lmhead_s42/phase_c/lora/adapter_model.safetensors
    = 2,956,739,912 bytes\nEvery other checkpoint is 314-595 MB. This is exp-e9's
    lmhead-LoRA arm: adapting lm_head means the adapter carries the full vocabulary
    projection, so the 'lightweight' bundle ends up larger than the base model's own
    head.\n\nVERIFIED SAFE BEFORE DELETING, four ways:\n  - no active driver references
    it (grepped run_v37..v40, the seed family, both floor runners)\n  - its experiment
    is CLOSED with verdict `refutes` (exp-e9-lmhead-lora-20260814, failure mode recipe_conditional)\n
    \ - its SCORED OUTPUTS live separately in logs/ and are untouched: e9_gate_scores_v11lmhead_s42.json,
    v11lmhead_s42_emissions_n128_summary.json, v11lmhead_s42_emissions_supabase_summary.json\n
    \ - kept the two small provenance files before removing the weights (manifest.json,
    adapter_config.json -> logs/*.kept)\n\nRESULT: 551M -> 3.4G free (98%). Cell 3's
    scoring survived the delete (pid 351335 still on GPU), all seven units still active.\n\nTHE
    GENERAL POINT, worth more than the 2.9 GB: a refuted arm's WEIGHTS are the disposable
    part and its CAPTURES are the durable part, and this campaign has it backwards
    on disk -- 34 GB of checkpoints and adapters against 41 MB of logs. The e9 verdict
    was reached in August; the weights that produced it have no remaining use, because
    reproducing the arm means re-running it on current code anyway.\n\nNOT AUTOMATED,
    deliberately: a sweep that deletes 'refuted' checkpoints would eventually delete
    something a queued arm chains off. Every deletion here was checked against the
    live queue by hand."
---

## Description
The experiment log became hard to read and partly untrustworthy. Six measured defects as of 2026-08-14:

1. ID chaos — two incompatible naming schemes; 89 records carried an id over 60 chars, auto-slugged from a full sentence and hard-truncated at 80 mid-word, so `ls experiments/` was useless and neighbouring ids were identical for their first 60 characters.
2. Verdict semantics lied on powered nulls — sign-only comparison called delta=+0.0003 at permutation p=0.65 `directional_only`, and the verdict rank put that ABOVE `inconclusive`.
3. Status rot — 15/17 problems `open` (several already answered), 8 hypotheses permanently `proposed`, 2 stuck in `testing` since July, 10 runbooks `queued` with no staleness signal.
4. `notes` was a dumping ground — close_experiment took only {experiment_id, notes}, so artifacts, key numbers and do-not-retry warnings were compressed into unqueryable prose. `artifacts` existed on the model but had no way in through the API.
5. Provenance pointed at the wrong repo — _git_ref ran git inside the VAULT (itself a repo), so a run made on fix/soft-token-norm-scale was logged as main@d3f379f. No dirty-tree flag either.
6. Tag sprawl — 69 tags over 62 experiments with no merge operation, so the vocabulary could only grow.

## Updates
- **2026-09-01T08:02:17+00:00** — AUDITED 2026-09-01 (scratch/_vault_hygiene_audit.py, scratch/_vault_provenance_audit.py, scratch/_mcp_cwd_probe.sh). FIVE of the six defects are fixed. The sixth is not the defect this record describes -- it MUTATED into a different one that is live right now.

FIXED, measured on the 317-record vault:
 1. ID chaos -- 4 records over 60 chars, not 89, and only 3 near the 80 cut (all three are legitimately long PROBLEM titles, not truncated slugs).
 2. Verdict semantics -- the `null` verdict now exists and is used on 9 experiments; powered nulls are no longer mislabelled directional_only.
 3. Status rot -- problems 3 open / 8 parked / 11 resolved (was 15/17 open), runbooks 18 abandoned / 17 done / 2 queued / 1 running (was 10 queued with no staleness signal). Six of today's parks came from this session's audits.
 4. `notes` as a dumping ground -- 138/138 closed experiments (100%) carry artifacts, key_numbers, do_not_retry, cost AND interpretation as structured fields.
 6. Tag sprawl -- 75 registered tags with merge_tags available; the vocabulary can now shrink.

DEFECT 5 HAS MUTATED AND IS LIVE. The record's version -- '_git_ref ran git inside the VAULT, so a run on fix/soft-token-norm-scale was logged as main@d3f379f' -- is fixed: core.py:574 now has an explicit _code_repo() with a docstring naming that exact failure, and only 2 records still start with `main@`. But the replacement silently records NOTHING:

  git_ref coverage by month: 2026-07 14/14 (100%), 2026-08 12/123 (10%), 2026-09 1/1
  Of the 16 experiments created in this session, 11 carry git_ref='' -- including exp-v22, v24, v25, v25r, v25r30, v26, v27, v28, v29, v30 and v31.

ROOT CAUSE, localised: _code_repo() reads VIBESCIENCE_CODE_REPO and falls back to Path.cwd(). Measured on the live server process -- VIBESCIENCE_CODE_REPO is UNSET, and the server's cwd is not a git repository at all ('fatal: not a git repository'). So _git_ref()'s `if not (branch and sha): return ""` fires on every call and the provenance field is written empty.

WHY AN EMPTY REF IS WORSE THAN A WRONG ONE IN PRACTICE: a wrong ref is detectably wrong -- 'main@' on a checkout that is on fix/soft-token-norm-scale is a visible contradiction, which is how the original defect was caught. An empty one just drops the link between a verdict and the code that produced it, and nothing downstream complains. Eleven of this campaign's verdicts currently cannot be tied to a commit except by their close-time notes.

FIX, one line of configuration: set VIBESCIENCE_CODE_REPO=/root/optiHealth-EiV in the MCP server's environment. Verified that the value would be correct -- git -C /root/optiHealth-EiV reports fix/soft-token-norm-scale@9299890. Not applied from this session because it edits the MCP server's launch environment rather than this repo; flagged for Anton.

WORKAROUND IN FORCE MEANWHILE: pass git_ref explicitly to start_experiment. The five experiments in this session that DO carry a ref (v31-chain, v32, v33, v34, v36) are exactly the ones where it was passed by hand.
- **2026-09-01T09:41:18+00:00** — GIT_REF RELIABILITY MEASURED, AND THREE RECORDED REFS POINT AT COMMITS THAT DO NOT EXIST IN THE CODE REPO (scratch/_git_ref_reliability.py).

STATE OF THE AUTOMATIC PATH: still dead. The live MCP server (pid 751) carries VIBESCIENCE_VAULT and nothing else -- VIBESCIENCE_CODE_REPO is unset, so _code_repo() falls back to Path.cwd(), which is not a git repository, and _git_ref() returns empty.

COVERAGE BY DAY, which shows exactly what the workaround is worth:
  2026-08-20  0/11    2026-08-21  0/4     2026-08-24  0/1
  2026-08-27  0/1     2026-08-29  0/4     2026-08-30  0/13
  2026-08-31  4/15 (27%)          2026-09-01  1/1 (100%)
Every ref on record from 08-31 onward was typed BY HAND. The zeros are what forgetting looks like, and they are the norm, not the exception.

NEW FINDING -- THREE REFS NAME COMMITS THAT ARE NOT IN optiHealth-EiV:
  d3f379f   exp-e14-within-subject-affect-20260814
  66544f1   exp-dcl-softtoken-resolved-via-fp32-supersession-20260818
  66544f1   exp-multipositive-dcl-resolved-via-spectral-supersession-20260818
  (12 refs checked with git cat-file, 3 unresolvable)

d3f379f RESOLVES INSIDE THE VAULT'S OWN GIT REPO. That is the ORIGINAL defect's signature, preserved in the record: _git_ref used to run git inside self.vault.root, so a run made on fix/soft-token-norm-scale was logged as the VAULT's HEAD. This problem's description names d3f379f explicitly as the example -- and here it is, still sitting in exp-e14 as that experiment's provenance. 66544f1 resolves in neither repo, most likely a vault commit since garbage-collected or rewritten.

SO THE PROVENANCE FIELD HAS THREE STATES, NOT TWO: correct (9 of 12 checked), EMPTY (the current failure, ~90% of August), and CONFIDENTLY WRONG (3 records pointing into the wrong repository). The third is the worst kind -- it looks like provenance and resolves to a real object in a repo nobody would think to check.

DO NOT TRUST A git_ref ON A PRE-2026-08-31 RECORD WITHOUT RESOLVING IT. `git cat-file -e <sha>^{commit}` in /root/optiHealth-EiV is the check; if it fails, try the vault repo before assuming corruption.

THE FIX IS STILL ONE ENV VAR: VIBESCIENCE_CODE_REPO=/root/optiHealth-EiV in the MCP server's launch environment. Verified the value resolves correctly. It edits the server's environment rather than this repo, so it needs Anton -- flagged, not applied.
- **2026-09-01T10:13:57+00:00** — PROFILE-NOTE DRIFT IS NOT A CLASS -- CHECKED ACROSS ALL 93 PROFILES (scratch/_profile_note_drift.py). The v25bigbatch defect (notes claimed 'batch_size 25 -> 92' while the profile carried 30, and every arm v27..v40 inherited the 30) was found by hand, so the question was whether the config is riddled with them.

  profiles                                                        93
  with notes                                                      93  (every one)
  numeric 'KEY A -> B' claims naming a key the profile RESOLVES     8
  MISMATCHES                                                       0

SO THE ANSWER IS NO, WITH A CAVEAT WORTH MORE THAN THE RESULT: only 8 claims were checkable. 93 profiles carry notes and nearly all describe their variable in PROSE ('tests whether the auxiliary triplet term is what makes fresh new-corpus Phase B degrade C') rather than in the machine-checkable 'KEY A -> B' idiom. The scanner verifies the 8 stated numerically; the rest are unverifiable by construction.

VALIDATED THE SCANNER AGAINST THE KNOWN DEFECT rather than trusting a zero, because a zero from a broken matcher is exactly this morning's ALL-CLEAR-after-checking-nothing failure. Ran the same regex over HEAD~1's training.yaml, before today's notes fix: it matches 'batch_size 25 -> 92', resolves batch_size in the profile, and reads the value as 30 -- i.e. it WOULD have flagged the one real defect. The scanner also exits RC=4 with 'CHECKED NOTHING' if its claim count reaches zero, so a future regex breakage cannot present as a clean bill.

WHAT THIS LEAVES: the config's numeric claims are consistent with its values. Prose notes remain unverifiable, which is a limit of the idiom rather than a defect to fix -- and the one defect that DID exist was numeric, so the checkable subset is also where drift is likely, since a number is what changes when an OOM forces a retreat.
- **2026-09-01T10:17:03+00:00** — DISK WAS AT 100% WITH 551 MB FREE, WHICH WOULD HAVE KILLED CELL 4 AND EVERY QUEUED ARM. Found while checking whether the seed family could finish -- nothing in this campaign watches disk, so no alert fired.

  /dev/nvme0n1p5  113G total, 112G used, 551M free, 100%
  outputs/checkpoints 21G | outputs/adapters 13G | logs 41M

A Phase C adapter bundle is 282 MB, so 551 MB left room for ONE more cell. Cell 4 plus six queued arms would have failed at save time -- and a run that dies while writing has spent its full GPU cost and produced nothing.

THE CULPRIT WAS ONE DIRECTORY AT 2.9 GB, ten times any other:
  outputs/checkpoints/v11lmhead_s42/phase_c/lora/adapter_model.safetensors = 2,956,739,912 bytes
Every other checkpoint is 314-595 MB. This is exp-e9's lmhead-LoRA arm: adapting lm_head means the adapter carries the full vocabulary projection, so the 'lightweight' bundle ends up larger than the base model's own head.

VERIFIED SAFE BEFORE DELETING, four ways:
  - no active driver references it (grepped run_v37..v40, the seed family, both floor runners)
  - its experiment is CLOSED with verdict `refutes` (exp-e9-lmhead-lora-20260814, failure mode recipe_conditional)
  - its SCORED OUTPUTS live separately in logs/ and are untouched: e9_gate_scores_v11lmhead_s42.json, v11lmhead_s42_emissions_n128_summary.json, v11lmhead_s42_emissions_supabase_summary.json
  - kept the two small provenance files before removing the weights (manifest.json, adapter_config.json -> logs/*.kept)

RESULT: 551M -> 3.4G free (98%). Cell 3's scoring survived the delete (pid 351335 still on GPU), all seven units still active.

THE GENERAL POINT, worth more than the 2.9 GB: a refuted arm's WEIGHTS are the disposable part and its CAPTURES are the durable part, and this campaign has it backwards on disk -- 34 GB of checkpoints and adapters against 41 MB of logs. The e9 verdict was reached in August; the weights that produced it have no remaining use, because reproducing the arm means re-running it on current code anyway.

NOT AUTOMATED, deliberately: a sweep that deletes 'refuted' checkpoints would eventually delete something a queued arm chains off. Every deletion here was checked against the live queue by hand.

#optihealth-eiv
