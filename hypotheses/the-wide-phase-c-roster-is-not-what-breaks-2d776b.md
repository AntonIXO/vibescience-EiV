---
ambition_class: mechanism
created_at: '2026-09-01T09:12:57+00:00'
former_ids: []
gap_closure_estimate: null
gates:
- blocking: true
  description: 'g1 BLOCKING, deployment accuracy: dense hr_raw MAE inside [1.34, 4.36],
    i.e. k=2 at the seed floor 0.7566 MEASURED today around the incumbent''s 2.851.
    The four confounded arms sat at 21.3-24.6, so a roster effect of that magnitude
    would be unmissable; a small effect is what this gate is actually resolving.'
  diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  id: g1-dense-no-harm
  mde: 0.7566
- blocking: true
  description: 'g2 BLOCKING, calibration: emitted/true SD ratio inside [0.85, 1.35]
    on the dense draw. THIS IS THE GATE THAT NAMES THE KNOWN FAILURE MODE. All four
    confounded wide-roster arms are outside it -- v11full 0.57 and v12conly 0.48 emit
    a COMPRESSED range (regressing toward a constant), v13flat 2.03 and v14long 2.76
    over-express it. MAE alone would report ''worse'' without saying the arms had
    stopped tracking the target at all, and the dense noise floor is only valid inside
    this band.'
  diagnostic_id: eiv_gen_between_sample_std
  id: g2-calibration-domain
  mde: 3.0506
- blocking: false
  description: 'g3 ADVISORY, deployment population: supabase hr_raw MAE not worse
    than the incumbent''s 3.030 by more than k=2 at the re-measured floor 0.8601.
    Advisory rather than blocking because optihealth_supabase is itself on BOTH rosters,
    so the supa draw is the least sensitive place to see a roster effect.'
  diagnostic_id: eiv_supabase_hr_raw_mae_bpm
  id: g3-supa-advisory
  mde: 0.8601
id: the-wide-phase-c-roster-is-not-what-breaks-2d776b
interventions: []
override_reason: ''
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_dense128_hr_raw_mae_bpm
  direction: up
  magnitude_note: 'Predicted WORSE (higher MAE), which is why the prior is under 50%.
    The affect corpora are 40-minute lab sessions (CASE 41 min, POPANE ~40 min) against
    a dense draw of full wearable days, and they enter at sampler weights 2.0-7.0,
    so short-session windows take a much larger share of the emitter''s training mass
    than their window count suggests. If the effect is real it should exceed the k=2
    band of 1.513 bpm. A move INSIDE the band is the informative outcome: it would
    mean the four confounded arms failed for one of their other four keys.'
- diagnostic_id: eiv_gen_between_sample_std
  direction: down
  magnitude_note: 'Predicted DOWN if the roster is the cause: v11full and v12conly
    both COMPRESS the emitted range (0.57 and 0.48), which is the signature of an
    emitter regressing toward a constant when its training population no longer matches
    what it is asked to describe. v13flat and v14long go the other way (2.03, 2.76),
    so the direction is not consistent across the four -- another reason their shared
    outcome is not one effect. Judged against the registered floor 3.0506 bpm, which
    is wide enough that only a gross change is detectable.'
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
THE WIDE PHASE-C ROSTER IS NOT WHAT BREAKS DEPLOYMENT. Training Phase C with dataset_filter widened from 6 to 20 datasets, and every other key held at the incumbent's value (seed 42, the shipped json_slots prompt, band04's patch_pool_mode and hr_coverage_denominator), will keep dense hr_raw MAE inside the measured no-harm band [1.34, 4.36] and keep the emitted/true SD ratio inside [0.85, 1.35].

## Rationale
THE QUESTION IS UNTESTED, NOT UNTOUCHED, AND THAT DISTINCTION IS THE WHOLE POINT. Four profiles already carry a wide roster into Phase C -- lora_proxy_12ch_v11full, _v12conly, _v13flat, _v14long -- and all four are catastrophic on deployment: dense hr_raw MAE 21.3-24.6 against the incumbent's 2.851, z +24 to +29 against the seed floor measured 2026-09-01. Read casually that is a verdict on the roster.

