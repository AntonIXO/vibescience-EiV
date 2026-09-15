---
created_at: '2026-08-17T22:06:19+00:00'
former_ids: []
id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
paper_refs:
- yang-2022-privileged-features-distillation-2209-08754
- sensorfm-2026-2605-22759
- feli-2025-cha-ppghr-agent-2502-12836
- phllm-2024-personal-health-llm-2406-06474
problem_tags:
- optihealth-eiv
- conditioning-collapse
- subject-heldout
- matched-baseline
status: open
target: null
title: Phase C is trainable only for JSON slot filling; the product surface is conversational
  annotation + coaching
topic_tags:
- target-style
- optihealth
- medgemma
- deployment-distribution
- readout
updated_at: '2026-09-03T12:56:50+00:00'
updates:
- at: '2026-09-01T08:11:55+00:00'
  text: "TRAIN/SERVE PROMPT SKEW MEASURED 2026-09-01 (scratch/_train_serve_prompt_skew.py,
    scratch/_prompt_budget_audit.py, scratch/_context_resolution_probe.py). This record's
    'second, independent defect' is not only still live -- it is ASYMMETRIC, and the
    asymmetry was never noticed.\n\nTHE RECORD SAID: json_slots is 327 templated tokens
    against effective_max_prompt_tokens 256, silently truncated at TRAINING AND INFERENCE,
    losing the temp/state key definitions and the 'output ONLY the JSON object' line.\n\nMEASURED
    NOW, on the live profile and the live tokenizer:\n  prompts/json_slots_prompt.md
    \  1,046 chars, 317 tokens\n  TRAIN budget                   256   (pipeline.py
    -> resolve_prompt_token_budget)\n  SERVE budget                   512   (scratch/emission_diversity_audit.py:237,
    HARDCODED)\n  SKEW                           61 tokens present at serve, absent
    at train\n\nSo the truncation is real but it is NOT symmetric. Every arm is TRAINED
    on a 256-token decapitated prompt and TESTED on the whole 317-token one. The 61
    tokens the model has never seen during training, verbatim:\n  '\", \"elevated\")
    \ - \"temp\": string (\"low\", \"normal\", \"elevated\")  - \"state\": string
    (\"stress\", \"flow\", \"neutral\", \"burnout\")  Do NOT include any markdown
    formatting, code blocks, or explanatory text. Output ONLY the JSON object.'\n\nROOT
    CAUSE OF THE TRAIN SIDE, localised (this is NOT the old hardcoded-256 story):
    resolve_model_context_tokens read only the config's TOP level, and medgemma-1.5-4b-it
    is a Gemma3Config whose top level carries no context attribute at all -- max_position_embeddings
    (131072) and sliding_window (1024) live under text_config. The resolver returned
    None, resolve_prompt_token_budget took its auto_fallback of 256, and the tokenizer's
    model_max_length is an int64 sentinel that is correctly discarded. Nothing was
    left.\n\nWHY THIS MATTERS BEYOND TIDINESS. Two of the defects this problem is
    ABOUT sit exactly in the truncated tail. The `state` slot is documented in the
    record as 'a near-constant prior with arm-dependent polarity' (v5c stress 139/200,
    v4w neutral 198/200, v5d flow 65/200) -- and its enum was never in the training
    prompt. The JSON-termination defect that blocked v33's promotion (47/128 dense
    emissions looping '\"temp\":\"normal\",\"state\":\"flow\"' forever) loops on the
    two keys whose definitions are cut, and the 'Output ONLY the JSON object' instruction
    is cut with them. Neither is proof, but both are now testable rather than mysterious.\n\nFIXED
    BEHIND A FLAG, NOT LANDED: EIV_RESOLVE_NESTED_CONTEXT=1 makes the resolver scan
    text_config. Default OFF, deliberately -- the Phase-C seed family was mid-flight
    when this was found, and landing it would have put a prompt change inside a family
    whose entire purpose is to isolate the seed. With the flag: context 1024 (the
    resolver takes the MINIMUM, so sliding_window binds, not 131072), budget 456,
    prompt fits with 139 tokens spare, skew 0. Commit 9299890, guard tests/test_context_resolution_guard.py
    (7 tests, one of which fails if the flag ever defaults on).\n\nCONSTRAINT ON ANY
    FIX ARM: the two budgets must move TOGETHER. Fixing only the train side removes
    the skew; fixing only the serve side INVERTS it. And every arm from v28 to v37
    is comparable to every other precisely BECAUSE they all ran under this skew --
    the fix is a recipe change and needs its own one-variable arm, not a quiet landing."
