---
created_at: '2026-09-01T10:37:08+00:00'
former_ids: []
id: v36labels-seed-spread-is-a-calibration-instability
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:37:08+00:00'
topic_tags: []
---

## Claim
v36labels' seed spread is a CALIBRATION instability rather than a reading instability -- its seeds agree about the shape of the signal and disagree about the amplitude, so removing the gain variation would remove most of the MAE spread.

## Method
For each capture, rescaled emissions to unit gain about their own mean using the true spread, re-scored MAE, and compared per-arm seed sd before and after; also correlated |scale-1| with MAE across all 16 captures.

## Evidence
ONE THIRD TRUE ON DENSE, AND FALSE EVERYWHERE ELSE (scratch/_calibration_vs_mae_spread.py). Rescaled every capture's emissions to unit gain about their own mean and re-scored, then compared the seed spread before and after.

  arm          draw   n   MAE sd   scale sd   MAE@gain=1 sd
  v36labels    supa   3    0.345      0.100         0.314
  v36labels    dense  3    0.849      0.218         0.581
  v28affect01  supa   2    0.025      0.034         0.025
  v28affect01  dense  2    0.050      0.031         0.036
  w6avg        supa   3    0.230      0.154         0.203
  w6msk        supa   3    0.213      0.175         0.317

ON DENSE the claim has real content: v36labels' MAE sd falls 0.849 -> 0.581, so about a third of its dense seed spread is gain. That is the largest such effect in the table and it is the draw where two of its three seeds sit at or past the band edge (1.32, 1.38).

EVERYWHERE ELSE IT FAILS. On supa v36labels barely moves (0.345 -> 0.314). w6msk gets WORSE when rescaled (0.213 -> 0.317) -- correcting the gain INCREASES its seed spread, which is the opposite of what a gain-instability account predicts. The incumbent has nothing to explain either way (0.025, 0.050).

WHAT IS REAL AND WORTH KEEPING: corr(|scale - 1|, MAE) = +0.493 over 16 captures. Miscalibration and error do travel together, which is the quantitative basis for the campaign's standing rule to read the scale ratio BEFORE the MAE. But +0.49 over 16 points is a tendency, not a decomposition -- it does not license treating MAE as 'gain error plus reading error'.

AND THE PER-CAPTURE COLUMN SHOWS WHY THE AVERAGE HIDES THINGS. The gain cost ranges from -0.145 to +0.495. v36labels dense s42 is the extreme: MAE 4.017 at scale 1.32, and rescaling recovers 0.495 bpm -- so its status as v36's worst dense seed is substantially an amplitude problem. But w6avg's captures all have NEGATIVE gain cost (-0.073, -0.133, -0.119): they emit at 0.62-0.91 and rescaling makes them worse, because under-expression was partially compensating for a bias.

SO THE HONEST SUMMARY: calibration and accuracy are correlated but not decomposable, and a per-arm gain correction is not the lever it looked like. What survives is narrower and already known -- read the scale ratio first, and treat a capture outside [0.85, 1.35] as not comparable rather than as merely worse.

METHOD NOTE, so nobody reads MAE@gain=1 as a proposal: the rescaling uses the TRUE spread of the evaluation rows. Production has no such quantity. It is a decomposition tool, not a fix.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
