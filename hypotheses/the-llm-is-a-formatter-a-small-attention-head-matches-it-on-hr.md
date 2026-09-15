---
ambition_class: reframe
created_at: '2026-09-01T20:32:21+00:00'
former_ids: []
gap_closure_estimate: 0.5
gates:
- blocking: true
  description: 'PRIMARY, BLOCKING. Attention head hr_raw MAE <= 4.5 bpm on the 1,485-row
    val+test pool, trivial baseline recomputed on those rows. mde 0.5 bpm: the pool
    is 1,485 rows so it resolves far finer than the 128-row draws, but no split-honest
    repeat-run sigma exists yet and I will not borrow the incumbent''s leaked 0.31.'
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  id: g1-head-reaches-the-llm-accuracy-class
  mde: 0.5
- blocking: true
  description: BLOCKING, and read FIRST. Emitted/true sd ratio inside [0.85, 1.35].
    Outside the band the MAE is not comparable and the arm is not a candidate regardless
    of what it scores.
  diagnostic_id: eiv_gen_between_sample_std
  id: g2-calibration-in-band
  mde: 0.15
- blocking: true
  description: BLOCKING, and it is the gate that can void the whole experiment. The
    head on the TRAINED tower must beat the same head on a RANDOM-INIT tower of identical
    geometry. exp-055's do_not_retry records every trained LUPI cell being matched
    or beaten by an untrained encoder, including a headline z+8.88. If A1 does not
    beat A3 here, the 96 tokens carry nothing learned, and this experiment says nothing
    about the LLM -- it says the tower is empty, which is a different and larger finding.
  diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  id: g3-must-beat-its-own-random-tower
  mde: null
id: the-llm-is-a-formatter-a-small-attention-head-matches-it-on-hr
interventions: []
override_reason: ''
paper_refs:
- tan-2024-llm-useful-timeseries-2406-16964
- qiu-2026-rethinking-llm-tsf-2602-14744
- prajod-2022-ecg-stress-generalizability-2210-06225
predicted_effects:
- diagnostic_id: eiv_splithonest_heldout_hr_raw_mae_bpm
  direction: down
  magnitude_note: PRIMARY. Attention head under 4.5 bpm on the 1,485-row pool against
    its own recomputed trivial baseline, i.e. inside the accuracy class the LLM occupies
    rather than an order away from it. I am NOT predicting the head wins; I am predicting
    the LLM is not necessary to reach this class. The A0 arm supplies the paired number
    that turns this into a comparison.
- diagnostic_id: eiv_gen_between_sample_std
  direction: none
  magnitude_note: CONTROL, read BEFORE the MAE per the project's standing rule. Emitted/true
    sd ratio inside [0.85, 1.35]. A head that regresses toward the mean would post
    a flattering MAE with a collapsed sd, which is how v36labels s43 looked fine in-sample
    at scale 2.08, and outside the band the MAE is not comparable to anything.
problem_id: is-the-frozen-llm-load-bearing-or-a-formatter
problem_tags:
- optihealth-eiv
- matched-baseline
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags: []
---

## Statement
TRACK 1, THE TAN ABLATION. Stated inline rather than behind a citation, because "the Tan ablation" is a name and not a definition: take the incumbent's chain exactly as it is, DELETE MedGemma from it, put a small attention head on the same 96 soft tokens, and see whether the number moves.

COMMITTED PREDICTION, before any head is trained. On the 1,485-row val+test pool, with the consumer trained under `split: train`, the attention head's hr_raw MAE lands WITHIN the accuracy class the LLM occupies rather than far behind it -- concretely, under 4.5 bpm against a trivial always-predict-median baseline recomputed on those same rows, with calibration (emitted/true sd) inside [0.85, 1.35]. Direction on eiv_splithonest_heldout_hr_raw_mae_bpm: DOWN (a real number where the alternative is "the head cannot do this at all").

I EXPECT TAN TO REPLICATE HERE, and I am saying so before looking. The reason is internal evidence, not the paper: this project measures hr_mean reading back off frozen embeddings at R2 0.971-0.99, while the LLM's emitted hr_peak has negative skill on every seed ever run. A stack whose encoder nearly solves the level and whose LLM cannot beat a constant on the excursion is a stack where the LLM is formatting a number it was handed.

WHAT WOULD MAKE ME WRONG, and it is a live outcome: if the head lands far behind, the LLM is doing real work on this interface and Qiu's regime holds -- which would make Track 4 (richer targets) the right next move rather than Track 5 (a different interface).

THE THREE CONTROLS ARE THE EXPERIMENT, not decoration.
  A2 SHALLOW FLOOR   mean-pool the 96 tokens -> RidgeCV. Lecture 4 of the
                     multimodal-ai skill and E28's own result both say to run
                     this: on frozen LM hidden states a plain Ridge BEAT every
                     non-linear readout tried. If A2 matches A1, attention is
                     decoration too and the finding is stronger, not weaker.
  A3 RANDOM TOWER    the same head on tokens from a RANDOM-INIT tower of
                     identical geometry. exp-055's do_not_retry is explicit: "a
                     large z against a permutation null is not evidence that
                     training did anything", because every trained cell there was
                     matched by an untrained encoder. If A1 does not beat A3, the
                     96 tokens carry nothing the encoder learned and NO claim
                     about the LLM can be made from this experiment at all.
  A0 THE LLM         the same frozen tower, the same roster, the same split, LoRA
                     r32 on MedGemma. GPU, queued separately.