- at: '2026-09-01T08:50:27+00:00'
  text: "THE PROVENANCE DEFECT IS ALREADY SHIPPING IN v36labels, NOT A FUTURE RISK.
    I set out to ADD a valence clause to _label_clauses(). Reading the shipped builder
    first showed both `valence` and `stress_level` are ALREADY in its `order` list,
    rendered as the raw native number ('valence {v:.1f}', 'stress {v:.1f}').\n\nMeasured
    by running the SHIPPED builder over the Phase A roster (scratch/_shipped_clause_provenance.py,
    9,428 windows):\n  windows receiving any clause : 3,310 (35.1%)\n  valence clauses
    \             : 1,298\n  stress clauses               : 1,678\n\nAnd the emitted
    numbers are not on one axis:\n\n  'valence X'          n     min     p50     max\n
    \   dapper           548    1.00    3.30    5.00     (native scale 1-5)\n    popane_s7
    \       213    3.10    6.40    9.70     (native 0-10)\n    popane_s5        199
    \   3.90    5.70    8.50\n    popane_s6        140    1.40    4.95    8.90\n    popane_s3
    \       137    2.50    5.40    8.80\n    k_emocon          31    2.30    3.00
    \   3.90     (native 1-5)\n    case              30    4.90    5.15    5.60     (native
    0.5-9.5)\n\n  'stress X'\n    pmdata         1,665    0.00    3.00    5.00     (native
    0-5)\n    brain_wearable    13    1.40    1.50    1.60\n\nSo the caption 'valence
    3.3' describes the TOP of DAPPER's range and the BOTTOM of POPANE's. InfoNCE keeps
    what the two views share, so the encoder is being taught that the same physiology
    pairs with numerically different text depending on which corpus supplied the window
    -- which is the caption-provenance shortcut this project already measured at bal-acc
    0.9945 against 0.1250 chance. The clause is a source indicator dressed as an affect
    label.\n\nTHE FIX IS THE TRANSFORM, NOT THE CLAUSE, AND THE TWO CHANNELS NEED
    DIFFERENT ONES (CLAUDE.md states they are not interchangeable and warns against
    getting it backwards):\n  valence      -> scale_to_declared_range. Six of seven
    sources agree once scaled to their own declared spans (p50 0.490-0.510 across
    two labs and three instruments); popane_s7 is the documented outlier at 0.651
    and should be excluded, leaving 1,085 windows (84% of the channel).\n  stress_level
    -> SubjectQuantileScaler. Its sources have disjoint supports (pairwise Jaccard
    0.032-0.033) and do NOT declare comparable ranges, so no declared-range scaling
    exists.\n\nSTATUS OF v36labels. This does not retract its results: it reached
    128/128 JSON termination and the best supabase numbers of the campaign, and the
    seed family now running will say whether that survives. But its caption carries
    a known shortcut on 35.1% of Phase A windows, so a v36-vs-v28 win cannot be attributed
    to 'labels help' without ruling out 'the model learned which corpus this is'.
    The next arm is the SAME clause set with the two transforms applied -- one variable
    against v36labels, and the first arm in this campaign that would make the label
    clauses mean the same thing across sources."
- at: '2026-09-01T08:57:45+00:00'
  text: "LEAKAGE QUANTIFIED, AND v39scaled HAS A NAMED LIMITATION IT MUST BE READ
    WITH. Measured before the arm reaches Phase A (scratch/_clause_leakage_audit.py).
    Metric: balanced accuracy of predicting WHICH CORPUS a window came from, using
    ONLY the number the caption states, nearest-class-median rule, balanced so a dominant
    corpus cannot inflate it.\n\n  channel        mode     srcs  bal-acc  chance    lift
    \  windows\n  stress_level   native      2    0.879   0.500  +0.379    1,678\n
    \ valence        native      7    0.437   0.143  +0.294    1,298\n  valence        SCALED
    \     7    0.267   0.143  +0.124    1,298\n  arousal        native      3    0.709
    \  0.333  +0.376      609\n  arousal        SCALED      3    0.465   0.333  +0.131
    \     609\n\nTHE TRANSFORM WORKS: scaling cuts leakage 2.4x on valence (+0.294
    -> +0.124) and 2.9x on arousal (+0.376 -> +0.131). This is the first direct measurement
    that the transform does what it was built for, rather than an argument that it
    should.\n\nBUT THE SHORTCUT SURVIVES BY ANOTHER ROUTE. stress_level keeps a native
    lift of +0.379 on 1,678 windows -- MORE leakage, on MORE windows, than either
    channel the arm fixes. It is left native for a defensible reason (its sources
    have disjoint supports and declare no comparable ranges, so no declared-range
    map exists; it needs SubjectQuantileScaler, a different arm). The consequence
    is that after v39scaled the encoder can still read provenance off the caption
    on a larger window set than before the fix touched.\n\nWHAT THIS DOES TO THE ARM'S
    INTERPRETATION, stated now rather than after the numbers arrive:\n  - A POSITIVE
    result (gates pass, deployment holds or improves) is still meaningful: less leakage
    did not cost accuracy.\n  - A NULL result is NOT clean. 'Removing the shortcut
    does not matter' and 'the shortcut was still available through stress_level' predict
    the same outcome. A null must be reported with that ambiguity, not as evidence
    against the mechanism.\n  - Extending the arm to stress_level would fix the ambiguity
    but costs the one-variable property, and SubjectQuantileScaler carries its own
    documented failure mode (CASE sessions fit inside one patch, so a patch mean of
    within-subject ranks is 0.5 by construction). So the honest sequence is v39 first,
    then a stress_level arm, not one combined change.\n\nThe arm ships as queued with
    this limitation on the record."
