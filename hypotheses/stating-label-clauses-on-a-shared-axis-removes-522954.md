---
ambition_class: mechanism
created_at: '2026-09-01T08:53:54+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: 'g0 GEOMETRY (blocking, cheap, runs between A and B): eff_rank >= 7.0
    on the Phase A checkpoint, the campaign''s standing veto. Anchors: healthy 8.35,
    carrier-collapse 4.47, v19 5.19. Measured floor sigma 0.076 (probe-repeat). This
    is a VETO not a direction -- v31 posted the highest rank ever measured (10.106)
    and lost deployment, while v34 at 5.414 posted the best supabase numbers. If g0
    fails, stop before B and do not spend the chain.'
  diagnostic_id: eiv_effective_rank
  id: g0-geometry-veto
  mde: 0.152
- blocking: true
  description: 'g1 NO-HARM ON DEPLOYMENT (blocking): dense hr_raw MAE within k=2 of
    the incumbent''s 2.851 at the MEASURED seed floor sigma 0.7566, i.e. [1.34, 4.36].
    Note this band was measured today from three same-recipe seed families; the previously
    asserted [2.23, 3.83] was never measured. Removing a shortcut can legitimately
    COST accuracy -- if it does, that is the price of the shortcut and must be reported
    as such, not hidden.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g1-no-harm-dense
  mde: 0.7566
- blocking: false
  description: 'g2 ADVISORY, deployment population: supabase hr_raw MAE not worse
    than v36labels by more than k=2 at the re-measured seed floor sigma 0.8601 (the
    old 3.145 was measured on arms erroring at ~12 bpm and is 3.66x too wide for today''s
    arms). Advisory because supabase contains none of the datasets whose captions
    changed, so a large move here would indicate something other than the intended
    mechanism.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g2-supabase-advisory
  mde: 0.8601
id: stating-label-clauses-on-a-shared-axis-removes-522954
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Weakly up, and the honest position is that this could go either
    way. Removing a source-identifying signal removes an EASY axis of variance the
    encoder was free to spend capacity on, which should raise effective rank if the
    shortcut was consuming dimensions. But v33/v34 showed caption changes can move
    deployment while leaving rank flat (v33 8.637, v34 5.414, both with better emissions
    than the 8.599 incumbent), so rank may simply not respond. Judged against the
    probe-repeat floor 0.076; anything inside +/-0.152 is no movement.
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: none
  magnitude_note: Predicted FLAT, and this is the gate that matters most for interpretation.
    The dense draw is 97% lifesnaps+pmdata, and NONE of the seven valence sources
    are on the Phase C roster, so the dense population never saw a scaled clause directly.
    Any large dense movement would therefore be an ENCODER-mediated effect propagating
    through B and C -- interesting, but not what the arm set out to test, and it would
    need its own explanation rather than being claimed as the mechanism working.
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
problem_tags:
- optihealth-eiv
- conditioning-collapse
- subject-heldout
- matched-baseline
reopen_reason: ''
reopened_from: null
status: proposed
supersedes: null
topic_tags:
- target-style
- optihealth
- medgemma
- deployment-distribution
- readout
---

## Statement
STATING LABEL CLAUSES ON A SHARED AXIS REMOVES A PROVENANCE SHORTCUT WITHOUT COSTING DEPLOYMENT ACCURACY. Rendering valence and arousal as a percentage of each source's DECLARED span (scale_to_declared_range), with popane_s7 omitted because its dial centre is shifted rather than merely scaled, will hold the Phase A geometry gate (eff_rank >= 7.0) and leave dense hr_raw MAE inside the measured no-harm band [1.34, 4.36] around the incumbent's 2.851.

## Rationale
THE DEFECT IS MEASURED AND ALREADY SHIPPING, NOT HYPOTHETICAL. Running the SHIPPED _label_clauses() over the Phase A roster (scratch/_shipped_clause_provenance.py, 9,428 windows) shows 3,310 windows (35.1%) receive clauses, of which 1,298 carry valence in NATIVE units:
  dapper     548  p50 3.30  (native 1-5)
  popane_s7  213  p50 6.40  (native 0-10)
  popane_s5  199  p50 5.70
  popane_s6  140  p50 4.95
  popane_s3  137  p50 5.40
  k_emocon    31  p50 3.00  (native 1-5)
  case        30  p50 5.15  (native 0.5-9.5)
