---
created_at: '2026-08-05T15:47:14+00:00'
former_ids: []
id: ro-sensor-slot-granularity-20260805
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: true
retraction_reason: "RETRACTED 2026-08-08 — its stated MECHANISM is disproven by two
  other records in this same vault, and it was missed by the 11:13-11:27 retraction
  sweep.\n\nTHE DEFECT. Its Evidence section closes with: \"The information is already
  destroyed upstream at patch construction, so no amount of downstream slot tuning
  can recover it.\" That premise is now EXACTLY DISPROVEN:\n  (a) the patch-construction
  rule_out's surviving half re-derived from scratch that max over the 23 per-patch
  maxima equals the true daily peak to 0.0000000000 bpm on all 6624 strict-clean days,
  with the unfold(120,60)+Linear(120->192) operator at rank 1440/1440, cond 5.47 —
  a 1.6x EXPANSION, not a compression. Nothing is destroyed at patch construction.\n
  \ (b) ro-dual-avg-max-pooling-downstream-20260805 independently eliminated the same
  framing as a corollary: peak information is \"present and linearly decodable at
  the exact tensor that becomes the soft-token prefix\" (R2 0.8084, increment +0.7211).\nSo
  the rule_out reached a conclusion via a mechanism its own neighbours refute. The
  observed flatness of the slot sweep (0.4659 at 23 slots -> 0.4573 at 1) is a real
  measurement and STANDS, but it does not license the causal claim attached to it:
  slot count being non-limiting is not evidence that upstream destroyed the signal
  — the opposite is now established.\n\nTWO FURTHER REASONS IT CANNOT STAND AS FILED:\n
  1. 15-MINUTE-ERA EVIDENCE. \"Same ridge probe, fixed 24h window, sweeping projector
  slot count 23/12/8/4/2/1\" was measured on the 15-minute corpus/checkpoint, where
  the daily peak had already been attenuated by a median 19.13 bpm on 98.2% of days.
  R2(TRUE 1-minute peak <- 15-minute peak) is only 0.7327, so a 15-minute instrument
  cannot bound a 1-minute question. This is the identical inconsistency the patch-construction
  rule_out was retracted for.\n 2. ENCODER READOUT vs PIPELINE. It is a ridge probe
  on pooled slots — encoder readout — used to close a claim about what the trained
  pipeline can recover. Per the 2026-08-08 retraction wave, that inference is not
  valid here: Phase A->B->C is sequential and compounding, and the actual failure
  is in GENERATION.\n\nWHAT SURVIVES: the narrow, resolution-scoped measurement that
  at 15 minutes, compressing 23 slots to one cost only ~0.009 R2 — i.e. slot COUNT
  was not the binding constraint on that grid.\n\nRE-FILE NARROWLY IF WANTED: \"at
  15-minute resolution, projector slot count is not the binding constraint on encoder-side
  peak readout.\" Do not re-file the upstream-destruction mechanism; it is false."
ruled_out_at: '2026-08-05T15:47:14+00:00'
topic_tags: []
---

## Claim
Tuning the projector's sensor-slot count / pooling granularity will recover the lost peak information.

## Method
Same ridge probe, fixed 24h window, sweeping projector slot count 23/12/8/4/2/1.

## Evidence
R2->max moves only 0.4659 (23 slots) -> 0.4330 (12) -> 0.4460 (8) -> 0.4528 (4) -> 0.4548 (2) -> 0.4573 (1). Compressing 23 slots to ONE costs ~0.009. The information is already destroyed upstream at patch construction, so no amount of downstream slot tuning can recover it.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

**RETRACTED:** RETRACTED 2026-08-08 — its stated MECHANISM is disproven by two other records in this same vault, and it was missed by the 11:13-11:27 retraction sweep.

THE DEFECT. Its Evidence section closes with: "The information is already destroyed upstream at patch construction, so no amount of downstream slot tuning can recover it." That premise is now EXACTLY DISPROVEN:
  (a) the patch-construction rule_out's surviving half re-derived from scratch that max over the 23 per-patch maxima equals the true daily peak to 0.0000000000 bpm on all 6624 strict-clean days, with the unfold(120,60)+Linear(120->192) operator at rank 1440/1440, cond 5.47 — a 1.6x EXPANSION, not a compression. Nothing is destroyed at patch construction.
  (b) ro-dual-avg-max-pooling-downstream-20260805 independently eliminated the same framing as a corollary: peak information is "present and linearly decodable at the exact tensor that becomes the soft-token prefix" (R2 0.8084, increment +0.7211).
So the rule_out reached a conclusion via a mechanism its own neighbours refute. The observed flatness of the slot sweep (0.4659 at 23 slots -> 0.4573 at 1) is a real measurement and STANDS, but it does not license the causal claim attached to it: slot count being non-limiting is not evidence that upstream destroyed the signal — the opposite is now established.

TWO FURTHER REASONS IT CANNOT STAND AS FILED:
 1. 15-MINUTE-ERA EVIDENCE. "Same ridge probe, fixed 24h window, sweeping projector slot count 23/12/8/4/2/1" was measured on the 15-minute corpus/checkpoint, where the daily peak had already been attenuated by a median 19.13 bpm on 98.2% of days. R2(TRUE 1-minute peak <- 15-minute peak) is only 0.7327, so a 15-minute instrument cannot bound a 1-minute question. This is the identical inconsistency the patch-construction rule_out was retracted for.
 2. ENCODER READOUT vs PIPELINE. It is a ridge probe on pooled slots — encoder readout — used to close a claim about what the trained pipeline can recover. Per the 2026-08-08 retraction wave, that inference is not valid here: Phase A->B->C is sequential and compounding, and the actual failure is in GENERATION.

WHAT SURVIVES: the narrow, resolution-scoped measurement that at 15 minutes, compressing 23 slots to one cost only ~0.009 R2 — i.e. slot COUNT was not the binding constraint on that grid.

RE-FILE NARROWLY IF WANTED: "at 15-minute resolution, projector slot count is not the binding constraint on encoder-side peak readout." Do not re-file the upstream-destruction mechanism; it is false.
