---
created_at: '2026-09-01T10:07:05+00:00'
former_ids: []
id: an-unrendered-label-channel-is-worth-adding
problem_id: phase-c-arms-fail-on-the-deployment-9217e0
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:07:05+00:00'
topic_tags: []
---

## Claim
There remains an un-rendered label channel worth adding to _label_clauses -- some channel lands on rows the renderer currently leaves bare.

## Method
For each un-rendered label channel, counted windows carrying it that have ZERO rendered clauses, rather than counting reach -- separating new supervision from extra text on already-supervised rows.

## Evidence
THE RENDERER IS CLOSED. Measured NEW-ROW gain rather than reach: for every label channel _label_clauses does not render, how many windows carrying ZERO rendered clauses would it light up (scratch/_unrendered_channel_gain.py).

Wide roster, 9,530 windows, 6,118 (64.2%) currently bare:
  age                863 reach ->  178 new rows  2.91%   [dreamt, popane_s4]
  hba1c              145 reach ->  145 new rows  2.37%   [big_ideas_glycemic]
  food_log_entries   145 ->  145  2.37%
  avg_daily_sugar/fat/carbs/calories/protein  137 -> 137 each  2.24%
  sex_code           767 ->   78  1.27%   [popane_s4]
  mean_sao2/bmi/arousal_index/oahi/ahi  100 -> 100 each  1.63%   [dreamt]
  valence_recalled   486 ->   42  0.69%   [dapper]
  arousal_recalled   486 ->   42  0.69%
  couple_id          199 ->    0  0.00%

BEST CASE IS 178 OF 6,118 BARE ROWS -- 2.91%. And that best case is `age`.

THE GAP BETWEEN REACH AND NEW-ROW IS THE FINDING. age reaches 863 windows but only 178 are bare; sex_code reaches 767 and lights up 78; valence_recalled reaches 486 and lights up 42; couple_id reaches 199 and lights up ZERO. Every high-reach un-rendered channel co-occurs with labels the renderer ALREADY emits. Adding them puts more text on rows that are already supervised.

AND THE CANDIDATES ARE THE WRONG KIND OF THING. age, sex_code, bmi, hba1c, ahi, oahi, mean_sao2, the five avg_daily_* nutrition fields, couple_id -- these are SUBJECT TRAITS and clinical constants, not observations about the epoch the window covers. They are constant across every window of a subject, so a clause stating them is a subject identifier the model can memorise, which is the leakage pattern this project already rejected for split keys. valence_recalled/arousal_recalled are genuine epoch observations but land on 42 bare rows, 0.69%.

SO THE CAPTION-SIDE LABEL THREAD IS COMPLETE. 13 channels are rendered; every remaining channel is either a subject trait, or co-occurs with something already rendered, or reaches under 1% of bare rows. The 64.2% of windows with no clause are bare because their DATASET carries no epoch annotations at all -- not because the renderer is missing a case.

WHAT THIS LEAVES AS THE ONLY LIVE LEVER: the corpus. Which annotated datasets are admitted to a roster, and at what dilution cost to the channels already present (measured earlier: widening 7,592 -> 9,530 drops the six pmdata channels from 21.9% to 17.5%). Renderer work is done.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-9217e0]].
