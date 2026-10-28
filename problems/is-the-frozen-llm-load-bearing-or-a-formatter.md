---
created_at: '2026-09-01T20:31:24+00:00'
former_ids: []
id: is-the-frozen-llm-load-bearing-or-a-formatter
paper_refs:
- tan-2024-llm-useful-timeseries-2406-16964
- qiu-2026-rethinking-llm-tsf-2602-14744
- song-2025-decoding-based-regression-2501-19383
- chen-2025-rl-decoding-regression-2512-06533
- biderman-2024-lora-learns-less-2405-09673
- sensorlm-2025-2506-09108
- sensorfm-2026-2605-22759
- feli-2025-cha-ppghr-agent-2502-12836
- phllm-2024-personal-health-llm-2406-06474
problem_tags:
- optihealth-eiv
- matched-baseline
status: resolved
target: null
title: Nothing has ever checked whether the frozen LLM is load-bearing, or a formatter
  the encoder could do without
topic_tags: []
updated_at: '2026-09-18T11:14:24+00:00'
updates:
- at: '2026-09-01T20:52:57+00:00'
  text: "2026-09-01 — TWO ADDENDA TO exp-t1-tan-ablation-heads-20260901, both measured
    after it closed.\n\n(1) EXACT REPRODUCIBILITY. The head arms were re-run end to
    end from the same seed to produce per-row predictions for the pending paired comparison.
    The loss trajectory is identical step for step and the scores match to six decimals:
    attention head hr_raw MAE 1.121046 both runs, ridge mean-pool 1.161078 both runs,
    trivial 6.881492. Worth recording because this project spent months on effects
    smaller than its own seed band; on THIS instrument the head arms carry no seed
    noise at all, because nothing in the path is stochastic once the tokens are cached.\n\n(2)
    THE CROSS-DEVICE SLICE, which Track 0 asked for and which Prajod & Andre 2022
    (arXiv:2210.06225) says is where deep models fall behind feature models. Per-dataset,
    attention head on the trained tower:\n\n  dataset                 n     MAE   trivial
    \  corr\n  pmdata                514   1.043    7.910   0.992\n  lifesnaps             906
    \  1.090    5.883   0.983\n  big_ideas_glycemic     31   2.013    4.339   0.944\n
    \ dryad_ifh_affect       28   2.267    9.779   0.974\n  mmash                   6
    \  2.550    5.701   0.968\n\n`dryad_ifh_affect` is the ONLY cohort in the held-out
    pool that is absent from the training roster altogether -- it is not in band04's
    6-dataset dataset_filter, so no stage of this chain, including Phase A and Phase
    B, ever saw it. It is therefore the one genuinely out-of-roster, cross-device
    measurement available anywhere in this project, and it is also the 3 rows that
    were the entire honest-generalisation content of the incumbent's leaked 128-row
    capture.\n\nOn it the head reads MAE 2.267 against a trivial 9.779 -- skill +7.5
    bpm, corr 0.974, the LARGEST skill of any cohort here because dryad's HR spread
    is the widest. So on the one cohort where Prajod predicts the deep readout should
    fall behind handcrafted features, it does not fall behind at all; it degrades
    from 1.09 to 2.27 bpm while the baseline it must beat degrades from 5.88 to 9.78.\n\nCAVEAT
    THAT BINDS THIS: n=28, one cohort, and it is a READOUT task (hr_raw is the masked
    mean of an input channel), not the forecasting/classification setting Prajod tested.
    It is a reassuring data point, not a refutation of his result, and it should not
    be quoted as one."
