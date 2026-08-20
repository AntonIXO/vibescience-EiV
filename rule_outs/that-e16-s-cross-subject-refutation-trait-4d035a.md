---
created_at: '2026-08-20T09:11:26+00:00'
former_ids: []
id: that-e16-s-cross-subject-refutation-trait-4d035a
problem_id: peri-ema-window-destroys-momentary-labels-20260815
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-20T09:11:26+00:00'
topic_tags:
- state-supervision
- control
- epoch-resolution
---

## Claim
That E16's cross-subject refutation (trait dominance) might be stale on the 12,547 corpus — Anton's challenge, tested by re-running the exact E16 instrument (scratch/e16_peri_ema_window.py) on the new corpus. VERDICT: the CEILING REPRODUCES, the W-PROFILE SHAPE does not. Trait constants are byte-stable across two corpus rebuilds: tense 0.7392 (was 0.7381), tired 0.6743 (was 0.6731); best physiological arm still ~0.60 — the 0.14-0.15 AUC trait gap stands and the cross-subject rule-out SURVIVES. STALE DETAIL: the old claim 'delta rises monotonically with W, peaks at 24h' no longer holds — on the new corpus tense peaks at W=240 (+0.0471) and 1440 drops to +0.0167; tired peaks at 240-480. Do not quote the monotone-rise shape anymore. NEW OBSERVATION worth a hypothesis if state supply grows: dryad_stress_hi has a WEAK trait ceiling (constant 0.5746 vs lifesnaps' 0.74) and hr+clock at W=15 already matches it (0.5745, n=595) — dryad's within-day VAS design largely removes the trait shortcut, making it the least trait-confounded cross-subject target in the corpus.

## Method
Exact re-run of the E16 instrument on the rebuilt corpus; same targets, same trivials, same GroupKFold; only the corpus changed. Anton's standing correction: the corpus changes between hypotheses — re-measure references before quoting them.

## Evidence
logs/e16_rerun_12547.log (2026-08-20, corpus ac7d2b1c, n per W 4079-4549 lifesnaps / 595-4366 dryad). Old reference: exp-e16-peri-ema-window-20260815 on corpus e849eb7.

## Links
Rules out for problem [[peri-ema-window-destroys-momentary-labels-20260815]].

#state-supervision #control #epoch-resolution
