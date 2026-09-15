---
created_at: '2026-09-01T09:50:45+00:00'
former_ids: []
id: early-vs-late-presence-3v3-is-a-clean-one-variable-comparison
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:50:45+00:00'
topic_tags: []
---

## Claim
The 3-vs-3 w6avg-vs-w6msk comparison is a clean one-variable test of early vs late presence, and its z +3.11 on supabase is the campaign's first result to clear the full noise treatment.

## Method
Diffed the two families' manifests (5 differing keys), resolved both git_sha values with git log, then read the actual diff of the intervening commit to determine whether it touches the mechanism under test.

## Evidence
RETRACTED BY MY OWN ONE-VARIABLE CHECK, RUN ONE STEP LATER. The two families were trained on DIFFERENT CODE, and the commit between them modifies the exact mechanism under test.

  w6avg s43/s44/s45   git_sha 4f66de892294   2026-08-20 22:49 -> 2026-08-21 00:46
  w6msk s43/s44/s45   git_sha 880c3325c675   2026-08-21 01:50 -> 2026-08-21 03:48

Perfectly confounded: all three control seeds before the commit, all three treatment seeds after. 880c3325c675 is titled 'fix: patch_presence channel layout under with_presence_v1 (24 vs 12)' and touches src/projectors/cross_modal.py, src/training/datasets.py, src/training/pipeline.py and 11 other files -- 1,155 insertions.

WHY THIS DOES NOT SIMPLY INVALIDATE EVERYTHING: reading the actual diff, the cross_modal change sits INSIDE the masked_avg branch. It folds pres_c through value_presence_fusion and swaps patch_presence -> pres_c at the pooling weight. The avg branch calls adaptive_avg_pool2d and never touches presence. So the fix is not a neutral improvement applied to one arm -- it is the code that MAKES masked_avg run at all. The commit message says the three treatment arms CRASHED at step 0 before it ('size of tensor a (24) must match tensor b (12)').

SO THE COMPARISON IS STRUCTURALLY UNAVOIDABLE, NOT CARELESS: masked_avg could not have been trained on the older commit. But it is still not a one-variable test, because datasets.py (+76 lines) and pipeline.py (+41) also moved between the two, and neither is inside the masked_avg branch.

WHAT SURVIVES: the six MAE values are real and the seed spreads are real -- those are what the noise floors were built from and nothing here touches them. What does NOT survive is the ATTRIBUTION. 'Late presence beats early presence by 0.562 bpm' should read 'w6avg-on-4f66de8 beats w6msk-on-880c332 by 0.562 bpm', and the difference between those two statements is 1,155 lines of code.

The rank-sum-0 separation is still striking and still unexplained by seed noise. It is now a REASON TO RUN the arm, not a substitute for running it: w6msk rebuilt on today's HEAD against w6avg on today's HEAD, three seeds each.

METHOD NOTE: this is the fourth time today a result of mine has been undone by checking whether the arms differed in one variable. The check cost two minutes and the claim had already been written into the vault and a git commit.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