It is not. Dict-diffed against band04 (scratch/_wide_roster_variable_check.py), every one of those arms differs in FIVE keys:
  dataset_filter           6 -> 20 datasets
  hr_coverage_denominator  <absent> -> recording
  patch_pool_mode          <absent> -> masked_avg
  prompt_path              <absent> -> prompts/json_slots_prompt_fit256.md
  seed                     42 -> 43
Two are separately load-bearing. prompt_path swaps the truncated 317-token json prompt for a 250-token one that fits whole -- exactly the variable v38prompt is queued to isolate. And seed 42->43 alone spans the Phase-C seed floor: same-recipe ab_ctrl seeds land 2.562 to 10.168 bpm on dense. So 'the roster fails' and 'one of the other four fails' predict the identical table, and four arms agreeing is one test repeated, not four independent tests.

WHY IT GATES THE STANDING GOAL ('utilise all labels in all three phases'). Measured per-channel reach (scratch/_phase_c_label_ceiling.py): on band04's roster valence reaches ZERO windows and arousal ZERO; on the wide roster valence reaches 1,298 windows from SEVEN sources and arousal 609 from three. Phase C cannot carry an affect label in any form until this question is settled -- it is the gate, not a side quest.

ONE VARIABLE, ENFORCED STRUCTURALLY: the new profile inherits &band04_base and replaces dataset_filter alone, verified by verify_one_variable.py (diff=['dataset_filter'] PASS). A near-miss is recorded in the config: inheriting &v11_full_filter alone yields 19 datasets, because that anchor is the PHASE A roster which excludes nurse_stress on purpose (shift-schedule-carried state labels, clock-alone AUC 0.6358) while the v11 block keeps it in C precisely because C is json-slot supervised rather than state-scored. Inheriting blindly would have smuggled a second variable in through an anchor.

PRIOR ~35%, i.e. I expect this to FAIL and the roster to be genuinely costly. The affect corpora are short lab sessions against a production population of full wearable days, and they enter at sampler weights 2.0-7.0 -- their share of the emitter's training mass is far larger than their window count. Calibration 0.408; the gates decide, and a failure here is as useful as a pass because it retrospectively explains four expensive arms.

## Plan


## Links
Tests problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
Predicts [[eiv_dense128_hr_raw_mae_bpm]] (up).
Predicts [[eiv_gen_between_sample_std]] (down).
Gate `g1-dense-no-harm` (blocking): g1 BLOCKING, deployment accuracy: dense hr_raw MAE inside [1.34, 4.36], i.e. k=2 at the seed floor 0.7566 MEASURED today around the incumbent's 2.851. The four confounded arms sat at 21.3-24.6, so a roster effect of that magnitude would be unmissable; a small effect is what this gate is actually resolving.
Gate `g2-calibration-domain` (blocking): g2 BLOCKING, calibration: emitted/true SD ratio inside [0.85, 1.35] on the dense draw. THIS IS THE GATE THAT NAMES THE KNOWN FAILURE MODE. All four confounded wide-roster arms are outside it -- v11full 0.57 and v12conly 0.48 emit a COMPRESSED range (regressing toward a constant), v13flat 2.03 and v14long 2.76 over-express it. MAE alone would report 'worse' without saying the arms had stopped tracking the target at all, and the dense noise floor is only valid inside this band.
Gate `g3-supa-advisory` (advisory): g3 ADVISORY, deployment population: supabase hr_raw MAE not worse than the incumbent's 3.030 by more than k=2 at the re-measured floor 0.8601. Advisory rather than blocking because optihealth_supabase is itself on BOTH rosters, so the supa draw is the least sensitive place to see a roster effect.

#target-style #optihealth #medgemma #deployment-distribution #readout #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