- at: '2026-09-01T09:02:31+00:00'
  text: "LABEL REACH BY PHASE, MEASURED FROM THE CODE PATHS (scratch/_label_reach_by_phase.py).
    The standing goal is 'labels in all three phases'. Two of the three inherit them;
    the third structurally cannot, and that is a target-design question rather than
    a caption question.\n\nPHASE A -- FULL REACH. _label_clauses() renders 12 continuous/ordinal
    channels into the caption on 3,310 of 9,428 roster windows (35.1%).\n\nPHASE B
    -- INHERITS FOR FREE, which was not obvious and is worth recording. run_phase_b_orthogonalization
    builds SensorTextPairDataset (2 references) and reads caption_style (8 references);
    it never constructs InstructionDataset and never calls _label_clauses itself.
    So Phase B consumes the SAME caption Phase A does, and any clause work -- v36labels'
    label clauses, v39scaled's shared-axis fix -- reaches B without a line of B-specific
    code. It also means the provenance defect reaches B: whatever the caption leaks,
    B's contrastive objective is trained on.\n\nPHASE C -- STRUCTURALLY LABEL-BLIND
    EXCEPT FOR ONE SLOT. Read _build_json_slots_target (datasets.py:2525) slot by
    slot:\n  heart_rate_bucket, hr_pct, hr_raw, hr_peak   <- channel 0 of the TENSOR\n
    \ hrv_bucket, hrv_pct, hrv_raw                 <- channel 1\n  deep_pct, rem_pct,
    awake_pct                 <- channels 4, 5, 2\n  rr                                           <-
    channel 10\n  temp                                         <- channel 11\n  state
    \                                       <- state_series(metadata) + infer_state_label(metadata)\nTwelve
    of thirteen keys are derived from the SENSOR TENSOR. Exactly one, `state`, comes
    from the label events, and it enters through the state reducer rather than through
    label_summary(). The target never calls label_summary() and never reads subject_traits.\n\nCONSEQUENCE
    FOR THE GOAL: 34 of the 35 declared label channels cannot reach Phase C in any
    form, no matter what Phase A's caption says. 'Utilise all labels in all three
    phases' is therefore not achievable by extending _label_clauses() -- it needs
    the Phase C TARGET to carry more label-derived keys, which is a schema change
    to json_slots with its own costs (the 13-key schema is what the prompt documents,
    and the prompt is already 317 tokens against a 256 train budget).\n\nWHAT IS NOT
    WRONG HERE: the one label slot that does exist is correctly disciplined. `state`
    is OMITTED when state_series(metadata) is empty, with the reason recorded inline
    -- writing it unconditionally taught 45.9% of lora_proxy_12ch_v11full's rows to
    emit a verdict from an unscored window, and value_loss_weight up-weights the @@...@@
    span so the fabricated token carried MORE gradient than measured ones. Any new
    label slot must inherit that discipline: omit when unobserved, never default."
