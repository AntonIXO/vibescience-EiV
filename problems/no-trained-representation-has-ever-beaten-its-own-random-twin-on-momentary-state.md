---
created_at: '2026-09-02T18:57:07+00:00'
former_ids: []
id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
paper_refs:
- marco-blanco-2025-rocket-random-projection-theory-2502-17061
- sensorlm-2025-2506-09108
- amin-2025-stress-reproducibility-consumer-wearables-2505-05694
- pulse-2025-privileged-eda-teacher-2510-24058
- prajod-2022-ecg-stress-generalizability-2210-06225
- sensorfm-2026-2605-22759
- apple-2024-accel-fm-ppg-distillation-2412-11276
- physioomni-2025-shared-private-tokenizer-2504-19596
- jets-2025-jepa-irregular-wearables-openreview
problem_tags:
- optihealth-eiv
- representation-learning
- matched-baseline
status: open
target: null
title: 'Momentary state: the pooled AUC is a lab/field mixture, and on held-out subjects
  the encoder reads chance'
topic_tags:
- readout
- control
- baseline
- optihealth
updated_at: '2026-09-03T12:56:37+00:00'
updates:
- at: '2026-09-02T19:36:31+00:00'
  text: '2026-09-02, SAME DAY — **THE DISCRIMINATOR RAN, AND IT OVERTURNS BOTH HALVES
    OF THE FRAMING ABOVE. Read this before the description.** `scratch/g5_pool_ceiling.py`
    -> `logs/g5_pool_ceiling.json`, `logs/g5_enc_multiscale.json`. Twenty arms, all
    on the IDENTICAL 2,136 rows / 666 windows / 113 subjects, identical GroupKFold
    folds, identical within-window permutation seeds; `enc_v41/ridge` reproduces g3''s
    +0.0603 / AUC 0.6327 exactly, so the harness is validated.


    **(1) READING (B) IS REFUTED — the pool is not the ceiling.** Best readout `union_ms/gb`
    (15 handcrafted HR/HRV features at W in {30,120,240} concatenated with the encoder''s
    covering-patch max-pools at the same three spans) reads **AUC 0.7211, over_null
    +0.1649, z +9.76**. Raw features alone, no encoder: `raw_multiscale/rf` **+0.1515
    (AUC 0.7049, z +11.99)**, reproducing exp-w11''s +0.1315 on a different pool.
    So the flow-vs-stress labels support **2.7x** what g3''s gate cell reads (+0.0603),
    and the trained chain''s single-span ridge readout captures about **37%** of the
    accessible signal. Track 5''s architectural premise is MOTIVATED, not dead — but
    its bar is +0.1649, not the +0.0666 this problem was created quoting.


    **(2) THE +0.0666 BOUND IN THE DESCRIPTION IS STALE AND MUST NOT BE QUOTED.**
    `logs/wsweep_raw_shallow.json` was measured 2026-08-20, before two corpus rebuilds
    and before the 2026-08-30 label-correctness fixes (DAPPER''s 8-hour timezone displacement,
    per-session label slicing, the three fabricated `stress_level` sources). Recomputed
    on today''s corpus (14,041 windows) and today''s rows, the same 15 features under
    the same RF-300 read **+0.1178, not +0.0666**. Every "handcrafted bound" comparison
    written before today is against the wrong number.


    **(3) "TRAINING ADDS NOTHING" WAS A READOUT ARTEFACT OF THE GATE CELL, and this
    is `null-and-control-design.md` rule 2 firing again.** trained minus its own random
    twin, by cell:


    | cell | trained | random twin | delta |

    |---|---:|---:|---:|

    | enc, single span, **ridge** (g3''s gate) | +0.0603 | +0.0533 | **+0.0070** (inside
    the band) |

    | enc, single span, **gb** | +0.0803 | +0.0454 | **+0.0349** |

    | enc, {30,120,240}, **ridge** | +0.0772 | +0.0405 | **+0.0367** |

    | enc, {30,120,240}, **gb** | +0.0852 | +0.0512 | **+0.0340** |

    | raw ∪ enc, single span, gb | +0.1586 | +0.1470 | +0.0116 |

    | raw ∪ enc, {30,120,240}, gb | +0.1649 | +0.1504 | +0.0145 |


    The ONLY cell inside the ±0.0141 band is the exact one g3 gated on. Widen the
    span or drop the linearity and the random twin FALLS (+0.0533 -> +0.0405) while
    the trained encoder RISES (+0.0603 -> +0.0852). That asymmetry is a mechanism,
    not noise: a random projection''s information is linear and span-insensitive,
    the trained encoder''s is non-linear and span-sensitive. **So Phase A/B DOES put
    momentary-state information into the encoder; the gate could not see it.** The
    count in the description''s table — "six of eight cells at or below the twin"
    — holds only under the estimators those cells used, and is not evidence about
    the representation.


    **(4) WHAT SURVIVES INTACT.** The encoder is still not competitive ALONE: best
    encoder-only +0.0852 against raw_shallow/rf +0.1178 and raw_multiscale/rf +0.1515.
    And once good handcrafted features are present the learned part is mostly redundant
    — the union''s trained-minus-random delta is +0.0116 / +0.0145, at the band edge.
    **If the product needs a state slot today, the honest implementation is a multi-scale
    handcrafted-feature classifier (+0.1515), not the chain''s own state head (-0.0067,
    at its random twin''s level).** That is the opposite of the "drop the state slot"
    decision v41''s g3 pre-committed to on a null: the slot is reachable, just not
    by the current path.


    **(5) DO NOT RETRY.** Do not run another momentary-state readout under one estimator
    family. Every conclusion recorded on this target that used a single family has
    now been overturned once (exp-046 by e34''s linear arm, g3 by this run''s gb arm).
    Two families minimum, each with its own null and its own random twin, or the number
    is uninterpretable.'
