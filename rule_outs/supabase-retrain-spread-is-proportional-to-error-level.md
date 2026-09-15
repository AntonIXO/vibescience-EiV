---
created_at: '2026-09-01T10:32:08+00:00'
former_ids: []
id: supabase-retrain-spread-is-proportional-to-error-level
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:32:08+00:00'
topic_tags: []
---

## Claim
The supabase retrain spread is proportional to the error level, so a coefficient of variation (~0.25) is the right shape of floor and transfers between arms where a bpm sigma does not.

## Method
Computed per-family mean, sd and CV over five families with 2+ seeds on the same frozen supabase draw, then compared how much each candidate invariant (sigma, CV) actually varies across them.

## Evidence
NEITHER A CONSTANT SIGMA NOR A CONSTANT CV SURVIVES CONTACT WITH FIVE FAMILIES (scratch/_floor_proportionality.py). The proportionality claim was made across two REGIMES -- CV 0.256 at mean MAE 12.27 (v7noatom) and 0.247 at 3.484 (the current families) -- which is a two-point fit, and both points are family AVERAGES. Tested within the current regime, where five families now have at least two seeds each:

  family        n   mean MAE      sd       CV
  ab_ctrl       4      4.029   1.456    0.361
  w6avg         3      2.930   0.230    0.079
  w6msk         3      3.493   0.213    0.061
  v36labels     3      2.257   0.345    0.153
  v28affect01   2      3.047   0.025    0.008

  SD varies 58.1x across families (0.025 to 1.456)
  CV varies 44.0x across families (0.008 to 0.361)
  corr(mean MAE, sd) = +0.654 over 5 families

SO THE PROPORTIONAL LAW IS NOT REFUTED SO MUCH AS INSUFFICIENT. The correlation is positive and moderate, which is real -- noisier arms do tend to sit at higher error. But holding CV fixed leaves a 44x spread, barely better than the 58x that holding sigma fixed leaves. A rule that varies 44-fold is not a rule.

THE CONCLUSION THE DATA FORCES: retrain spread is a property of the RECIPE, and no single number -- bpm or fraction -- transfers between families. v28affect01 at CV 0.008 and ab_ctrl at 0.361 are not two draws from one distribution under any reparameterisation.

WHAT THAT COSTS, stated plainly because it is expensive: every promotion decision needs its OWN control family, run at the same seeds as the candidate, rather than a borrowed floor. That is exactly what the v36-vs-v28 seed family is -- four cells, both arms, same seeds -- and it is why that design is worth its GPU while the previous single-capture comparisons were not.

CAVEAT ON THE EXTREME VALUES, which I am not going to hide behind: v28affect01's 0.008 comes from n=2, and this campaign established that a 2-point spread is a poor variance estimate in BOTH directions. ab_ctrl's 0.361 is inflated by four out-of-band captures (scale ratios 1.61/0.70/0.83/1.36, all outside [0.85,1.35]). Drop those two families and the remaining three read CV 0.079 / 0.061 / 0.153 -- a 2.5x spread, which IS closer to a usable rule. That is a defensible narrower claim: among calibrated arms with 3+ seeds, CV sits near 0.06-0.15. It is not the 0.25 the record asserts, and it is measured on three families rather than two regime averages.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