- at: '2026-09-01T10:24:08+00:00'
  text: "PHASE C'S ONE LABEL SLOT IS 72.7% NEUTRAL, AND HALF THE ROWS CARRY NO STATE
    OBSERVATION AT ALL (scratch/_state_slot_content.py). Follows directly from the
    finding that _build_json_slots_target emits 13 slots with exactly one -- `state`
    -- derived from a label.\n\nBAND04 (7,592 windows, the roster every shipped arm
    runs):\n  state_series() non-empty on 3,735 windows = 49.2%\n  infer_state_label
    over ALL windows: neutral 5,517 (72.7%) | stress 1,079 (14.2%) | flow 996 (13.1%)\n
    \ -> a constant 'neutral' emitter scores 72.7% on the campaign's only label slot\n\nWIDE
    ROSTER (9,530 windows): 46.2% carry a state; neutral 76.2% / stress 13.1% / flow
    10.7%. Widening makes the prior WORSE, not better -- the affect corpora it admits
    contribute almost entirely neutral.\n\nTHE PER-DATASET BREAKDOWN IS THE REAL FINDING.
    On the wide roster, TWELVE of twenty datasets emit ONLY neutral, across 2,304
    windows:\n  big_ideas_glycemic 145, brain_wearable_monitoring 21, case 30, dapper
    862, dreamt 100, k_emocon 31, mmash 42, optihealth_supabase 302, popane_s3 142,
    popane_s4 83, popane_s5 199, popane_s6 140, popane_s7 213, wesad 15\nOnly four
    datasets carry a non-degenerate state distribution: lifesnaps (3,109/990/720),
    pmdata (1,819/359/6), nurse_stress (8/19/75) and consumer_grade_wearables (1/0/34).\n\nNOTE
    WHAT THIS MEANS FOR THE AFFECT CORPORA SPECIFICALLY. CASE, DAPPER, K-EmoCon and
    all five POPANE studies were admitted to the wide roster precisely because they
    carry valence and arousal -- and every one of them reduces to `neutral` at the
    window level. Their film-clip inductions are 40-120 SECOND events inside a 1,440-minute
    window; infer_state_label reduces the window by majority over annotated epochs,
    so a 2-minute anger clip in a 41-minute session that is itself one window loses
    to the surrounding baseline. The state slot cannot see an induction shorter than
    the window's own majority.\n\nSO THE SINGLE LABEL CHANNEL PHASE C CARRIES IS,
    ON ITS OWN ROSTER, A 72.7% PRIOR CONCENTRATED IN TWO DATASETS. That is not a reason
    to widen the roster (it gets worse) and not a reason to add label slots (measured
    separately: 26 of 35 channels reach under 3% of the wide roster). It is a statement
    about what a DAY-LEVEL window can express: the corpus's affect labels are minute-scale
    events and the target is a daily summary.\n\nDOES NOT TOUCH the hr_raw result,
    which is what the campaign is actually scored on and which is a genuine per-window
    continuous read. This is specifically about the state slot's information content."
---

## Description
OPEN QUESTION (registered 2026-08-18, exp-chat-coach-20260818).

Every Phase-C arm ever trained on this project uses `target_style: json_slots` — a 13-key
schema whose entropy sits in slot values. The optiHealth product surface is a coaching
assistant that must ANNOTATE and EXPLAIN a day in prose. Nothing in the pipeline has ever
been trained for that, so 'can the same soft-token prefix support a prose surface without
losing numeric grounding?' is UNANSWERED, not answered negatively.

WHAT IS ALREADY KNOWN (do not re-litigate):
- json_slots hr_raw emission skill is POSITIVE and seed-robust: dense-128 skill
  +4.08/+2.44/+4.36 bpm at seeds 42/43/44 (v9band04), corr 0.87/0.71/0.91.
- hr_peak skill is NEGATIVE on every seed (-2.88/-2.73/-3.16). Encoder excursion limit,
  not a target-style property.
- Phase-C seed band: 0.0781 p4 hr_acc, 2.42 bpm hr_peak skill, 2.26 bpm hr_raw skill.
  Any chat-vs-json delta smaller than that is noise.

THE CONFABULATION FRAMING IS REFUTED (measured before launch, 2026-08-18). An earlier
version of this framing said json_slots 'forces a value into every key' and therefore
fabricates absent channels. `_build_json_slots_target` guards every slot with np.isfinite,
so absent channels are ALREADY omitted from the target, and shipped arms emit a slot their
own row's target would have omitted only 5/2585 (0.2%, v9band04_s42 supabase), 9/1290
(0.7%, dense-128), 3/2556 (0.1%, v11lmhead supabase). Whole-channel confabulation is real
but small and json_slots is not its cause. Script: scratch/where_is_confabulation.py.

THE SURVIVING, MEASURED DEFECT IS THIN EVIDENCE. np.isfinite passes on ONE observed epoch
out of 1440. On optihealth_supabase — the PRODUCTION population — respiratory rate is
backed by 1-4 observed epochs on 213/223 rows and skin temperature on 219/223; only 6 and
4 rows are truly absent. Corpus-wide 227 rows state rr and 219 state temp from <15 observed
epochs. json_slots states these as flat facts because a JSON enum has nowhere to put a
hedge. Script: scratch/thin_evidence_census.py.