- at: '2026-09-01T20:55:27+00:00'
  text: '2026-09-01 — HOW exp-t1''s ARMS MAP ONTO TAN''S ACTUAL ABLATION TAXONOMY,
    read from arXiv:2406.16964v2 section 3.2 rather than from its abstract, plus the
    one confound the design cannot remove.


    TAN''S THREE ABLATIONS: (a) `w/o LLM` -- remove the language model entirely, pass
    the input tokens straight to the reference method''s final layer; (b) `LLM2Attn`
    -- replace it with a SINGLE randomly-initialised multi-head attention layer; (c)
    `LLM2Trsf` -- replace it with a single randomly-initialised transformer block.


    MAPPING. exp-t1''s ridge mean-pool is `w/o LLM`; its cross-attention head is `LLM2Attn`.
    `LLM2Trsf` was not run, and the internal evidence says it need not be: ridge and
    attention land 0.040 bpm apart on 1,485 rows, so consumer capacity is already
    shown not to move this number, and a transformer block sits between them in capacity.


    THE DELIBERATE DEVIATION, which a reader comparing the two papers will otherwise
    flag as an error. Tan writes: "In the above ablations, we keep left parts of the
    forecasters unchanged (TRAINABLE)" -- he lets the surrounding encoder adapt to
    the new consumer. exp-t1 holds the tower FROZEN instead. That is not carelessness:
    the EiV reference system freezes it (band04 sets freeze_encoder: True and neither
    train_projector nor train_log_scale), so freezing is precisely what makes both
    consumers eat identical inputs. Tan''s choice is right for his systems, which
    fine-tune around the LLM; mine is right for this one. The consequence is that
    exp-t1 is a STRICTER test of the tokens and a WEAKER test of the architecture:
    a trainable tower would likely do better than 1.121 bpm, not worse.


    THE CONFOUND THE DESIGN CANNOT REMOVE, and which must be stated when A0 closes.
    The LLM arm is trained with token cross-entropy over a JSON string; the head arm
    regresses a number under smooth-L1. Those objectives are not matched. But this
    is NOT a nuisance to control away -- it is part of what is being ablated. E28
    already established that `lm_head` cannot output a scalar, so a numeric head is
    not available inside the LLM path at all: emitting numbers as tokens is intrinsic
    to the decision to use an LLM here, and its cost is a legitimate part of the LLM''s
    column.


    THE FOLLOW-UP THAT WOULD LOCALISE THE LOSS, not run here: a linear readout on
    the LLM''s FINAL HIDDEN STATE (E28''s design, which reached hr_peak MAE 10.270
    bpm with plain RidgeCV) placed beside the projector-token readout on the same
    pool. If the hidden-state readout is worse than the projector-token readout, the
    loss happens inside the LLM''s forward pass rather than at the emission step --
    which is the distinction between "the LLM discards it" and "the decoder cannot
    say it".'
- at: '2026-09-02T19:02:51+00:00'
  text: '2026-09-02 — THE TOWER-ARTEFACT ESCAPE IS CLOSED; STAYS OPEN ONLY FOR THE
    LOCALISATION FOLLOW-UP. New rule-out `the-llm-s-emission-loss-was-an-artefact-of-the-incumbent-s-weak-tower`.
    The obvious objection to exp-t1 was that its 3.382 bpm head-beats-LLM gap came
    from measuring on the incumbent''s weak PL120 tower. Measured on v41rebased''s
    better PL60 tower with the identical head, seed, roster, pool and code (`scratch/t1b_heads_v41_tower.py`),
    the gap in bpm narrows to +2.031 (CI95 [+1.606, +2.485]) — and the fraction of
    available skill the LLM DISCARDS goes UP, 58% to 66%: the head reads 0.812 bpm
    where the LLM emits 2.843, against a 6.876 trivial. On hr_peak the same paired
    contrast is +3.120 bpm (CI95 [+1.625, +4.686]). So a better representation does
    not make the LLM a better reader of it, and v41''s 1.586 bpm gain over arm A0
    is the TOWER improving, not the LLM path.


    WHAT REMAINS GENUINELY OPEN, and it is the reason not to resolve this yet: WHERE
    in the LLM the information is lost. exp-t1''s own follow-up — an E28-style readout
    on MedGemma''s FINAL HIDDEN STATE — separates "discarded in the forward pass"
    (the soft tokens never reach the residual stream in usable form) from "lost at
    emission" (the information is there and `lm_head`''s token CE over JSON digits
    cannot express it). Those two have opposite fixes: the first is an interface problem,
    the second is an objective problem, and the product decision differs. Nobody has
    run it; it needs no retrain.'
