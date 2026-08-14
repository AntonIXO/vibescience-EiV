---
created_at: '2026-08-11T21:20:49+00:00'
former_ids:
- admitting-dryad-ifh-affect-to-the-phase-c-dataset-filter-is-a-route-to-measurabl
id: admitting-dryad-ifh-affect-to-the-phase-de51ab
problem_id: phase-c-hr-peak-saturated-prior-20260805
retracted: true
retraction_reason: "RETRACTED 2026-08-12. Decided on a broken instrument; the conclusion
  REVERSES on the repaired one.\n\nWHAT I CLAIMED. That admitting dryad bought no
  measurable HRV emission skill — 'skill exactly 0.0 both arms' — with the primary
  gate hrv_total >= 60 failing in the wrong direction (60 -> 20).\n\nWHY IT WAS WRONG.
  The pre-c53cd53 HRV_MAP admitted only decreased/normal/increased/'normal range',
  while the json_slots target builder (src/training/datasets.py:1514-1518) and prompts/json_slots_prompt.md
  both write low/normal/high. 74 of 108 hrv emissions (68%) were silently unscorable,
  and hrv_acc was measuring P(truth = normal | model said normal) — one-class precision.
  The gate was decided on hrv_total = 5 (control) vs 20 (+dryad).\n\nREPAIRED INSTRUMENT.
  All arms rescored on one build, the rebuilt data/eval_v3_1min bundle, pinned cut
  points 157.71853625752556/126.9999977820953; denominators now 68-69 instead of 5-20:\n
  \ v5c control  30/69 = 0.4348, majority 0.4203, skill +0.0145\n  v5d +dryad   49/68
  = 0.7206, majority 0.4265, skill +0.2941\n  delta +0.2858, two-proportion z = +3.385,
  p = 0.0007\n\nPRICED AGAINST THE SEED BAND, same build (seeds 42/43/44 = v5c/v6s43/v6s44):
  hrv_acc 0.4348 / 0.4412 / 0.4412 — every seed scoring exactly 30 correct, so the
  hrv seed band is 0.0064. The dryad effect is 44.7x that band. For contrast the hr_acc
  band is 0.0781, which swallows every other effect this project has claimed.\n\nBORING
  EXPLANATIONS RULED OUT. Scored ground-truth composition differs by exactly ONE row
  ({low 29, normal 25, high 15} vs {29, 24, 15}), so this is not a composition artifact.
  Best constant predictor scores 0.4265: v5d beats it by +0.2941, v5c by +0.0083.\n\nSTILL
  UNPROVEN — why this is a retraction, not a new positive claim. hrv_acc cannot say
  WHICH classes improved. The scorer recorded only true_hrv_seen and discarded predictions,
  so per-class recall was structurally unmeasurable — the same defect that let hrv_acc
  0.8125 ship as a headline when it was one-class precision over 12% of the run. A
  shifted prior toward the modal class remains a live alternative. Commit ea45a0e
  adds pred_* tracking, confusion matrices, per-class recall and predicted-class counts;
  until an arm is probed carrying those fields, 'dryad separates the hrv classes'
  is NOT established.\n\nWHAT SURVIVES. The hr_peak mode collapse under dryad (19
  -> 6 distinct emitted values, top-1 mass 0.271 -> 0.604) was measured on raw text
  and is parser-independent, so it stands. Dryad is a MIXED intervention: it appears
  to help hrv while collapsing hr_peak diversity. The correct next arm is dryad gated
  on MEASURED coverage only, isolating the 63.5% held-HR-cell fraction from dataset
  identity."
ruled_out_at: '2026-08-11T21:20:49+00:00'
topic_tags:
- cross-modal
- medgemma
- dataset-whitelist
- denominator
---

## Claim
Admitting dryad_ifh_affect to the Phase C dataset_filter is a route to measurable HRV emission (it triples the HRV-bearing training pool, so the hrv slot should become both measurable and better). ALSO ELIMINATED: the framing that dryad's value can be judged by hr_acc at all.