A SECOND, INDEPENDENT DEFECT FOUND WHILE SCOPING: the json_slots prompt is 327 templated
tokens against an effective_max_prompt_tokens of 256, so it is SILENTLY TRUNCATED at
training and inference. The lost 71 tokens are the tail of the schema — the `temp` and
`state` key definitions and the 'output ONLY the JSON object' instruction. Every shipped
Phase-C arm was trained with a decapitated prompt. Script: scratch/check_prompt_truncation.py.
The chat_coach prompt is 216 templated tokens and fits. This is a confound in the pending
A/B and must be stated with any result: chat gets a whole prompt, json does not.

DATA SITUATION (load-bearing): the historical Phase-C training bundle data/canonical/
(10344,1440,12) is GONE from this disk. Only eval_v3_1min (5955,1440,12) survives, in the
pre-epoch-label format. Converted to the current contract as data/canonical_1min_conv
(17,725 label events recovered through the declared labels.yaml source mapping). Label
TIMESTAMPS were destroyed upstream and are NOT recoverable; events sit at sentinel epochs
so the declared majority reduction in infer_state_label returns the historical day-level
answer. NEVER use this bundle for state_at(epoch) or within-day transition claims.
Because the training corpus changed, a FRESH json_slots control is retrained on the same
bundle — comparing chat against the shipped v9band04 numbers would confound target_style
with the corpus change.

## Synthesis
STATE OF KNOWLEDGE, 2026-09-03.

WHAT WE BELIEVE. Phase C (LoRA on frozen MedGemma over 96 soft tokens, JSON-slot target) is not the product surface and cannot become it by changing the target: exp-045 and exp-v10 refuted free-text emission; exp-a (2026-09-03) showed 75% of the LLM's hr_raw loss (1.52 of 2.03 bpm) is in the FORWARD PASS, so no target format or decode rule can recover it (D-a withdrawn unpowered). The product surface is the NARRATOR: base MedGemma-1.5-4b-it with the adapter DISABLED and no soft tokens, handed a head's outputs in the prompt. Measured on split-honest rows: numbers carried at 0.0245 bpm fidelity, end-to-end 0.814 vs the soft-token path's 2.843 and a 6.876 trivial (exp-n, supports); state carried at 666/666 agreement with probability MAE 0.0026 when the classifier's DECISION is handed as a label (exp-n3, supports), against 95.2% when handed a probability plus a threshold rule (exp-n2: every miss at p in [0.30, 0.50)). Narrated per-device AUC equals the thresholded classifier exactly. This is SensorFM's condition (LLM given head predictions ~ LLM given ground truth) reproduced on our own heads with zero training.

RULED OUT. Soft-token injection beating a head on a numeric target (t1/t1b; no published counter-example in 29 systems). Emission-side fixes (number-first target, medianK, constrained decoding) as the route to the head's 0.812. Handing the narrator a rule to apply. Comparing a binary narrated verdict to a continuous-probability AUC.

OPEN. Clinical quality of the narration -- needs SensorFM's five-axis rubric with raters, not a scorer; nothing here measures it. A narrator that must decide WHAT to say (multiple heads, conflicting signals) -- untested; every experiment handed it one or two numbers and one label. Whether a rank-32 LoRA is the wrong instrument for any future soft-token research (Biderman 2024; Track G, unfunded).

PRODUCT CONSEQUENCE. Prompt = head label + numbers, never a threshold; prefill the assistant turn (the base model plans for 160 tokens otherwise); HF_HUB_OFFLINE on inference drivers. The soft-token bridge remains a research object only.

## Updates
- **2026-09-01T08:11:55+00:00** — TRAIN/SERVE PROMPT SKEW MEASURED 2026-09-01 (scratch/_train_serve_prompt_skew.py, scratch/_prompt_budget_audit.py, scratch/_context_resolution_probe.py). This record's 'second, independent defect' is not only still live -- it is ASYMMETRIC, and the asymmetry was never noticed.

THE RECORD SAID: json_slots is 327 templated tokens against effective_max_prompt_tokens 256, silently truncated at TRAINING AND INFERENCE, losing the temp/state key definitions and the 'output ONLY the JSON object' line.