- at: '2026-09-02T20:57:32+00:00'
  text: '2026-09-02 23:55 — LITERATURE + VAULT TRACE, and Track A pre-registered (hypothesis
    `a-the-llm-keeps-hr-raw-in-its-residual-stream-and-loses-it-at-emission`). Kept
    OPEN deliberately despite doctor''s ''answered but open'' flag: the confirmed
    hypothesis answers ''is it load-bearing'' (no: head 0.812 vs LLM 2.843 on the
    PL60 tower, 66% of the tokens'' skill discarded, t1b); the open question is WHERE
    the loss is, and that decides D-a vs E/G.


    WHAT THE VAULT ALREADY HAD (all on OLD adapters, never on the same rows as the
    head): E36 2026-08-06 (logs/e36_full.log, medgemma_12ch_e33.seed_1) — hr_raw RidgeCV
    on the residual stream at the emission token 1.48 bpm vs donor-null 6.98 / zeroed
    6.94 / always-median 7.59: the forward pass KEEPS most of it. exp-e25 — hr_peak
    linearly present at the emission position (R2 +0.51, MAE 11.8 vs 20.1). exp-e26
    — measured in lm_head''s real digit function class the readout still beats median
    by 4.2 bpm but carries coarse magnitude only. exp-037 — decoding the full conditional
    pmf (median/mean) instead of argmax took hr_peak corr 0.55 -> 0.795 and skill
    -1.91 -> +1.86 bpm with NO retrain; its lesson, verbatim: ''separate decode rule
    from representation before concluding the signal is absent''. Nobody carried that
    lesson to hr_raw or to a split-honest adapter.


    WHAT THE LITERATURE SAYS (papers linked): Song & Bahri 2025 (arXiv:2501.19383)
    — a decoder head over digit tokens MATCHES a pointwise head on the same features,
    under digit-by-digit tokens, CONSTRAINED decoding, no interleaved language, and
    the loss-matched point estimate (median for MAE); their Sec. 5 names LLM vocabularies
    without digit tokens, top-p sampling and interleaved template tokens as the expected
    losses — i.e. our exact configuration. Chen 2025 (arXiv:2512.06533) — token-CE
    never sees magnitude; a sequence-level |y_hat - y| reward (GRPO) beats both decoding
    baselines and regression heads. Biderman 2024 (arXiv:2405.09673) — rank-32 LoRA
    learns far less than full FT; full FT perturbations are rank 10-100x higher. SensorLM
    (arXiv:2506.09108) — Google''s sensor-language model reports NO LLM-vs-head ablation;
    its text side is a small decoder; generic LLMs fed sensor days as text are at
    chance.


    READING: ''formatter'' is now split into two testable halves — (i) the forward
    pass discards (interface problem; fix = E/G or the narrator architecture), (ii)
    the emission step discards (objective/format problem; fix = D-a, then Chen-style
    reward). E36 on the old adapter says mostly (ii). Track A measures it on v41rebased_c
    on the 128 rows every other tower number was measured on; D-a (`d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss`)
    is pre-registered with A''s hidden-state number as its g0 precondition.'
- at: '2026-09-02T21:05:37+00:00'
  text: '2026-09-03 00:20 — THE FIELD HAS ALREADY MOVED THE LLM OFF THE SENSOR PATH,
    and two papers now report the same sign as exp-t1/t1b on numeric targets. (1)
    SensorFM (arXiv:2605.22759, Google 2026, added to vault): the deployed architecture
    is sensor FM -> PCA-50 + LINEAR heads -> Personal Health Agent that consumes the
    heads'' PREDICTIONS; in the 1,860-rating clinician study the LLM-given-metrics-only
    condition is worst on every axis (p<0.001) and the LLM-given-head-predictions
    condition is indistinguishable from LLM-given-ground-truth (p=0.396). No condition
    feeds soft tokens to the LLM. (2) CHA-PPGHR (arXiv:2502.12836): an LLM orchestrating
    a signal-processing tool chain reads HR at ~2.8 bpm MAE against ECG where GPT-4o
    reading the data directly reads ~8.9 — the same ~3x gap as t1b''s 0.81 head vs
    2.84 LLM. (3) PH-LLM (arXiv:2406.06474): even with a learned multimodal encoder,
    the LLM only MATCHES specialised discriminative heads on sleep-outcome prediction;
    never exceeds them. NO published result anywhere in the OVERVIEW.md survey (29
    systems) shows soft-token injection into a frozen LLM beating a task head on a
    numeric physiological target. Consequence for this problem: ''formatter'' is not
    an EiV pathology, it is the field''s settled position for numeric targets; what
    remains EiV-specific is Track A''s localisation (emission vs forward pass) because
    it decides whether the soft-token path is worth ONE more Phase C retrain (D-a)
    before the narrator architecture becomes the product path. Diagnostic eiv_narration_numeric_fidelity_mae_bpm
    registered for that architecture.'
