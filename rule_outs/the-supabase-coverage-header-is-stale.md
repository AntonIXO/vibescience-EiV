---
created_at: '2026-09-01T10:46:33+00:00'
former_ids: []
id: the-supabase-coverage-header-is-stale
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:46:33+00:00'
topic_tags: []
---

## Claim
The problem header's 'optihealth_supabase, mean HR coverage 0.789' is stale or wrong -- the rows actually being scored read median 0.349 and never exceed 0.598.

## Method
Read the emission scorer's bundle constant, loaded both bundles, and reproduced the deterministic rng(0) draw to compare its index set and coverage against the capture on disk.

## Evidence
THE HEADER IS EXACTLY RIGHT AND I JOINED AGAINST THE WRONG BUNDLE (scratch/_supabase_coverage_definition.py). The emission scorer reads EV = Path('data/eval_v3_1min'), NOT data/canonical. I computed per-row coverage by indexing data/canonical's M_mask with the capture's `idx` values, which are indices into the EVAL bundle. Two different tensors, same integer keys, no error raised -- the join silently addressed unrelated windows.

REPRODUCED THE DRAW EXACTLY to settle it. The scorer uses np.random.default_rng(0) over the dataset's pool, so it is deterministic:
  eval bundle supabase pool: 230 windows
  rng(0) draw of 200 -> HR coverage mean 0.772, median 0.789
  capture's own idx set: n=200, min 4663, max 4892
  reproduced set == capture set: True

So the scored rows have HR coverage 0.789 at the median. The header's figure is the right number for the right population, and my 0.349 was data/canonical windows 4663-4892, which belong to some other dataset entirely.

THE TWO BUNDLES DIFFER SUBSTANTIALLY, which is why the error produced a plausible-looking result rather than nonsense:
  data/canonical    X(14041,1440,12)  supabase 302  HR cov mean 0.661 median 0.739
  data/eval_v3_1min X( 6918,1440,12)  supabase 230  HR cov mean 0.776 median 0.790

WHAT THIS RETRACTS: the previous entry's claim that 'the deployment population is UNIFORMLY SPARSE (median 0.349, max 0.598), so the coverage bias is a near-constant offset on every row'. That was built on the wrong rows. The real draw spans a normal coverage range and the uniform-sparsity account is withdrawn.

WHAT SURVIVES UNTOUCHED: the target-side measurement. The subsampling test held windows FIXED and thinned their own masks -- it never joined across bundles, so -5.3 bpm at 40% coverage and -8.6 at 20% stand. The hr_peak skill numbers stand too; they come from the capture files themselves.

WHAT IS NOW UNTESTED AGAIN: whether the emitted peak tracks coverage on supabase. Those five negative slopes were regressions against the wrong covariate and mean nothing. The dense slopes (+7.16, +21.11) are equally invalid -- same bad join.

RULE THIS COSTS: an emission capture's `idx` is an index into the bundle the SCORER read, and this repo has three canonical-shaped bundles. Before joining a capture to any tensor, read the scorer's EV constant. A join that silently succeeds against the wrong array of the same shape is the most dangerous kind, because every downstream number looks reasonable.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