- at: '2026-09-02T19:37:07+00:00'
  text: '2026-09-02 — description REPLACED with the measured table above. The original
    framing (written hours earlier from `logs/wsweep_raw_shallow.json`''s stale +0.0666
    bound and a ridge-only twin control) asserted two things the discriminator refuted:
    that the pool ceiling was ~+0.067 with ~+0.013 of headroom, and that training
    adds nothing beyond the random twin. Both were artefacts — the first of a pre-rebuild
    corpus, the second of a single estimator family. Kept as provenance in this log
    rather than silently corrected.'
- at: '2026-09-02T20:53:28+00:00'
  text: '2026-09-02 23:50 -- description RESTORED (it had been blanked by an update_problem
    call that passed only add_update) and extended with the 09-02 literature: ROCKET
    theory explains why the random twin is a strong baseline by construction; SensorLM
    Table 4 locates the missing ingredient in the CAPTION (no minute-stamped semantics);
    Amin 2025 places the 0.72 ceiling at the published HRV-only level; PULSE names
    EDA-teacher distillation as the published route above it. Three hypotheses pre-registered
    on this problem tonight: C-1 (ceiling holds on the 1,485-row held-out pool), C-3
    (labels-in-caption encoder beats HR-caption encoder on trained-minus-twin), E
    (PULSE-style EDA distillation raises the UNION ceiling, not just the encoder).'
- at: '2026-09-02T21:06:04+00:00'
  text: '2026-09-03 00:20 — THE PUBLISHED BAR FOR AN ENCODER, and where EiV''s stands
    against it. SensorFM (arXiv:2605.22759): a frozen sensor-FM embedding + PCA-50
    + LINEAR head beats a supervised model on ENGINEERED features on 34/35 health
    tasks, mental health included — that is the criterion under which a learned representation
    earns its place. EiV''s PL60 encoder fails it on momentary state: best encoder-only
    cell +0.085 vs raw_multiscale/rf +0.152 (g5), and the union adds only +0.013 over
    the raw features. Two things SensorFM says about WHY, both consistent with g5:
    (a) its smallest model shows dimensional collapse (90% of variance in 20 PCs)
    while the large one keeps a long tail — EiV''s eff_rank 9-10 is the collapsed
    regime; (b) it is pretrained by RECONSTRUCTION on 1e12 minutes, not by InfoNCE
    to HR captions on 9.4k windows. Cross-modal distillation is the other published
    route: Apple (arXiv:2412.11276) distils a PPG teacher into accelerometer encoders
    and beats supervised accelerometer encoders by 23-49% on HR/HRV; PhysioOmni (2504.19596)
    and PULSE (2510.24058) both use a shared/private split — three independent instances
    of the design Track E adopts. CAVEAT carried from the vault: rule-out that-a-jepa-latent-prediction-pretraining-7061c5
    eliminated JEPA-style pretraining AS A FIX FOR MOMENTARY STATE (exp-046); OVERVIEW.md
    Part X''s ''sensor pretraining sprint'' may be proposed only under the SensorFM
    bar (linear probe beats engineered features on HR and state) and never as a momentary-state
    fix. Papers linked.'