MEASURED NOW, on the live profile and the live tokenizer:
  prompts/json_slots_prompt.md   1,046 chars, 317 tokens
  TRAIN budget                   256   (pipeline.py -> resolve_prompt_token_budget)
  SERVE budget                   512   (scratch/emission_diversity_audit.py:237, HARDCODED)
  SKEW                           61 tokens present at serve, absent at train

So the truncation is real but it is NOT symmetric. Every arm is TRAINED on a 256-token decapitated prompt and TESTED on the whole 317-token one. The 61 tokens the model has never seen during training, verbatim:
  '", "elevated")  - "temp": string ("low", "normal", "elevated")  - "state": string ("stress", "flow", "neutral", "burnout")  Do NOT include any markdown formatting, code blocks, or explanatory text. Output ONLY the JSON object.'

ROOT CAUSE OF THE TRAIN SIDE, localised (this is NOT the old hardcoded-256 story): resolve_model_context_tokens read only the config's TOP level, and medgemma-1.5-4b-it is a Gemma3Config whose top level carries no context attribute at all -- max_position_embeddings (131072) and sliding_window (1024) live under text_config. The resolver returned None, resolve_prompt_token_budget took its auto_fallback of 256, and the tokenizer's model_max_length is an int64 sentinel that is correctly discarded. Nothing was left.

WHY THIS MATTERS BEYOND TIDINESS. Two of the defects this problem is ABOUT sit exactly in the truncated tail. The `state` slot is documented in the record as 'a near-constant prior with arm-dependent polarity' (v5c stress 139/200, v4w neutral 198/200, v5d flow 65/200) -- and its enum was never in the training prompt. The JSON-termination defect that blocked v33's promotion (47/128 dense emissions looping '"temp":"normal","state":"flow"' forever) loops on the two keys whose definitions are cut, and the 'Output ONLY the JSON object' instruction is cut with them. Neither is proof, but both are now testable rather than mysterious.

FIXED BEHIND A FLAG, NOT LANDED: EIV_RESOLVE_NESTED_CONTEXT=1 makes the resolver scan text_config. Default OFF, deliberately -- the Phase-C seed family was mid-flight when this was found, and landing it would have put a prompt change inside a family whose entire purpose is to isolate the seed. With the flag: context 1024 (the resolver takes the MINIMUM, so sliding_window binds, not 131072), budget 456, prompt fits with 139 tokens spare, skew 0. Commit 9299890, guard tests/test_context_resolution_guard.py (7 tests, one of which fails if the flag ever defaults on).

CONSTRAINT ON ANY FIX ARM: the two budgets must move TOGETHER. Fixing only the train side removes the skew; fixing only the serve side INVERTS it. And every arm from v28 to v37 is comparable to every other precisely BECAUSE they all ran under this skew -- the fix is a recipe change and needs its own one-variable arm, not a quiet landing.
- **2026-09-01T08:50:27+00:00** — THE PROVENANCE DEFECT IS ALREADY SHIPPING IN v36labels, NOT A FUTURE RISK. I set out to ADD a valence clause to _label_clauses(). Reading the shipped builder first showed both `valence` and `stress_level` are ALREADY in its `order` list, rendered as the raw native number ('valence {v:.1f}', 'stress {v:.1f}').

Measured by running the SHIPPED builder over the Phase A roster (scratch/_shipped_clause_provenance.py, 9,428 windows):
  windows receiving any clause : 3,310 (35.1%)
  valence clauses              : 1,298
  stress clauses               : 1,678

And the emitted numbers are not on one axis:

  'valence X'          n     min     p50     max
    dapper           548    1.00    3.30    5.00     (native scale 1-5)
    popane_s7        213    3.10    6.40    9.70     (native 0-10)
    popane_s5        199    3.90    5.70    8.50
    popane_s6        140    1.40    4.95    8.90
    popane_s3        137    2.50    5.40    8.80
    k_emocon          31    2.30    3.00    3.90     (native 1-5)
    case              30    4.90    5.15    5.60     (native 0.5-9.5)

  'stress X'
    pmdata         1,665    0.00    3.00    5.00     (native 0-5)
    brain_wearable    13    1.40    1.50    1.60

So the caption 'valence 3.3' describes the TOP of DAPPER's range and the BOTTOM of POPANE's. InfoNCE keeps what the two views share, so the encoder is being taught that the same physiology pairs with numerically different text depending on which corpus supplied the window -- which is the caption-provenance shortcut this project already measured at bal-acc 0.9945 against 0.1250 chance. The clause is a source indicator dressed as an affect label.