So 'valence 3.3' is the TOP of DAPPER's range and the BOTTOM of POPANE's. InfoNCE keeps what the two views share, so the encoder is being taught which CORPUS a window came from -- a shortcut this project measured at bal-acc 0.9945 against 0.1250 chance.

THE TRANSFORM ALREADY EXISTS AND ITS DOCSTRING INDEPENDENTLY ARGUES THIS CASE. src/training/label_scaling.py:188 scale_to_declared_range already documents that valence has seven sources on declared spans, that ranking within subject would DESTROY the axis (every CASE session is 41 minutes, fits one 120-minute patch, so a patch mean of within-subject ranks is 0.5 by construction -- measured: raw std 1.76, ranked patch target std 0), and that any arm using it must be scored against a WITHIN-DATASET permutation null. That docstring was written before this audit and reaches the same conclusion from the other direction.

PRE-FLIGHT PASSED BEFORE ANY GPU (scratch/_verify_scaled_clauses.py, three gates):
  g1 valence median spread across sources, normalised to its own axis: 0.340 -> 0.085 (4x narrower)
  g2 popane_s7 valence clauses: 213 -> 0 (omitted, not rescaled)
  g3 windows losing all clauses outside popane_s7: 0
Per-source medians go from 3.00-6.40 native to 49.5-58.0 percent-of-scale.

WHY popane_s7 IS DROPPED RATHER THAN SCALED: its dial has a shifted CENTRE, not a different span (median 0.651 of scale against 0.490-0.510), reproducing from label events alone the documented finding that study 7's resting dial sits higher (6.23 vs 5.48/4.96) with between-subject sd 1.47 vs 0.31. An affine map cannot align a shifted centre, so the clause is omitted instead of stated wrongly. Cost: 213 windows, 16% of the channel.

PRIOR ~40%. Against: the shortcut may be carrying real predictive load, so removing it can cost accuracy; and the dense/supabase draws contain none of the affected datasets, so the effect must survive propagation through B and C to be visible at all. For: this is the first arm in the campaign that makes a label clause mean the same thing across sources, and every previous label result is uninterpretable until it is done. Calibration 0.408; the gates decide.

## Plan


## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (none).
Gate `g0-geometry-veto` (blocking): g0 GEOMETRY (blocking, cheap, runs between A and B): eff_rank >= 7.0 on the Phase A checkpoint, the campaign's standing veto. Anchors: healthy 8.35, carrier-collapse 4.47, v19 5.19. Measured floor sigma 0.076 (probe-repeat). This is a VETO not a direction -- v31 posted the highest rank ever measured (10.106) and lost deployment, while v34 at 5.414 posted the best supabase numbers. If g0 fails, stop before B and do not spend the chain.
Gate `g1-no-harm-dense` (blocking): g1 NO-HARM ON DEPLOYMENT (blocking): dense hr_raw MAE within k=2 of the incumbent's 2.851 at the MEASURED seed floor sigma 0.7566, i.e. [1.34, 4.36]. Note this band was measured today from three same-recipe seed families; the previously asserted [2.23, 3.83] was never measured. Removing a shortcut can legitimately COST accuracy -- if it does, that is the price of the shortcut and must be reported as such, not hidden.
Gate `g2-supabase-advisory` (advisory): g2 ADVISORY, deployment population: supabase hr_raw MAE not worse than v36labels by more than k=2 at the re-measured seed floor sigma 0.8601 (the old 3.145 was measured on arms erroring at ~12 bpm and is 3.66x too wide for today's arms). Advisory because supabase contains none of the datasets whose captions changed, so a large move here would indicate something other than the intended mechanism.

#target-style #optihealth #medgemma #deployment-distribution #readout #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