- at: '2026-09-02T22:47:12+00:00'
  text: '2026-09-03 02:00 -- TITLE AND DESCRIPTION REWRITTEN around exp-c1-heldout-pool-ceiling-20260903
    (`refutes` / `test_invalid`). The old framing asked whether the CEILING transfers
    across subjects. It mostly does (+0.1649 -> +0.1117), and that turned out to be
    the uninteresting half of the answer: the per-cohort slice shows the pooled figure
    is WESAD 0.9728 averaged with LifeSnaps 0.4744, i.e. a solved lab contrast averaged
    with a below-chance field contrast. The encoder half is now settled harder than
    the clean pool suggested -- every encoder-only cell at or below its own null on
    held-out subjects, union trained-minus-random -0.0002. Kept as provenance rather
    than silently corrected.'
---

## Description
MEASURED 2026-09-02/03. Two pools, and the second one reframes the first.

CLEAN POOL (2,136 minute-cells / 666 windows / 113 subjects, flow vs stress, pos 0.512; scratch/g5_pool_ceiling.py, GroupKFold by subject, every arm against its own within-window permutation null):
  union_ms/gb        +0.1649  (AUC 0.7211)   15 handcrafted HR/HRV features at W in {30,120,240} + the encoder's covering-patch max-pools
  raw_multiscale/rf  +0.1515  (AUC 0.7049)   the same features, NO encoder
  raw_shallow/rf     +0.1178                 one span. NOT the +0.0666 in logs/wsweep_raw_shallow.json, which predates two corpus rebuilds and the 2026-08-30 label fixes and must not be quoted.
  best encoder-only  +0.0852  (enc_ms/gb)
  the chain's own state head  -0.0067        chance
trained minus its own random twin, clean pool: +0.0070 single-span ridge (inside the +/-0.0141 band), +0.0349 gb, +0.0367 multi-span ridge, +0.0340 multi-span gb. The twin FALLS as the span widens while the trained encoder RISES -- a random projection's information is linear and span-insensitive (ROCKET theory, arXiv:2502.17061).

HELD-OUT POOL (exp-c1-heldout-pool-ceiling-20260903; val+test subjects only: 666 rows / 202 windows / 26 subjects, pos 0.508):
  raw_multiscale/rf  +0.1372  (AUC 0.6865)   the handcrafted readout survives the subject split
  union_ms/gb        +0.1117  (AUC 0.6398)
  union_ms@random/gb +0.1119                 => trained minus random = -0.0002
  enc_v41/ridge      -0.0206 |  enc_ms/gb -0.0128 |  enc_v41/gb +0.0224 (z +0.76, p 0.27)
So EVERY encoder-only cell is at or below its own null on subjects the chain never trained on, and the clean pool's +0.034 does not cross a subject boundary. Any arm justified by "training put state into the encoder" must show that HELD-OUT or it is measuring memorisation.

*** THE POOLED NUMBER IS A MIXTURE OF TWO REGIMES AND MUST NEVER BE QUOTED ALONE. *** Per-cohort AUC of the winning arm on the held-out pool:
  wesad      0.9728  (n=148)  scripted lab baseline-vs-stress protocol
  lifesnaps  0.4744  (n=442)  free-living EMA -- BELOW CHANCE
  case 15, dryad_ifh_affect 14, neuro_stress_resilience_hci 23, popane_s6 24 -- all under the n>=30 floor, unscored rather than silently scored.
Every momentary-state figure this project has ever published (+0.0666, +0.1178, +0.1515, +0.1649, "the pool supports AUC 0.72") has this shape, because the pools were always built by concatenating lab and field corpora. A pooled g5 number without the per-cohort slice is no longer admissible evidence. The state slot is defensible on lab-like protocols and is a coin flip on the free-living days the ring actually ships into.

