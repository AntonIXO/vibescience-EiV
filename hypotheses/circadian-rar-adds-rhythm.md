---
created_at: '2026-07-11T04:03:50+00:00'
former_ids: []
gates: []
id: circadian-rar-adds-rhythm
interventions:
- circadian-rar
paper_refs:
- circadian-accel-2024-healthspan
- hr-temp-circadian-2023
predicted_effects:
- diagnostic_id: insights_yield
  direction: up
  magnitude_note: '>=1 rhythm metric with a datable disruption'
- diagnostic_id: new_deps
  direction: none
  magnitude_note: 0 new deps
problem_id: oh-missing-earlywarning-rhythm
problem_tags:
- missing-circadian
- missing-early-warning
- missing-directionality
status: proposed
supersedes: null
topic_tags:
- circadian
- rest-activity-rhythm
- optihealth
- wearables
- n-of-1
---

## Statement
Nonparametric RAR metrics (IS/IV/RA/M10/L5) + cosinor on intraday hr/steps will quantify Anton's rest-activity rhythm strength and date circadian disruptions the worker currently cannot see.

## Rationale
Intraday hr (4.2M pts/150d) and steps support IS/IV/M10/L5; nothing in the worker computes rhythmicity.

## Plan
Compute IS/IV/RA/M10/L5 on intraday hr; flag weeks of rhythm collapse. (Phase 2 after RHR ships.)

## Links
Tests problem [[oh-missing-earlywarning-rhythm]].
Applies [[circadian-rar]].
Predicts [[insights_yield]] (up).
Predicts [[new_deps]] (none).
Cites [[circadian-accel-2024-healthspan]], [[hr-temp-circadian-2023]].

#circadian #rest-activity-rhythm #optihealth #wearables #n-of-1 #missing-circadian #missing-early-warning #missing-directionality
