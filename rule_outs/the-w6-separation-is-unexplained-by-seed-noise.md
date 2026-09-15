---
created_at: '2026-09-01T09:52:22+00:00'
former_ids: []
id: the-w6-separation-is-unexplained-by-seed-noise
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:52:22+00:00'
topic_tags: []
---

## Claim
The w6avg-vs-w6msk supabase separation (delta +0.562) is unexplained by seed noise even though it is confounded, so it remains a strong reason to rerun the arm on one commit.

## Method
Built a comparison tool that gates on manifest provenance before scoring, and included the registered seed floor in the standard error rather than the within-family spread alone.

## Evidence
THE SEPARATION IS NOT BEYOND SEED NOISE EITHER. I built a guard that refuses to score confounded families (scratch/_family_compare.py), ran it on this pair, and it caught a THIRD error in the same comparison -- one that has nothing to do with the confound.

My earlier script computed SE from the three-seed spreads alone: SE 0.181, z +3.11. That treats the observed sd of three retrains as the whole retrain variance. But the registered supabase seed floor is 0.8601, measured across families precisely because a within-family sd of n=3 underestimates it -- the same n=2 sampling-distribution argument I made this morning, one step weaker.

Folding the registered floor in:
  SE = sqrt(0.181^2 + 0.8601^2 * (1/3 + 1/3)) = 0.725
  delta +0.562  ->  z +0.78  -- INSIDE NOISE

So the honest reading is not 'confounded but striking'. It is 'confounded AND inside noise'. The rank-sum-0 separation that impressed me is what three-vs-three looks like when each side's spread is 0.21-0.23 but the true retrain sigma is 0.86: the families are tight internally and that tightness is itself a small-n artefact.

ALSO SURFACED BY THE GUARD, unrelated to either point: w6avg s43 emits at scale ratio 0.62, far outside the [0.85, 1.35] band where the floor is valid. Under the campaign's own rule -- scale ratio first, then MAE -- that seed's 3.187 should not have entered a mean at all. Two of the six captures in the comparison are at or past a band edge.

WHAT THIS LEAVES: no evidence either way on early-vs-late presence. Not a weak effect, not a confounded effect -- no effect distinguishable from retrain noise at n=3 per side. Running the arm cleanly is justified by the MECHANISM being untested, not by this delta.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