- at: '2026-09-03T06:43:54+00:00'
  text: '2026-09-03 — RESOLVED. Both halves are now measured on the same 128 split-honest
    rows of the v41rebased PL60 tower. (1) LOAD-BEARING: no. Attention head over the
    96 soft tokens 0.812 bpm vs MedGemma+LoRA emission 2.843 (trivial 6.876), rule-out
    `the-llm-s-emission-loss-was-an-artefact-of-the-incumbent-s-weak-tower`. (2) WHERE
    THE LOSS IS (exp-a-hr-tower-localisation-20260903): RidgeCV on the residual stream
    at the hr_raw emission token, zeroed-scaffold context, reads 2.331 (donor-null
    7.645, zeroed 7.738). So 1.519 of the 2.031 bpm (75%) is lost in the FORWARD PASS
    and 0.512 at emission; medianK decoding does not help (3.032). The emission-fix
    branch (D-a) is therefore unpowered against the measured seed floor (sigma 0.208)
    and was not run. (3) THE ALTERNATIVE WORKS WITH NO TRAINING (exp-n-narrator-fidelity-20260903,
    supports): base MedGemma with the adapter disabled, handed the head''s numbers
    in the prompt, repeats them at 0.0245 bpm fidelity; end-to-end 0.814 vs truth.
    Product path = head -> narrator, matching SensorFM / PH-LLM / CHA-PPGHR. What
    this problem does NOT settle: whether the narrator carries STATE (g3 of exp-n
    unevaluated; follow-up pre-registered 2026-09-03), and the soft-token bridge as
    a research object under full fine-tuning (Track G, unfunded).'
- at: '2026-09-18T11:14:24+00:00'
  text: 'PROVENANCE CORRECTION 2026-09-18: the documented scratch/run_v41_rebased.sh
    writes split=train to YAML, but src/cli_train.py at saved commit ad78bc2082d0
    forwards split only for C; Phase A allowed_subject_keys was not forwarded (saved
    allowed_subject_count=null) and B had no allowlist. This static evidence blocks
    the WHOLE-PIPELINE subject-held-out claim for the documented path. Stored captures/numerical
    comparisons are preserved; no historical rerun or unknown launch path is asserted.
    A new campaign will rebuild a verified train-only common upstream tower. Numerical
    readout-error differences between fitted heads are not an information-theoretic
    decomposition.'
---

## Description
The product's whole architecture is encoder -> channel fusion -> projector -> 96 soft tokens -> frozen MedGemma-1.5-4b-it + LoRA. Every arm in this project's history has varied something UPSTREAM of the LLM (patch length, pooling operator, caption content, slot count, state supervision, roster) or inside the LoRA recipe. Not one has asked whether the LLM contributes anything at all.

WHY THE QUESTION IS LIVE, in two directions that disagree.
- Tan et al., NeurIPS 2024 (arXiv:2406.16964) replaced the LLM in three published LLM-for-time-series systems with a single attention layer and lost nothing. The multimodal-ai skill lists this as pitfall 12: "wrapping a frozen LLM around time series without ablating the LLM -- replacing it with a simple attention layer has changed performance by ~0.003".
- Qiu et al. 2026 (arXiv:2602.14744) rebut at 8B observations: LLMs DO help, specifically under distribution shift and cross-domain transfer, and pre-alignment beats post-alignment in >90% of tasks. A 21-corpus training set scored on unseen subjects is closer to Qiu's regime than to Tan's benchmarks.

