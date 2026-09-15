---
created_at: '2026-09-01T08:56:17+00:00'
former_ids: []
id: including-arousal-in-the-scaled-set-is-unjustified-by-analogy
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:56:17+00:00'
topic_tags: []
---

## Claim
Including `arousal` alongside `valence` in v39scaled's SCALED set is unjustified by analogy: CLAUDE.md states 'Arousal stays CASE-only: POPANE's Response Meter is one-dimensional, so that column is simply masked', so arousal is a single-source channel for which a cross-source axis cannot be verified, and scaling it would either be a no-op or silent label loss.

## Method
Rendered every Phase A roster window's label_summary() for both channels, looked up each source's declared span via label_scaling.declared_range, and compared per-source medians before and after scaling. Reported the spread both with and without popane_s7, since the arm excludes it. No GPU.

## Evidence
CHECKED BEFORE THE ARM REACHED PHASE A, AND THE PREMISE IS OUT OF DATE -- arousal is NOT CASE-only on the current corpus. Measured over the Phase A roster (scratch/_arousal_axis_check.py):

  AROUSAL      windows   declared      native p50   scaled p50
    dapper         548   [1.0, 5.0]         3.00        0.500
    k_emocon        31   [1.0, 5.0]         3.10        0.525
    case            30   [0.5, 9.5]         5.15        0.517
    scaled median spread across 3 sources: 0.025

The CLAUDE.md sentence is correct about what it actually claims -- POPANE's Response Meter is one-dimensional, so POPANE contributes no arousal -- but DAPPER and K-EmoCon do, and DAPPER alone supplies 548 windows, 18x CASE's 30. So arousal has three sources, all declaring ranges, and a cross-source axis is verifiable.

AND IT IS BETTER ALIGNED THAN VALENCE, WHICH IS THE OPPOSITE OF WHAT I EXPECTED WHEN WRITING THE CHECK:
  arousal, 3 sources                       spread 0.025
  valence, 6 sources as the arm ships      spread 0.088
  valence, all 7 sources                   spread 0.148  (fails a 0.12 bar)

THE 0.148 FIGURE IS ALSO A USEFUL CORRECTION TO MY OWN EARLIER NUMBER. This morning I reported valence's six aligned sources at p50 0.490-0.510, a 0.020 spread, computed from RAW LABEL EVENTS. Recomputing through the window-level label_summary() -- the path the caption actually uses, which averages a window's annotated epochs -- gives 0.495-0.583, a spread of 0.088. Both are correct for what they measure; the caption sees the second one. The arm's pre-flight gate was written against the caption path, so it is the 0.088 figure that governs, and it still clears a 0.12 bar.

CONSEQUENCE FOR v39scaled: no change. Both channels in the SCALED set are justified by measurement rather than by analogy, and popane_s7's exclusion is what takes valence from 0.148 (fails) to 0.088 (passes). The arm ships as queued.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