WHAT IS HELD FIXED. Both consumers eat the IDENTICAL frozen tower -- the incumbent's own encoder + channel fusion + projector at sensor_slots 96, loaded from its bundle. band04 sets `freeze_encoder: True` and neither `train_projector` nor `train_log_scale`, so those weights are byte-identical to what the incumbent's LLM was fed. The positional codebook is NOT part of the tower: it is constructed fresh in Phase C (pipeline.py:4341-4346) and trained (:4365), so it belongs to the consumer, and each consumer supplies its own positional handling -- the LLM its codebook, the head a learned slot embedding.

THE RESIDUAL LEAK, STATED BEFORE THE RESULT. Phase A and Phase B ran with split=None, so the frozen tower was fit on subjects held out here. That inflates BOTH arms identically, through the same 96 tokens, and cannot be removed without retraining A and B. The COMPARISON is honest; the absolute MAEs are optimistic and are not deployment figures.

## Rationale
This is the cheapest experiment in NEXT.md and the one whose answer re-orders every other track: if the LLM is a formatter, then "teach the LLM physiology" must first fix the interface (Tracks 4-5), and the 2-3 GPU-h per Phase C arm plus 96 profiles of tuning are not buying the goal. It has never been run in this project. It also cannot be run against the existing captures, because those are 97.7% in-sample at the subject level and the leak is asymmetric in the LLM's favour.

## Plan
scratch/t1_llm_ablation.py, two stages.
EXTRACT (done, CPU, ~25 min): rebuild the frozen tower without loading the 4B LLM -- same constructor arguments and same state dicts as adapter_persistence.load_adapter_bundle's sensor branch -- and run the scorer's exact serving path (m_union convention, 24-channel presence concat, channel_validity_mask duplication, and a real patch_presence rather than None, since None makes a masked-trained projector silently fall back to plain avg). Cache 96x2560 fp16 tokens for the split=train roster (5,910 windows / 178 subjects) and the val+test pool (1,485 rows / 32 subjects), for BOTH the trained and the random-init tower.
  NOTE: the eval pool is split-only by design. emission_diversity_audit.py --split heldout applies no dataset_filter and no coverage gate; applying band04's 6-dataset filter would score 1,457 rows and drop dryad_ifh_affect -- the ONLY cohort in the pool absent from training, i.e. exactly the cross-device slice Prajod & Andre 2022 says matters most.
TRAIN/SCORE (CPU): AttnReadout = learned slot embedding + one multi-head cross-attention block with learnable queries + linear -> (hr_raw, hr_peak) as numbers; targets standardised on TRAIN only and mapped back before any MAE. Scored by emission_diversity_audit.py's own summarise(), so MAE / skill / corr / std_ratio mean what they mean everywhere else.
A0 (the LLM arm) is a separate GPU unit: band04 + `split: train` off the same frozen Phase B, scored with --split heldout.

## Links
Tests problem [[is-the-frozen-llm-load-bearing-or-a-formatter]].
Predicts [[eiv_splithonest_heldout_hr_raw_mae_bpm]] (down).
Predicts [[eiv_gen_between_sample_std]] (none).
Gate `g1-head-reaches-the-llm-accuracy-class` (blocking): PRIMARY, BLOCKING. Attention head hr_raw MAE <= 4.5 bpm on the 1,485-row val+test pool, trivial baseline recomputed on those rows. mde 0.5 bpm: the pool is 1,485 rows so it resolves far finer than the 128-row draws, but no split-honest repeat-run sigma exists yet and I will not borrow the incumbent's leaked 0.31.
Gate `g2-calibration-in-band` (blocking): BLOCKING, and read FIRST. Emitted/true sd ratio inside [0.85, 1.35]. Outside the band the MAE is not comparable and the arm is not a candidate regardless of what it scores.
Gate `g3-must-beat-its-own-random-tower` (blocking): BLOCKING, and it is the gate that can void the whole experiment. The head on the TRAINED tower must beat the same head on a RANDOM-INIT tower of identical geometry. exp-055's do_not_retry records every trained LUPI cell being matched or beaten by an untrained encoder, including a headline z+8.88. If A1 does not beat A3 here, the 96 tokens carry nothing learned, and this experiment says nothing about the LLM -- it says the tower is empty, which is a different and larger finding.
Cites [[tan-2024-llm-useful-timeseries-2406-16964]], [[qiu-2026-rethinking-llm-tsf-2602-14744]], [[prajod-2022-ecg-stress-generalizability-2210-06225]].

#optihealth-eiv #matched-baseline
