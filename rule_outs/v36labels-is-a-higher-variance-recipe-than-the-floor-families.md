---
created_at: '2026-09-01T09:33:48+00:00'
former_ids: []
id: v36labels-is-a-higher-variance-recipe-than-the-floor-families
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T09:33:48+00:00'
topic_tags: []
---

## Claim
v36labels is a higher-variance recipe than the families the dense floor was measured on: its own two seeds span sd 0.843, exceeding the registered floor of 0.7566, so that floor is too permissive for judging it.

## Method
Derived the half-normal sampling distribution of an n=2 sd, then read every seed PAIR inside the three families the floor was measured on and counted how often a pair alone would report an sd above the pooled floor. No GPU.

## Evidence
REFUTED BY THE SAMPLING DISTRIBUTION OF AN n=2 STANDARD DEVIATION, and the empirical rate matches the theory almost exactly (scratch/_two_seed_sd_reliability.py).

An sd from two observations is just |a-b|/sqrt(2). If the true sd is exactly sigma, that estimate is HALF-NORMAL with scale sigma, so
  P(2-seed estimate > sigma) = 2*(1 - Phi(1)) = 0.317
i.e. a two-seed sd exceeds the true sigma about one time in three BY CHANCE, even for an arm exactly as noisy as the families the floor came from.

CHECKED AGAINST THE FLOOR'S OWN FAMILIES, read two seeds at a time:
  ab_ctrl   [10.049, 2.562, 10.168]  pair sds 5.294*, 0.084, 5.379*
  w6avg     [3.560, 2.642, 3.334]    pair sds 0.649, 0.160, 0.489
  w6msk     [3.947, 2.439, 3.301]    pair sds 1.066*, 0.457, 0.609
  (* exceeds the 0.7566 floor)
  EMPIRICAL: 3 of 9 pairs = 0.333, against the predicted 0.317.

v36labels' 0.843 is one such pair. It is 1.11x the floor -- well inside the range a two-seed estimate wanders even when nothing is unusual. The same families that PRODUCED the floor generate pair-estimates of 5.294 and 5.379 when read this way.

WHAT I ALMOST DID WRONG. I reported this morning that v36labels' own-seed spread 'EXCEEDS the floor it is judged by', with the implication that the floor is too narrow for this arm. That sentence is arithmetically true and inferentially empty: at n=2 it is the expected outcome a third of the time. Had I widened the floor on this basis, every future comparison against v36labels would have been made unfalsifiable -- the exact failure I avoided this morning when I refused to pool the two scale-broken ab_ctrl seeds into the floor.

WHAT WOULD ACTUALLY SETTLE IT: the third seed. Cell 2 of the family produced v36labels s44 on supa but its dense capture is still pending, so a 3-seed dense sd for this arm is one capture away and costs nothing extra -- it is already in the queue.

STANDING RULE THIS EARNS: never compare a 2-seed sd against a pooled floor and conclude anything about variance. Report the pair difference, wait for n=3, or state the comparison as uninformative.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
