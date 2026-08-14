---
created_at: '2026-08-14T08:31:20+00:00'
former_ids: []
gates:
- blocking: true
  description: 'BLOCKING apparatus gate, read FIRST: hr_raw-position R2 >= 0.60 (E24
    reference 0.9905). If it fails the apparatus is broken and nothing may be concluded.'
  diagnostic_id: null
  id: g0-hr-raw-positive-control
  mde: null
- blocking: true
  description: 'BLOCKING primary: final-layer hr_peak-position increment over R2(peak|true
    mean) > +0.25 => readout failure confirmed on the current recipe; rb-e4-lora-lm-head
    stays targeted (with its E26 ~4bpm scoping). <= +0.25 => forward-pass destruction
    ON THE NEW RECIPE (divergence from E24) => demote E4, lever moves to prefix geometry/gated
    cross-attention.'
  diagnostic_id: null
  id: g1-final-layer-increment
  mde: null
- blocking: true
  description: 'BLOCKING negative control: shuffled-target |R2| < 0.05.'
  diagnostic_id: null
  id: g2-shuffle-null
  mde: null
id: hyp-e8-lm-readout-transfer-20260814
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_lm_hidden_peak_r2
  direction: none
  magnitude_note: 'Committed: readout localization TRANSFERS to the current recipe
    — final-layer R2 stays high (predict 0.65-0.85, increment > +0.25 vs level control
    ~0.06), hr_raw positive control >= 0.95, shuffle null ~0. ''none'' direction =
    no material change vs E24''s 0.8029 on e23v2; the informative outcome is the increment
    clearing +0.25, not movement between adapters.'
problem_id: phase-c-hr-peak-saturated-prior-20260805
problem_tags:
- optihealth-eiv
- conditioning-collapse
- data-contract-violation
status: refuted
supersedes: null
topic_tags:
- cross-modal
- medgemma
- collapse
- sensor-text
---

## Statement
H-E8 (localization transfer): on the current baseline v9band04_s42 (1-min corpus, band law, with_presence_v1), the daily peak still survives the frozen LM forward pass — final-layer hidden state at the hr_peak emission position carries increment > +0.25 over the level control, replicating E24's readout-failure verdict on the current recipe.

## Rationale
E7 (parent chain) proved the projector output carries increment 0.5263 on v9band04_s42; E24 proved the peak survives the LM forward on e23v2 (final layer R2 0.8029, increment +0.7476, controls clean) — but e23v2 predates the 1-min corpus, the band law, and with_presence_v1, and house rules forbid citing 15-min-era numbers as current. E8 closes the localization on the CURRENT baseline with the existing instrument (scratch/diag_e24_lm_hidden_peak.py), zero code changes, ~20-40 min GPU at 4-bit. Whichever way it lands, the next lever is decided: readout arm vs prefix-side arm.

## Plan
E8: run scratch/diag_e24_lm_hidden_peak.py UNCHANGED except --adapter outputs/checkpoints/v9band04_s42/phase_c, -n 900, --out outputs/probes/e8_lm_hidden_peak_v9band04.json. Same strict-clean filter, same teacher-forced json_slots prefix, layers {0, 8, 17, 25, 34}. 4-bit load, GPU free (376 MiB used). Run inside systemd-run scope MemoryMax=14G (OOM discipline). Verdict routes: PASS all -> the full chain on the CURRENT baseline reads encoder 0.50 -> projector 0.53 -> LM final layer HIGH -> lm_head readout is the sole remaining failure stage; next GPU arm is rb-e4-lora-lm-head (untie weights first) OR the numeric-head architecture arm, decided by cost. FAIL g1 with controls passing -> the v9band04 recipe (1-min, band law, presence flags) genuinely differs from e23v2: file divergence, lever moves to prefix-side.

## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_lm_hidden_peak_r2]] (none).
Gate `g0-hr-raw-positive-control` (blocking): BLOCKING apparatus gate, read FIRST: hr_raw-position R2 >= 0.60 (E24 reference 0.9905). If it fails the apparatus is broken and nothing may be concluded.
Gate `g1-final-layer-increment` (blocking): BLOCKING primary: final-layer hr_peak-position increment over R2(peak|true mean) > +0.25 => readout failure confirmed on the current recipe; rb-e4-lora-lm-head stays targeted (with its E26 ~4bpm scoping). <= +0.25 => forward-pass destruction ON THE NEW RECIPE (divergence from E24) => demote E4, lever moves to prefix geometry/gated cross-attention.
Gate `g2-shuffle-null` (blocking): BLOCKING negative control: shuffled-target |R2| < 0.05.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