THE FIX IS THE TRANSFORM, NOT THE CLAUSE, AND THE TWO CHANNELS NEED DIFFERENT ONES (CLAUDE.md states they are not interchangeable and warns against getting it backwards):
  valence      -> scale_to_declared_range. Six of seven sources agree once scaled to their own declared spans (p50 0.490-0.510 across two labs and three instruments); popane_s7 is the documented outlier at 0.651 and should be excluded, leaving 1,085 windows (84% of the channel).
  stress_level -> SubjectQuantileScaler. Its sources have disjoint supports (pairwise Jaccard 0.032-0.033) and do NOT declare comparable ranges, so no declared-range scaling exists.

STATUS OF v36labels. This does not retract its results: it reached 128/128 JSON termination and the best supabase numbers of the campaign, and the seed family now running will say whether that survives. But its caption carries a known shortcut on 35.1% of Phase A windows, so a v36-vs-v28 win cannot be attributed to 'labels help' without ruling out 'the model learned which corpus this is'. The next arm is the SAME clause set with the two transforms applied -- one variable against v36labels, and the first arm in this campaign that would make the label clauses mean the same thing across sources.
- **2026-09-01T08:57:45+00:00** — LEAKAGE QUANTIFIED, AND v39scaled HAS A NAMED LIMITATION IT MUST BE READ WITH. Measured before the arm reaches Phase A (scratch/_clause_leakage_audit.py). Metric: balanced accuracy of predicting WHICH CORPUS a window came from, using ONLY the number the caption states, nearest-class-median rule, balanced so a dominant corpus cannot inflate it.

  channel        mode     srcs  bal-acc  chance    lift   windows
  stress_level   native      2    0.879   0.500  +0.379    1,678
  valence        native      7    0.437   0.143  +0.294    1,298
  valence        SCALED      7    0.267   0.143  +0.124    1,298
  arousal        native      3    0.709   0.333  +0.376      609
  arousal        SCALED      3    0.465   0.333  +0.131      609

THE TRANSFORM WORKS: scaling cuts leakage 2.4x on valence (+0.294 -> +0.124) and 2.9x on arousal (+0.376 -> +0.131). This is the first direct measurement that the transform does what it was built for, rather than an argument that it should.

BUT THE SHORTCUT SURVIVES BY ANOTHER ROUTE. stress_level keeps a native lift of +0.379 on 1,678 windows -- MORE leakage, on MORE windows, than either channel the arm fixes. It is left native for a defensible reason (its sources have disjoint supports and declare no comparable ranges, so no declared-range map exists; it needs SubjectQuantileScaler, a different arm). The consequence is that after v39scaled the encoder can still read provenance off the caption on a larger window set than before the fix touched.

WHAT THIS DOES TO THE ARM'S INTERPRETATION, stated now rather than after the numbers arrive:
  - A POSITIVE result (gates pass, deployment holds or improves) is still meaningful: less leakage did not cost accuracy.
  - A NULL result is NOT clean. 'Removing the shortcut does not matter' and 'the shortcut was still available through stress_level' predict the same outcome. A null must be reported with that ambiguity, not as evidence against the mechanism.
  - Extending the arm to stress_level would fix the ambiguity but costs the one-variable property, and SubjectQuantileScaler carries its own documented failure mode (CASE sessions fit inside one patch, so a patch mean of within-subject ranks is 0.5 by construction). So the honest sequence is v39 first, then a stress_level arm, not one combined change.

The arm ships as queued with this limitation on the record.
- **2026-09-01T09:02:31+00:00** — LABEL REACH BY PHASE, MEASURED FROM THE CODE PATHS (scratch/_label_reach_by_phase.py). The standing goal is 'labels in all three phases'. Two of the three inherit them; the third structurally cannot, and that is a target-design question rather than a caption question.

PHASE A -- FULL REACH. _label_clauses() renders 12 continuous/ordinal channels into the caption on 3,310 of 9,428 roster windows (35.1%).

PHASE B -- INHERITS FOR FREE, which was not obvious and is worth recording. run_phase_b_orthogonalization builds SensorTextPairDataset (2 references) and reads caption_style (8 references); it never constructs InstructionDataset and never calls _label_clauses itself. So Phase B consumes the SAME caption Phase A does, and any clause work -- v36labels' label clauses, v39scaled's shared-axis fix -- reaches B without a line of B-specific code. It also means the provenance defect reaches B: whatever the caption leaks, B's contrastive objective is trained on.

PHASE C -- STRUCTURALLY LABEL-BLIND EXCEPT FOR ONE SLOT. Read _build_json_slots_target (datasets.py:2525) slot by slot:
  heart_rate_bucket, hr_pct, hr_raw, hr_peak   <- channel 0 of the TENSOR
  hrv_bucket, hrv_pct, hrv_raw                 <- channel 1
  deep_pct, rem_pct, awake_pct                 <- channels 4, 5, 2
  rr                                           <- channel 10
  temp                                         <- channel 11
  state                                        <- state_series(metadata) + infer_state_label(metadata)
