---
created_at: '2026-08-14T20:24:32+00:00'
former_ids: []
id: vault-hygiene-and-record-readability
paper_refs: []
problem_tags:
- optihealth-eiv
status: open
title: vault hygiene and record readability
topic_tags: []
updated_at: '2026-08-14T20:24:32+00:00'
updates: []
---

## Description
The experiment log became hard to read and partly untrustworthy. Six measured defects as of 2026-08-14:

1. ID chaos — two incompatible naming schemes; 89 records carried an id over 60 chars, auto-slugged from a full sentence and hard-truncated at 80 mid-word, so `ls experiments/` was useless and neighbouring ids were identical for their first 60 characters.
2. Verdict semantics lied on powered nulls — sign-only comparison called delta=+0.0003 at permutation p=0.65 `directional_only`, and the verdict rank put that ABOVE `inconclusive`.
3. Status rot — 15/17 problems `open` (several already answered), 8 hypotheses permanently `proposed`, 2 stuck in `testing` since July, 10 runbooks `queued` with no staleness signal.
4. `notes` was a dumping ground — close_experiment took only {experiment_id, notes}, so artifacts, key numbers and do-not-retry warnings were compressed into unqueryable prose. `artifacts` existed on the model but had no way in through the API.
5. Provenance pointed at the wrong repo — _git_ref ran git inside the VAULT (itself a repo), so a run made on fix/soft-token-norm-scale was logged as main@d3f379f. No dirty-tree flag either.
6. Tag sprawl — 69 tags over 62 experiments with no merge operation, so the vocabulary could only grow.

#optihealth-eiv