WHAT THIS PROJECT ALREADY KNOWS THAT BEARS ON IT. The encoder side is strong where the emitted side is not: hr_mean reads back off frozen embeddings at R2 0.971-0.99, while the LLM's emitted hr_peak has NEGATIVE skill against always-predict-median on every seed ever measured. E28 found that on frozen LM hidden states a plain RidgeCV BEAT every non-linear readout tried (10.270 bpm vs 11.776 GBT / 11.902 MLP, CI95 of the difference entirely above zero) and concluded the cap is INFORMATION, not readout capacity. A refuted hypothesis on the same problem argued from Petrov et al. (ICLR 2024, arXiv:2310.19698) that a prefix can only bias the attention block's output, never change what the LM computes. All of that is consistent with "formatter" and none of it tests it.

WHAT WOULD RESOLVE IT. Hold the frozen tower fixed and swap ONLY the consumer: MedGemma+LoRA against a small attention readout over the identical 96 soft tokens, trained on the same roster, scored on the same rows, with a mean-pool+ridge shallow floor and a random-init tower as controls.

THE MEASUREMENT MUST BE SPLIT-HONEST, and this is not optional here. Rule-out `that-the-scorer-s-split-heldout-flag-makes-the-d88faa` establishes that 125 of the 128 rows in the current "held-out" captures are subjects the model trained on. The `eval-leakage-audit` skill's corollary is that a leak is NOT symmetric across arms -- an arm with more capacity to memorise exploits it more -- and this comparison is a 4-billion-parameter LoRA'd LLM against a ~2-million-parameter head over 27 subjects. A leaked comparison here is biased toward the LLM by construction, so both consumers must train under `split: train` and be scored only on val/test subjects.

RESOLVED 2026-09-03 — see the dated update: not load-bearing (head 0.812 vs LLM 2.843), 75% of the loss is in the forward pass (hidden-state ridge 2.331), and a narrator over the head's numbers reads 0.814 with no training.

## Updates
- **2026-09-01T20:52:57+00:00** — 2026-09-01 — TWO ADDENDA TO exp-t1-tan-ablation-heads-20260901, both measured after it closed.

(1) EXACT REPRODUCIBILITY. The head arms were re-run end to end from the same seed to produce per-row predictions for the pending paired comparison. The loss trajectory is identical step for step and the scores match to six decimals: attention head hr_raw MAE 1.121046 both runs, ridge mean-pool 1.161078 both runs, trivial 6.881492. Worth recording because this project spent months on effects smaller than its own seed band; on THIS instrument the head arms carry no seed noise at all, because nothing in the path is stochastic once the tokens are cached.

(2) THE CROSS-DEVICE SLICE, which Track 0 asked for and which Prajod & Andre 2022 (arXiv:2210.06225) says is where deep models fall behind feature models. Per-dataset, attention head on the trained tower:

  dataset                 n     MAE   trivial   corr
  pmdata                514   1.043    7.910   0.992
  lifesnaps             906   1.090    5.883   0.983
  big_ideas_glycemic     31   2.013    4.339   0.944
  dryad_ifh_affect       28   2.267    9.779   0.974
  mmash                   6   2.550    5.701   0.968

`dryad_ifh_affect` is the ONLY cohort in the held-out pool that is absent from the training roster altogether -- it is not in band04's 6-dataset dataset_filter, so no stage of this chain, including Phase A and Phase B, ever saw it. It is therefore the one genuinely out-of-roster, cross-device measurement available anywhere in this project, and it is also the 3 rows that were the entire honest-generalisation content of the incumbent's leaked 128-row capture.

On it the head reads MAE 2.267 against a trivial 9.779 -- skill +7.5 bpm, corr 0.974, the LARGEST skill of any cohort here because dryad's HR spread is the widest. So on the one cohort where Prajod predicts the deep readout should fall behind handcrafted features, it does not fall behind at all; it degrades from 1.09 to 2.27 bpm while the baseline it must beat degrades from 5.88 to 9.78.