## Method
Two full three-phase arms on the live corpus [10344,1440,12] and the REBUILT data/eval_v3_1min (row identity 200/200 vs canonical, norm sha 6f5de9c1b55b both sides), with BOTH arms carrying IDENTICAL PINNED cut points (tachycardic 157.71853625752556 / elevated 126.9999977820953) -- the first genuinely comparable Phase-C A/B in this project. CONTROL v5c: phase_a_12ch_1min_wl -> phase_b_12ch_wl -> lora_proxy_12ch_wl. INTERVENTION v5d: the same three profiles with dryad_ifh_affect appended to dataset_filter and NOTHING else changed (verified identical: max_steps 2600, probe_weight 0.0, hr_regression_weight 0.0). Preflight ASSERTED the single variable before launch (scratch/h1_preflight_filter.py): 4097 rows only in the intervention, all dryad; ZERO rows only in the control; n 6151->10248, subjects 216->239, days with ANY hrv 0.4926->0.6967. All six stages RC=0 per arm. Probes: full suite at 512 samples. Emission forensics: scratch/h1_forensics_parse_failure.py and scratch/emission_diversity_audit.py, both mirroring the production bucket_parser generation path exactly (production prompt file, channel_mask, .soft_tokens, positional codebook, chat template).

## Evidence
PRIMARY GATE FAILED IN THE WRONG DIRECTION
  v5c control : hrv_total = 60,  hrv_acc 0.4000 = 24/60, majority 0.4000 -> skill EXACTLY 0.0000
  v5d +dryad  : hrv_total = 20,  hrv_acc 0.6500 = 13/20, majority 0.6500 -> skill EXACTLY 0.0000
The denominator went DOWN 60 -> 20. On BOTH arms hrv_acc equals its own majority baseline to full float precision, i.e. constant emission either way. The >=60 gate had ALREADY been met by the eval rebuild alone (v4c 29 -> v4w 33 -> v5c 60), so instrument repair delivered it, not dryad.

hr_acc: v5c 0.5234 = 67/128 vs v5d 0.5156 = 66/128. ONE sample apart, two-proportion z = +0.125, p ~ 0.90.

WHAT hr_acc COULD NOT SEE: dryad COLLAPSED hr_peak emission onto a round-number lattice. Same 16 indices, same seed, production prompt through the exact bucket_parser path: v5c emits 10 distinct peaks (101.0, 111.9, 115.0, 123.1, 131.0, 131.5, 133.0, 133.3, 134.1, 160.0); v5d emits 4 -- {100, 110, 130, 160} -- with 130.0 on 11/16. At n=48 the same pattern: v5c 19 distinct / top-1 mass 0.271 vs v5d 6 distinct / top-1 mass 0.604. The bucket rule only asks which side of 127.00/157.72 a value falls on, so 130.0 and 133.3 score identically.

parse_failure_rate 0.0000 -> 0.2422 (31/128). Mechanism read from raw text: the model never stops, re-emitting tail slots until max_new_tokens truncates it mid-string ('state_neutral_neutral' x12 in one sample), leaving the JSON unterminated. hr_* keys render FIRST and survive; hrv_* follows hr_peak and is the first casualty -- the mechanical cause of the hrv_total collapse, NOT a shortage of HRV data. Corroborated by p5: hr_recall_at_1 0.5781 -> 0.0938 while hr_recall_at_3 is IDENTICAL at 0.7891.

ENCODER: dryad trades peak for level. hr_mean R2 +0.0034 at phase B (inside the seed band, secondary gate passes) but excursion increment R2 0.4139 -> 0.2906 and hr_peak skill +4.375 -> +3.080 bpm, consistent at BOTH phase A and phase B.

