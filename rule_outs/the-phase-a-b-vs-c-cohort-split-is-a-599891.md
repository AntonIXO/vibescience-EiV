---
created_at: '2026-08-11T21:21:27+00:00'
former_ids:
- the-phase-a-b-vs-c-cohort-split-is-a-live-defect-worth-an-experiment-subject-cou
id: the-phase-a-b-vs-c-cohort-split-is-a-599891
problem_id: the-15-min-1-min-epoch-migration-3e7d2d
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-11T21:21:27+00:00'
topic_tags:
- dataset-whitelist
- epoch-resolution
---

## Claim
The Phase A/B-vs-C cohort split is a live defect worth an experiment: subject counts collapse 240 -> 270 -> 89 across phases, so the encoder is contrastively aligned on a cohort the LLM is then grounded on a subset of, and unifying them should help.

## Method
Pure-CPU preflight (scratch/h2_preflight_cohorts.py) instantiating the production SensorTextPairDataset once per phase with that phase's OWN profile filters, on the live corpus, then comparing the resulting subject sets directly. Cost ~1 CPU minute against the ~4 GPU-hours the arm would have taken.

## Evidence
Live corpus [10344,1440,12], each profile's OWN filters, through the production dataset:
  PHASE A  phase_a_12ch_1min_wl   n=6151  subjects=216
  PHASE B  phase_b_12ch_wl        n=5621  subjects=215
  PHASE C  lora_proxy_12ch_wl     n=6151  subjects=216
  subjects in A but NOT in C: 0
  subjects in C but NOT in A: 0
A and C are the SAME population, sample for sample. Phase B differs by ONE subject and 530 days, and that deficit is entirely its min_coverage=0.25 gate -- not a cohort choice.

The 240/270/89 figures came from the OLD 3-entry Phase-C filter {lifesnaps, pmdata, optihealth_supabase}. The *_wl chain replaced it: v4w, v5c and v5d all already use the SAME 6-dataset filter in ALL THREE phases. There is no variable left to manipulate.

CONFIRMED EN ROUTE, and these ARE real (they just are not the cohort claim):
(a) min_coverage=0.25 is near-vacuous. It is applied to ALL-CHANNEL coverage, and 4 of 12 channels (hour_of_day_sin/cos, day_of_week_sin/cos) are synthetic and always present, giving a hard floor of 4/12 = 0.3333 -- ABOVE the threshold. Measured on 1500 sampled days: all-channel coverage min 0.1586, p1 0.3208, median 0.5257; fraction passing >0.25 is 0.9900, passing >0.3333 is 0.9873. It rejects 1.27% of days, nearly all of them below the synthetic floor (a merge artifact).
(b) min_hr_coverage=0.05 is mis-scaled for the 1-minute grid. Its in-config justification is 'lifesnaps at hr_cov~0.15 (15-min sampling)'; lifesnaps' median hr coverage at 1 minute is 0.9854. The gate admits days with as few as 72 usable minutes of 1440 and passes 99.4% of the corpus. Per-dataset medians: lifesnaps 0.9854, pmdata 0.9635, mmash 0.9719, big_ideas_glycemic 0.9576, optihealth_supabase 0.6979, dryad 0.3694, dreamt 0.3632, neuro_stress_resilience_hci 0.0222, wesad 0.0792.

SCOPE. This rules out the COHORT-UNIFICATION arm on the *_wl chain as specified. It does NOT rule out tightening min_hr_coverage or replacing min_coverage with a sensor-channel-only coverage gate -- both are untested and (b) in particular is a real mis-scaling. Any future 'cohort' arm must FIRST show a cohort difference exists on the chain it targets.

## Links
Rules out for problem [[the-15-min-1-min-epoch-migration-3e7d2d]].

#dataset-whitelist #epoch-resolution