CAVEAT THAT BINDS THIS: n=28, one cohort, and it is a READOUT task (hr_raw is the masked mean of an input channel), not the forecasting/classification setting Prajod tested. It is a reassuring data point, not a refutation of his result, and it should not be quoted as one.
- **2026-09-01T20:55:27+00:00** — 2026-09-01 — HOW exp-t1's ARMS MAP ONTO TAN'S ACTUAL ABLATION TAXONOMY, read from arXiv:2406.16964v2 section 3.2 rather than from its abstract, plus the one confound the design cannot remove.

TAN'S THREE ABLATIONS: (a) `w/o LLM` -- remove the language model entirely, pass the input tokens straight to the reference method's final layer; (b) `LLM2Attn` -- replace it with a SINGLE randomly-initialised multi-head attention layer; (c) `LLM2Trsf` -- replace it with a single randomly-initialised transformer block.

MAPPING. exp-t1's ridge mean-pool is `w/o LLM`; its cross-attention head is `LLM2Attn`. `LLM2Trsf` was not run, and the internal evidence says it need not be: ridge and attention land 0.040 bpm apart on 1,485 rows, so consumer capacity is already shown not to move this number, and a transformer block sits between them in capacity.

THE DELIBERATE DEVIATION, which a reader comparing the two papers will otherwise flag as an error. Tan writes: "In the above ablations, we keep left parts of the forecasters unchanged (TRAINABLE)" -- he lets the surrounding encoder adapt to the new consumer. exp-t1 holds the tower FROZEN instead. That is not carelessness: the EiV reference system freezes it (band04 sets freeze_encoder: True and neither train_projector nor train_log_scale), so freezing is precisely what makes both consumers eat identical inputs. Tan's choice is right for his systems, which fine-tune around the LLM; mine is right for this one. The consequence is that exp-t1 is a STRICTER test of the tokens and a WEAKER test of the architecture: a trainable tower would likely do better than 1.121 bpm, not worse.

THE CONFOUND THE DESIGN CANNOT REMOVE, and which must be stated when A0 closes. The LLM arm is trained with token cross-entropy over a JSON string; the head arm regresses a number under smooth-L1. Those objectives are not matched. But this is NOT a nuisance to control away -- it is part of what is being ablated. E28 already established that `lm_head` cannot output a scalar, so a numeric head is not available inside the LLM path at all: emitting numbers as tokens is intrinsic to the decision to use an LLM here, and its cost is a legitimate part of the LLM's column.

THE FOLLOW-UP THAT WOULD LOCALISE THE LOSS, not run here: a linear readout on the LLM's FINAL HIDDEN STATE (E28's design, which reached hr_peak MAE 10.270 bpm with plain RidgeCV) placed beside the projector-token readout on the same pool. If the hidden-state readout is worse than the projector-token readout, the loss happens inside the LLM's forward pass rather than at the emission step -- which is the distinction between "the LLM discards it" and "the decoder cannot say it".
- **2026-09-02T19:02:51+00:00** — 2026-09-02 — THE TOWER-ARTEFACT ESCAPE IS CLOSED; STAYS OPEN ONLY FOR THE LOCALISATION FOLLOW-UP. New rule-out `the-llm-s-emission-loss-was-an-artefact-of-the-incumbent-s-weak-tower`. The obvious objection to exp-t1 was that its 3.382 bpm head-beats-LLM gap came from measuring on the incumbent's weak PL120 tower. Measured on v41rebased's better PL60 tower with the identical head, seed, roster, pool and code (`scratch/t1b_heads_v41_tower.py`), the gap in bpm narrows to +2.031 (CI95 [+1.606, +2.485]) — and the fraction of available skill the LLM DISCARDS goes UP, 58% to 66%: the head reads 0.812 bpm where the LLM emits 2.843, against a 6.876 trivial. On hr_peak the same paired contrast is +3.120 bpm (CI95 [+1.625, +4.686]). So a better representation does not make the LLM a better reader of it, and v41's 1.586 bpm gain over arm A0 is the TOWER improving, not the LLM path.