Twelve of thirteen keys are derived from the SENSOR TENSOR. Exactly one, `state`, comes from the label events, and it enters through the state reducer rather than through label_summary(). The target never calls label_summary() and never reads subject_traits.

CONSEQUENCE FOR THE GOAL: 34 of the 35 declared label channels cannot reach Phase C in any form, no matter what Phase A's caption says. 'Utilise all labels in all three phases' is therefore not achievable by extending _label_clauses() -- it needs the Phase C TARGET to carry more label-derived keys, which is a schema change to json_slots with its own costs (the 13-key schema is what the prompt documents, and the prompt is already 317 tokens against a 256 train budget).

WHAT IS NOT WRONG HERE: the one label slot that does exist is correctly disciplined. `state` is OMITTED when state_series(metadata) is empty, with the reason recorded inline -- writing it unconditionally taught 45.9% of lora_proxy_12ch_v11full's rows to emit a verdict from an unscored window, and value_loss_weight up-weights the @@...@@ span so the fabricated token carried MORE gradient than measured ones. Any new label slot must inherit that discipline: omit when unobserved, never default.
- **2026-09-01T10:24:08+00:00** — PHASE C'S ONE LABEL SLOT IS 72.7% NEUTRAL, AND HALF THE ROWS CARRY NO STATE OBSERVATION AT ALL (scratch/_state_slot_content.py). Follows directly from the finding that _build_json_slots_target emits 13 slots with exactly one -- `state` -- derived from a label.

BAND04 (7,592 windows, the roster every shipped arm runs):
  state_series() non-empty on 3,735 windows = 49.2%
  infer_state_label over ALL windows: neutral 5,517 (72.7%) | stress 1,079 (14.2%) | flow 996 (13.1%)
  -> a constant 'neutral' emitter scores 72.7% on the campaign's only label slot

WIDE ROSTER (9,530 windows): 46.2% carry a state; neutral 76.2% / stress 13.1% / flow 10.7%. Widening makes the prior WORSE, not better -- the affect corpora it admits contribute almost entirely neutral.

THE PER-DATASET BREAKDOWN IS THE REAL FINDING. On the wide roster, TWELVE of twenty datasets emit ONLY neutral, across 2,304 windows:
  big_ideas_glycemic 145, brain_wearable_monitoring 21, case 30, dapper 862, dreamt 100, k_emocon 31, mmash 42, optihealth_supabase 302, popane_s3 142, popane_s4 83, popane_s5 199, popane_s6 140, popane_s7 213, wesad 15
Only four datasets carry a non-degenerate state distribution: lifesnaps (3,109/990/720), pmdata (1,819/359/6), nurse_stress (8/19/75) and consumer_grade_wearables (1/0/34).

NOTE WHAT THIS MEANS FOR THE AFFECT CORPORA SPECIFICALLY. CASE, DAPPER, K-EmoCon and all five POPANE studies were admitted to the wide roster precisely because they carry valence and arousal -- and every one of them reduces to `neutral` at the window level. Their film-clip inductions are 40-120 SECOND events inside a 1,440-minute window; infer_state_label reduces the window by majority over annotated epochs, so a 2-minute anger clip in a 41-minute session that is itself one window loses to the surrounding baseline. The state slot cannot see an induction shorter than the window's own majority.

SO THE SINGLE LABEL CHANNEL PHASE C CARRIES IS, ON ITS OWN ROSTER, A 72.7% PRIOR CONCENTRATED IN TWO DATASETS. That is not a reason to widen the roster (it gets worse) and not a reason to add label slots (measured separately: 26 of 35 channels reach under 3% of the wide roster). It is a statement about what a DAY-LEVEL window can express: the corpus's affect labels are minute-scale events and the target is a daily summary.

DOES NOT TOUCH the hr_raw result, which is what the campaign is actually scored on and which is a genuine per-window continuous read. This is specifically about the state slot's information content.

## Links
Cites [[yang-2022-privileged-features-distillation-2209-08754]], [[sensorfm-2026-2605-22759]], [[feli-2025-cha-ppghr-agent-2502-12836]], [[phllm-2024-personal-health-llm-2406-06474]].

#target-style #optihealth #medgemma #deployment-distribution #readout #optihealth-eiv #conditioning-collapse #subject-heldout #matched-baseline