NOISE FLOOR, measured: eiv_state_auc_pool_ceiling sigma 0.0155 over 6 subject-to-fold partitions (k=2 band +/-0.031); per-cell sd ranges 0.0053 (raw_shallow/ridge) to 0.0391 (enc_v41/ridge) and lives in every g5 output's `fold_auc_sd`.

LITERATURE THAT BOUNDS THIS. Amin 2025 (arXiv:2505.05694): wrist HRV-only LOSO reads 0.905-0.961 on scripted lab stressors and 0.73 on a mixed set -- our WESAD 0.9728 sits in that band, so nothing here contradicts the field; what the field does not report, and this does, is the free-living number beside it. SensorFM (arXiv:2605.22759) sets the bar an encoder must clear (frozen embedding + linear head beats engineered features on 34/35 tasks) and EiV's fails it. SensorLM (arXiv:2506.09108, Table 4) locates the missing ingredient in the CAPTION: statistical/structural captions give 0.49-0.51 on concept readout, minute-stamped semantic ones give 0.71-0.84, and our `hr_dispersion_labels` never says WHEN. PULSE (arXiv:2510.24058), Apple (arXiv:2412.11276) and PhysioOmni (arXiv:2504.19596) are three independent instances of the route above the HRV-only ceiling: distil a richer modality at the REPRESENTATION with a shared/private split, never as a value-regression head.

DO NOT RETRY: any momentary-state readout under ONE estimator family (exp-046 and g3 were each overturned that way). Two families minimum, each with its own null and its own random twin, AND the per-cohort slice. Do not motivate a JEPA/AIM pretraining arm by momentary state (rule-out that-a-jepa-latent-prediction-pretraining-7061c5); motivate it, if at all, by the SensorFM bar.

## Synthesis
STATE OF KNOWLEDGE, 2026-09-03 (five closed experiments, one rule-out).

WHAT WE BELIEVE. (1) The pooled momentary-state AUC was never one quantity: on the split-honest held-out pool union_ms/gb reads +0.1117 pooled = WESAD 0.973 (n=148) averaged with LifeSnaps 0.474 (n=442). Lab and free-living are now two diagnostics (eiv_state_auc_over_null_lab / _freeliving, both with measured partition floors) and no pooled state AUC is admissible as a gate. (2) Lab state from wrist HR/HRV is nearly solved with 15 handcrafted multi-scale features: WESAD-only 0.976 (rf) / 0.984 (gb). (3) Free-living state is weak but real and SUBJECT-supply-limited: LifeSnaps-only raw_multiscale/rf +0.045 (AUC 0.54, 0/48 nulls above) on 49 subjects, -0.004 on 9. (4) No trained representation beats the handcrafted bar on any pool, and none reliably beats its random-init twin on held-out subjects: InfoNCE captions +0.085 vs bar +0.152 (clean), reconstruction +0.067, EDA distillation +0.063; on WESAD the trained encoder reads 0.63 vs the features' 0.976 (twin 0.59), and adding it to the features buys +0.004 over adding a random one. What the encoder does see above the features on free-living is its multi-scale POOLING GEOMETRY (the twin matches it under ridge), not Phase A training. (5) An HR-only caption trains state OUT of the encoder (hr_only control below its twin on 3/4 cells).

RULED OUT. Changing the Phase A objective (captions, reconstruction, EDA-teacher distillation) as the route to state (rule-out that-changing-the-phase-a-objective-caption-d6b326). Per-patch EDA value regression. Pooled state gates. Reading the 9-subject held-out LifeSnaps cell as a ceiling.

OPEN. Whether more free-living subjects move the free-living cell (the +0.045 at 49 subjects says supply, not representation) -- a data decision. Whether EDA at inference (Amin 2025: 0.73 -> 0.95) is obtainable on any target device -- a hardware decision. Whether a within-subject (personalised) readout changes the free-living picture -- never measured here. Whether an encoder at SensorFM scale (10^12 minutes) would clear the bar -- not testable at 2e7.

PRODUCT CONSEQUENCE. Ship the 45-feature multi-scale GBM for lab-protocol state; report free-living state as unsolved (AUC 0.54) rather than pooling it; the narrator (see the Phase C problem) carries the classifier's decision losslessly.