WHAT REMAINS GENUINELY OPEN, and it is the reason not to resolve this yet: WHERE in the LLM the information is lost. exp-t1's own follow-up — an E28-style readout on MedGemma's FINAL HIDDEN STATE — separates "discarded in the forward pass" (the soft tokens never reach the residual stream in usable form) from "lost at emission" (the information is there and `lm_head`'s token CE over JSON digits cannot express it). Those two have opposite fixes: the first is an interface problem, the second is an objective problem, and the product decision differs. Nobody has run it; it needs no retrain.
- **2026-09-02T20:57:32+00:00** — 2026-09-02 23:55 — LITERATURE + VAULT TRACE, and Track A pre-registered (hypothesis `a-the-llm-keeps-hr-raw-in-its-residual-stream-and-loses-it-at-emission`). Kept OPEN deliberately despite doctor's 'answered but open' flag: the confirmed hypothesis answers 'is it load-bearing' (no: head 0.812 vs LLM 2.843 on the PL60 tower, 66% of the tokens' skill discarded, t1b); the open question is WHERE the loss is, and that decides D-a vs E/G.

WHAT THE VAULT ALREADY HAD (all on OLD adapters, never on the same rows as the head): E36 2026-08-06 (logs/e36_full.log, medgemma_12ch_e33.seed_1) — hr_raw RidgeCV on the residual stream at the emission token 1.48 bpm vs donor-null 6.98 / zeroed 6.94 / always-median 7.59: the forward pass KEEPS most of it. exp-e25 — hr_peak linearly present at the emission position (R2 +0.51, MAE 11.8 vs 20.1). exp-e26 — measured in lm_head's real digit function class the readout still beats median by 4.2 bpm but carries coarse magnitude only. exp-037 — decoding the full conditional pmf (median/mean) instead of argmax took hr_peak corr 0.55 -> 0.795 and skill -1.91 -> +1.86 bpm with NO retrain; its lesson, verbatim: 'separate decode rule from representation before concluding the signal is absent'. Nobody carried that lesson to hr_raw or to a split-honest adapter.

WHAT THE LITERATURE SAYS (papers linked): Song & Bahri 2025 (arXiv:2501.19383) — a decoder head over digit tokens MATCHES a pointwise head on the same features, under digit-by-digit tokens, CONSTRAINED decoding, no interleaved language, and the loss-matched point estimate (median for MAE); their Sec. 5 names LLM vocabularies without digit tokens, top-p sampling and interleaved template tokens as the expected losses — i.e. our exact configuration. Chen 2025 (arXiv:2512.06533) — token-CE never sees magnitude; a sequence-level |y_hat - y| reward (GRPO) beats both decoding baselines and regression heads. Biderman 2024 (arXiv:2405.09673) — rank-32 LoRA learns far less than full FT; full FT perturbations are rank 10-100x higher. SensorLM (arXiv:2506.09108) — Google's sensor-language model reports NO LLM-vs-head ablation; its text side is a small decoder; generic LLMs fed sensor days as text are at chance.

