---
created_at: '2026-08-05T15:47:14+00:00'
former_ids: []
id: ro-window-shortening-20260805
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-05T15:47:14+00:00'
topic_tags: []
---

## Claim
Shortening the analysis window (24h -> 12h/6h/2h/1h) will improve hr_peak conditioning by giving the encoder less context to compress.

## Method
RidgeCV probe on n=4010 clean fully-observed days (coverage>0.9, mean>20bpm), 70/30 split, sweeping sub-window length. Critically, each raw R2->max was paired with the control R2(max | true mean alone, 1 feature).

## Evidence
Raw R2->max nearly doubles 0.4659 (24h) -> 0.8385 (1h), but the control rises IDENTICALLY: increment +0.0053 @24h, +0.0708 @12h, +0.0258 @6h, +0.0096 @4h, and EXACTLY 0.0000 @2h and @1h. Every point of apparent gain is 'max converges to mean in a short window'. It also destroys the label distribution the bucket rule depends on: tachycardic base rate 18.3% -> 1.4%, elevated 30.0% -> 4.0%, leaving 3 of 4 classes near-extinct. This would convert a conditioning-collapse problem into a class-imbalance problem WHILE the entropy gate improved.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].