## Updates
- **2026-09-02T19:36:31+00:00** — 2026-09-02, SAME DAY — **THE DISCRIMINATOR RAN, AND IT OVERTURNS BOTH HALVES OF THE FRAMING ABOVE. Read this before the description.** `scratch/g5_pool_ceiling.py` -> `logs/g5_pool_ceiling.json`, `logs/g5_enc_multiscale.json`. Twenty arms, all on the IDENTICAL 2,136 rows / 666 windows / 113 subjects, identical GroupKFold folds, identical within-window permutation seeds; `enc_v41/ridge` reproduces g3's +0.0603 / AUC 0.6327 exactly, so the harness is validated.

**(1) READING (B) IS REFUTED — the pool is not the ceiling.** Best readout `union_ms/gb` (15 handcrafted HR/HRV features at W in {30,120,240} concatenated with the encoder's covering-patch max-pools at the same three spans) reads **AUC 0.7211, over_null +0.1649, z +9.76**. Raw features alone, no encoder: `raw_multiscale/rf` **+0.1515 (AUC 0.7049, z +11.99)**, reproducing exp-w11's +0.1315 on a different pool. So the flow-vs-stress labels support **2.7x** what g3's gate cell reads (+0.0603), and the trained chain's single-span ridge readout captures about **37%** of the accessible signal. Track 5's architectural premise is MOTIVATED, not dead — but its bar is +0.1649, not the +0.0666 this problem was created quoting.

**(2) THE +0.0666 BOUND IN THE DESCRIPTION IS STALE AND MUST NOT BE QUOTED.** `logs/wsweep_raw_shallow.json` was measured 2026-08-20, before two corpus rebuilds and before the 2026-08-30 label-correctness fixes (DAPPER's 8-hour timezone displacement, per-session label slicing, the three fabricated `stress_level` sources). Recomputed on today's corpus (14,041 windows) and today's rows, the same 15 features under the same RF-300 read **+0.1178, not +0.0666**. Every "handcrafted bound" comparison written before today is against the wrong number.

**(3) "TRAINING ADDS NOTHING" WAS A READOUT ARTEFACT OF THE GATE CELL, and this is `null-and-control-design.md` rule 2 firing again.** trained minus its own random twin, by cell:

| cell | trained | random twin | delta |
|---|---:|---:|---:|
| enc, single span, **ridge** (g3's gate) | +0.0603 | +0.0533 | **+0.0070** (inside the band) |
| enc, single span, **gb** | +0.0803 | +0.0454 | **+0.0349** |
| enc, {30,120,240}, **ridge** | +0.0772 | +0.0405 | **+0.0367** |
| enc, {30,120,240}, **gb** | +0.0852 | +0.0512 | **+0.0340** |
| raw ∪ enc, single span, gb | +0.1586 | +0.1470 | +0.0116 |
| raw ∪ enc, {30,120,240}, gb | +0.1649 | +0.1504 | +0.0145 |

The ONLY cell inside the ±0.0141 band is the exact one g3 gated on. Widen the span or drop the linearity and the random twin FALLS (+0.0533 -> +0.0405) while the trained encoder RISES (+0.0603 -> +0.0852). That asymmetry is a mechanism, not noise: a random projection's information is linear and span-insensitive, the trained encoder's is non-linear and span-sensitive. **So Phase A/B DOES put momentary-state information into the encoder; the gate could not see it.** The count in the description's table — "six of eight cells at or below the twin" — holds only under the estimators those cells used, and is not evidence about the representation.

**(4) WHAT SURVIVES INTACT.** The encoder is still not competitive ALONE: best encoder-only +0.0852 against raw_shallow/rf +0.1178 and raw_multiscale/rf +0.1515. And once good handcrafted features are present the learned part is mostly redundant — the union's trained-minus-random delta is +0.0116 / +0.0145, at the band edge. **If the product needs a state slot today, the honest implementation is a multi-scale handcrafted-feature classifier (+0.1515), not the chain's own state head (-0.0067, at its random twin's level).** That is the opposite of the "drop the state slot" decision v41's g3 pre-committed to on a null: the slot is reachable, just not by the current path.

**(5) DO NOT RETRY.** Do not run another momentary-state readout under one estimator family. Every conclusion recorded on this target that used a single family has now been overturned once (exp-046 by e34's linear arm, g3 by this run's gb arm). Two families minimum, each with its own null and its own random twin, or the number is uninterpretable.
- **2026-09-02T19:37:07+00:00** — 2026-09-02 — description REPLACED with the measured table above. The original framing (written hours earlier from `logs/wsweep_raw_shallow.json`'s stale +0.0666 bound and a ridge-only twin control) asserted two things the discriminator refuted: that the pool ceiling was ~+0.067 with ~+0.013 of headroom, and that training adds nothing beyond the random twin. Both were artefacts — the first of a pre-rebuild corpus, the second of a single estimator family. Kept as provenance in this log rather than silently corrected.
- **2026-09-02T20:53:28+00:00** — 2026-09-02 23:50 -- description RESTORED (it had been blanked by an update_problem call that passed only add_update) and extended with the 09-02 literature: ROCKET theory explains why the random twin is a strong baseline by construction; SensorLM Table 4 locates the missing ingredient in the CAPTION (no minute-stamped semantics); Amin 2025 places the 0.72 ceiling at the published HRV-only level; PULSE names EDA-teacher distillation as the published route above it. Three hypotheses pre-registered on this problem tonight: C-1 (ceiling holds on the 1,485-row held-out pool), C-3 (labels-in-caption encoder beats HR-caption encoder on trained-minus-twin), E (PULSE-style EDA distillation raises the UNION ceiling, not just the encoder).
- **2026-09-02T21:06:04+00:00** — 2026-09-03 00:20 — THE PUBLISHED BAR FOR AN ENCODER, and where EiV's stands against it. SensorFM (arXiv:2605.22759): a frozen sensor-FM embedding + PCA-50 + LINEAR head beats a supervised model on ENGINEERED features on 34/35 health tasks, mental health included — that is the criterion under which a learned representation earns its place. EiV's PL60 encoder fails it on momentary state: best encoder-only cell +0.085 vs raw_multiscale/rf +0.152 (g5), and the union adds only +0.013 over the raw features. Two things SensorFM says about WHY, both consistent with g5: (a) its smallest model shows dimensional collapse (90% of variance in 20 PCs) while the large one keeps a long tail — EiV's eff_rank 9-10 is the collapsed regime; (b) it is pretrained by RECONSTRUCTION on 1e12 minutes, not by InfoNCE to HR captions on 9.4k windows. Cross-modal distillation is the other published route: Apple (arXiv:2412.11276) distils a PPG teacher into accelerometer encoders and beats supervised accelerometer encoders by 23-49% on HR/HRV; PhysioOmni (2504.19596) and PULSE (2510.24058) both use a shared/private split — three independent instances of the design Track E adopts. CAVEAT carried from the vault: rule-out that-a-jepa-latent-prediction-pretraining-7061c5 eliminated JEPA-style pretraining AS A FIX FOR MOMENTARY STATE (exp-046); OVERVIEW.md Part X's 'sensor pretraining sprint' may be proposed only under the SensorFM bar (linear probe beats engineered features on HR and state) and never as a momentary-state fix. Papers linked.
- **2026-09-02T22:47:12+00:00** — 2026-09-03 02:00 -- TITLE AND DESCRIPTION REWRITTEN around exp-c1-heldout-pool-ceiling-20260903 (`refutes` / `test_invalid`). The old framing asked whether the CEILING transfers across subjects. It mostly does (+0.1649 -> +0.1117), and that turned out to be the uninteresting half of the answer: the per-cohort slice shows the pooled figure is WESAD 0.9728 averaged with LifeSnaps 0.4744, i.e. a solved lab contrast averaged with a below-chance field contrast. The encoder half is now settled harder than the clean pool suggested -- every encoder-only cell at or below its own null on held-out subjects, union trained-minus-random -0.0002. Kept as provenance rather than silently corrected.

## Links
Cites [[marco-blanco-2025-rocket-random-projection-theory-2502-17061]], [[sensorlm-2025-2506-09108]], [[amin-2025-stress-reproducibility-consumer-wearables-2505-05694]], [[pulse-2025-privileged-eda-teacher-2510-24058]], [[prajod-2022-ecg-stress-generalizability-2210-06225]], [[sensorfm-2026-2605-22759]], [[apple-2024-accel-fm-ppg-distillation-2412-11276]], [[physioomni-2025-shared-private-tokenizer-2504-19596]], [[jets-2025-jepa-irregular-wearables-openreview]].

#readout #control #baseline #optihealth #optihealth-eiv #representation-learning #matched-baseline