READING: 'formatter' is now split into two testable halves — (i) the forward pass discards (interface problem; fix = E/G or the narrator architecture), (ii) the emission step discards (objective/format problem; fix = D-a, then Chen-style reward). E36 on the old adapter says mostly (ii). Track A measures it on v41rebased_c on the 128 rows every other tower number was measured on; D-a (`d-a-number-first-digit-constrained-phase-c-target-recovers-emission-loss`) is pre-registered with A's hidden-state number as its g0 precondition.
- **2026-09-02T21:05:37+00:00** — 2026-09-03 00:20 — THE FIELD HAS ALREADY MOVED THE LLM OFF THE SENSOR PATH, and two papers now report the same sign as exp-t1/t1b on numeric targets. (1) SensorFM (arXiv:2605.22759, Google 2026, added to vault): the deployed architecture is sensor FM -> PCA-50 + LINEAR heads -> Personal Health Agent that consumes the heads' PREDICTIONS; in the 1,860-rating clinician study the LLM-given-metrics-only condition is worst on every axis (p<0.001) and the LLM-given-head-predictions condition is indistinguishable from LLM-given-ground-truth (p=0.396). No condition feeds soft tokens to the LLM. (2) CHA-PPGHR (arXiv:2502.12836): an LLM orchestrating a signal-processing tool chain reads HR at ~2.8 bpm MAE against ECG where GPT-4o reading the data directly reads ~8.9 — the same ~3x gap as t1b's 0.81 head vs 2.84 LLM. (3) PH-LLM (arXiv:2406.06474): even with a learned multimodal encoder, the LLM only MATCHES specialised discriminative heads on sleep-outcome prediction; never exceeds them. NO published result anywhere in the OVERVIEW.md survey (29 systems) shows soft-token injection into a frozen LLM beating a task head on a numeric physiological target. Consequence for this problem: 'formatter' is not an EiV pathology, it is the field's settled position for numeric targets; what remains EiV-specific is Track A's localisation (emission vs forward pass) because it decides whether the soft-token path is worth ONE more Phase C retrain (D-a) before the narrator architecture becomes the product path. Diagnostic eiv_narration_numeric_fidelity_mae_bpm registered for that architecture.
- **2026-09-03T06:43:54+00:00** — 2026-09-03 — RESOLVED. Both halves are now measured on the same 128 split-honest rows of the v41rebased PL60 tower. (1) LOAD-BEARING: no. Attention head over the 96 soft tokens 0.812 bpm vs MedGemma+LoRA emission 2.843 (trivial 6.876), rule-out `the-llm-s-emission-loss-was-an-artefact-of-the-incumbent-s-weak-tower`. (2) WHERE THE LOSS IS (exp-a-hr-tower-localisation-20260903): RidgeCV on the residual stream at the hr_raw emission token, zeroed-scaffold context, reads 2.331 (donor-null 7.645, zeroed 7.738). So 1.519 of the 2.031 bpm (75%) is lost in the FORWARD PASS and 0.512 at emission; medianK decoding does not help (3.032). The emission-fix branch (D-a) is therefore unpowered against the measured seed floor (sigma 0.208) and was not run. (3) THE ALTERNATIVE WORKS WITH NO TRAINING (exp-n-narrator-fidelity-20260903, supports): base MedGemma with the adapter disabled, handed the head's numbers in the prompt, repeats them at 0.0245 bpm fidelity; end-to-end 0.814 vs truth. Product path = head -> narrator, matching SensorFM / PH-LLM / CHA-PPGHR. What this problem does NOT settle: whether the narrator carries STATE (g3 of exp-n unevaluated; follow-up pre-registered 2026-09-03), and the soft-token bridge as a research object under full fine-tuning (Track G, unfunded).
- **2026-09-18T11:14:24+00:00** — PROVENANCE CORRECTION 2026-09-18: the documented scratch/run_v41_rebased.sh writes split=train to YAML, but src/cli_train.py at saved commit ad78bc2082d0 forwards split only for C; Phase A allowed_subject_keys was not forwarded (saved allowed_subject_count=null) and B had no allowlist. This static evidence blocks the WHOLE-PIPELINE subject-held-out claim for the documented path. Stored captures/numerical comparisons are preserved; no historical rerun or unknown launch path is asserted. A new campaign will rebuild a verified train-only common upstream tower. Numerical readout-error differences between fitted heads are not an information-theoretic decomposition.

## Links
Cites [[tan-2024-llm-useful-timeseries-2406-16964]], [[qiu-2026-rethinking-llm-tsf-2602-14744]], [[song-2025-decoding-based-regression-2501-19383]], [[chen-2025-rl-decoding-regression-2512-06533]], [[biderman-2024-lora-learns-less-2405-09673]], [[sensorlm-2025-2506-09108]], [[sensorfm-2026-2605-22759]], [[feli-2025-cha-ppghr-agent-2502-12836]], [[phllm-2024-personal-health-llm-2406-06474]].

#optihealth-eiv #matched-baseline