SCOPE LIMITS. ONE seed per arm, and this A/B PREDATES the Phase-C seeding fix (0b5aff2) -- both arms were unseeded draws. The hr_acc null is 'no evidence of difference', NEVER 'proven equivalent', and it sits far inside the v4w/v5c basin gap of 2.673 bpm hr_peak skill. What IS ruled out is the specific claim that adding dryad buys HRV emission: that failed on its own preregistered primary gate, in the wrong direction. The hr_peak collapse and parse-failure jump are much larger than the basin gap but rest on n=16 forensic and n=128 probe samples; replicate on a second seed before treating the MECHANISM as established. This does NOT rule out dryad as data -- its hr_peak labels are measured excursions (0/600 days have a HELD peak cell; median(peak|all present) == median(peak|unheld) == 124.97). The untested alternative is that the harm comes from its 63.5% HELD HR cells flattening the within-window variation the excursion signal needs. Cheap next test: a dryad arm gated on MEASURED coverage only, isolating the hold fraction from dataset identity.

## Links
Rules out for problem [[phase-c-hr-peak-saturated-prior-20260805]].

**RETRACTED:** RETRACTED 2026-08-12. Decided on a broken instrument; the conclusion REVERSES on the repaired one.

WHAT I CLAIMED. That admitting dryad bought no measurable HRV emission skill — 'skill exactly 0.0 both arms' — with the primary gate hrv_total >= 60 failing in the wrong direction (60 -> 20).

WHY IT WAS WRONG. The pre-c53cd53 HRV_MAP admitted only decreased/normal/increased/'normal range', while the json_slots target builder (src/training/datasets.py:1514-1518) and prompts/json_slots_prompt.md both write low/normal/high. 74 of 108 hrv emissions (68%) were silently unscorable, and hrv_acc was measuring P(truth = normal | model said normal) — one-class precision. The gate was decided on hrv_total = 5 (control) vs 20 (+dryad).

REPAIRED INSTRUMENT. All arms rescored on one build, the rebuilt data/eval_v3_1min bundle, pinned cut points 157.71853625752556/126.9999977820953; denominators now 68-69 instead of 5-20:
  v5c control  30/69 = 0.4348, majority 0.4203, skill +0.0145
  v5d +dryad   49/68 = 0.7206, majority 0.4265, skill +0.2941
  delta +0.2858, two-proportion z = +3.385, p = 0.0007

PRICED AGAINST THE SEED BAND, same build (seeds 42/43/44 = v5c/v6s43/v6s44): hrv_acc 0.4348 / 0.4412 / 0.4412 — every seed scoring exactly 30 correct, so the hrv seed band is 0.0064. The dryad effect is 44.7x that band. For contrast the hr_acc band is 0.0781, which swallows every other effect this project has claimed.

BORING EXPLANATIONS RULED OUT. Scored ground-truth composition differs by exactly ONE row ({low 29, normal 25, high 15} vs {29, 24, 15}), so this is not a composition artifact. Best constant predictor scores 0.4265: v5d beats it by +0.2941, v5c by +0.0083.

STILL UNPROVEN — why this is a retraction, not a new positive claim. hrv_acc cannot say WHICH classes improved. The scorer recorded only true_hrv_seen and discarded predictions, so per-class recall was structurally unmeasurable — the same defect that let hrv_acc 0.8125 ship as a headline when it was one-class precision over 12% of the run. A shifted prior toward the modal class remains a live alternative. Commit ea45a0e adds pred_* tracking, confusion matrices, per-class recall and predicted-class counts; until an arm is probed carrying those fields, 'dryad separates the hrv classes' is NOT established.

WHAT SURVIVES. The hr_peak mode collapse under dryad (19 -> 6 distinct emitted values, top-1 mass 0.271 -> 0.604) was measured on raw text and is parser-independent, so it stands. Dryad is a MIXED intervention: it appears to help hrv while collapsing hr_peak diversity. The correct next arm is dryad gated on MEASURED coverage only, isolating the 63.5% held-HR-cell fraction from dataset identity.

#cross-modal #medgemma #dataset-whitelist #denominator
