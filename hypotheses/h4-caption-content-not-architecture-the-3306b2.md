---
ambition_class: null
created_at: '2026-08-08T00:27:55+00:00'
former_ids:
- h4-caption-content-not-architecture-the-hr-peak-deficit-is-a-supervision-deficit
gap_closure_estimate: null
gates: []
id: h4-caption-content-not-architecture-the-3306b2
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_peak_excursion_increment_r2
  direction: up
  magnitude_note: ''
- diagnostic_id: eiv_heldout_hr_r2
  direction: none
  magnitude_note: ''
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
problem_tags: []
reopen_reason: ''
reopened_from: null
status: inconclusive
supersedes: null
topic_tags:
- cross-modal
- sensor-text
- contrastive
- infonce
- baseline
- epoch-resolution
- dataset-whitelist
- wearables
---

## Statement
H4 (caption content, not architecture). The hr_peak deficit is a SUPERVISION deficit: the
captions describe LEVEL and not SPREAD, so a contrastive objective aligned against them learns level
almost perfectly and excursion poorly. Putting within-day dispersion and temporal structure INTO the
caption will raise hr_peak readout materially, and no further architectural change is needed to get it.

EVIDENCE THAT MOTIVATES IT (measured today, matched subject-grouped protocol, n~6600, 123 subjects):
  target    what it is   deep-stack R2   MAE vs trivial
  hr_mean   LEVEL        0.9713          1.145 vs 7.602 bpm   <- essentially solved
  hr_peak   EXCURSION    0.4209         12.797 vs 16.972 bpm  <- half the skill of numpy scalars
and the ladder on hr_peak: level only 0.0226 -> caption scalars 0.1242 -> DEEP STACK 0.4209 ->
hand dispersion 0.7075 -> patch maxes + HistGB 0.9706 (the information bound). Adding exactly five
scalars to the shallow feature set - hr_std, hr_p25, hr_p75, hr_iqr, fraction of minutes above the
day's own 75th percentile - moves R2 from 0.1242 to 0.7075. Those five are absent from caption_style
hr_only.

MECHANISM. Contrastive alignment captures the mutual information between the two views and discards
what is not shared. If the text view contains only level, then spread is by construction NOT shared,
and more alignment training cannot help - it is the guaranteed-failure regime for plain cross-modal
contrastive learning, not a tuning problem. This also explains, without any new assumption, why every
architectural intervention tried so far came back flat or refuted: pooling operator, slot count,
window length and patch construction are all downstream of an objective that was never asked to
preserve spread. Patch construction was independently proved lossless for the peak (max over patch
maxima equals the true daily peak to 0.0000 bpm), so the information IS present at the tokens and is
simply not being selected for.

WHAT TO CHANGE, CONCRETELY: extend the caption with (a) within-day dispersion in physical units -
the peak itself, the 25th/75th percentiles, and time spent above a personal threshold; (b) WHEN the
peak occurred (hour of day) and the nocturnal-vs-diurnal split, which is temporal structure rather
than more decimals; (c) at least one conjunctive statement, so the objective has a reason to
represent a combination rather than two independent marginals.

FALSIFICATION, STATED UP FRONT. If enriching the caption does NOT move hr_peak readout, then the
deficit is architectural or optimisation-bound after all and the supervision story is wrong. A
particularly informative negative would be hr_peak readout rising while hr_mean readout FALLS, which
would mean the caption is a fixed-capacity channel being reallocated rather than an information
ceiling being raised - I will measure both and report the pair.

CONTROL REQUIRED: the level-only R2(hr_peak <- day mean) control must be recomputed on the same pool
and quoted alongside, because a caption that merely restates level would raise the raw number without
raising the increment.

## Rationale


## Plan


## Links
Tests problem [[the-15-min-1-min-epoch-migration-3e7d2d]].
Predicts [[eiv_peak_excursion_increment_r2]] (up).
Predicts [[eiv_heldout_hr_r2]] (none).

#cross-modal #sensor-text #contrastive #infonce #baseline #epoch-resolution #dataset-whitelist #wearables
