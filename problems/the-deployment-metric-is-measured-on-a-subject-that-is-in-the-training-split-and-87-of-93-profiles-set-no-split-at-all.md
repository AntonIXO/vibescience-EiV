---
created_at: '2026-09-01T11:01:10+00:00'
former_ids: []
id: the-deployment-metric-is-measured-on-a-subject-that-is-in-the-training-split-and-87-of-93-profiles-set-no-split-at-all
paper_refs:
- tan-2024-llm-useful-timeseries-2406-16964
- qiu-2026-rethinking-llm-tsf-2602-14744
- prajod-2022-ecg-stress-generalizability-2210-06225
problem_tags:
- optihealth-eiv
- subject-heldout
- matched-baseline
status: resolved
target: null
title: The deployment metric is measured on a subject that is in the TRAINING split,
  and 87 of 93 profiles set no split at all
topic_tags:
- deployment-distribution
- optihealth
- readout
updated_at: '2026-09-03T12:57:08+00:00'
updates:
- at: '2026-09-01T11:02:51+00:00'
  text: "MEMORISATION GAP MEASURED WITHOUT A RE-RUN, AND IT SPLITS THE TWO ARMS (scratch/_memorisation_gap.py).
    The dense draw is not dataset-restricted, so it carries a mix of subjects, and
    split_subjects.json labels each one. Within ONE capture, on identical rows, per-row
    error on subjects the model trained on can be compared against subjects it never
    saw -- same model, same draw, only seen-ness differs.\n\nPER-CAPTURE, dense draw
    (96 train rows, 23 held-out = 9 val + 14 test; 9 rows have no split entry):\n\n
    \ capture              train   held-out      gap\n  v28affect01 s42      2.730
    \     2.593    +0.137\n  v28affect01 s43      2.758      1.906    +0.852\n  v36labels
    \  s42      3.672      4.960    -1.289\n  v36labels   s43      2.317      4.937
    \   -2.620\n  v36labels   s44      2.189      2.395    -0.205\n  mean gap -0.625
    bpm (sd 1.356 over 5 captures)\n\nTHE SIGNS SEPARATE BY ARM, WHICH IS THE FINDING.
    Both incumbent captures are POSITIVE -- slightly WORSE on subjects it trained
    on, which is what a model that has not memorised looks like. All three v36labels
    captures are NEGATIVE, twice by more than a bpm, s43 at -2.620: held-out 4.937
    against train 2.317.\n\nSO THE LABEL CLAUSES MAY BE BUYING ACCURACY WITH SUBJECT
    MEMORISATION. The campaign already has evidence pointing the same way from another
    direction: the clause-leakage audit measured stress_level at bal-acc 0.879 against
    0.500 chance for predicting WHICH CORPUS a window came from, valence at +0.294
    lift. A caption that identifies the source helps identify the subject.\n\nWHAT
    THIS DOES NOT ESTABLISH: 23 held-out rows is thin, gaps span -2.620 to +0.852,
    sd 1.356 over five points, and v36labels s44 reads only -0.205 -- so the effect
    is not consistent even across its own seeds. A signal worth acting on, not a settled
    number.\n\nWHAT IT DOES ESTABLISH: the promotion comparison is NOT symmetric under
    the leak. I recorded earlier that 'every arm shares the defect equally, so relative
    comparisons stand'. That was wrong -- an arm whose caption carries provenance
    can exploit the leak MORE than one whose caption does not, and the measurement
    says v36labels does.\n\nCONSEQUENCE FOR THE PENDING VERDICT: on held-out rows
    only, the dense ranking REVERSES. v28affect01 reads 2.593 / 1.906 against v36labels'
    4.960 / 4.937 / 2.395. The incumbent wins on the rows that measure generalisation,
    by more than the in-sample comparison suggested. Cell 4 adds one more incumbent
    point.\n\nNOT PROMOTING OR DEMOTING ON 23 ROWS. The right instrument is a split-enabled
    arm -- a recipe change, and it belongs after the family closes."
- at: '2026-09-01T11:04:17+00:00'
  text: "THE FIX IS TWO CHANGES, NOT ONE -- AND THE SCORING SIDE DOES NOT EXIST YET
    (scratch/_split_feasibility.py). Checked feasibility before queueing anything,
    because a half-applied fix here would look like a failed experiment.\n\nSIDE 1,
    TRAINING: the profile key exists and works. What each roster would lose at split='train':\n
    \ band04         (6 datasets, 7592 windows)   train 5910 (77.8%) | val 824 | test
    858\n  wide roster    (20 datasets, 9530)          train 6723 (70.5%) | val 933
    | test 981 | UNLISTED 893\n  phase_a v36    (19 datasets, 9428)          train
    6652 (70.6%) | val 922 | test 961 | UNLISTED 893\nSo enabling it costs ~22-30%
    of training windows. That is a real cost but not prohibitive.\n\nSIDE 2, SCORING:
    does not exist. emission_diversity_audit.py contains the word 'split' exactly
    once (in prose) and its CLI flags are adapter, tag, n, out, dataset, impute, batch-size,
    presence-blind, stop-on-brace. It draws from the whole eval pool with no notion
    of splits.\n\nSO A NAIVE 'TURN THE SPLIT ON' MAKES THINGS WORSE: the arm trains
    on 22-30% less data and is still scored on rows that include subjects it saw.
    Strictly worse on both axes, and it would read as 'the split hurts accuracy' when
    it actually tested nothing.\n\nBUT THE SCORING SIDE IS BUILDABLE, AND CHEAPLY
    -- this is the part worth recording. Checked whether the eval bundle's subject
    keys match the training bundle's split file, since they are separate artefacts
    and nothing guarantees it:\n  eval bundle by split: train 5027 | val 747 | test
    738 | UNLISTED 406\n  key format matches exactly (big_ideas_glycemic::big_ideas_glycemic::001
    -> train)\n  HELD-OUT ROWS AVAILABLE IN THE EVAL BUNDLE: 1,485 of 6,918 (21.5%)\n\nSo
    a --split flag on the scorer is a small change against data that is already correctly
    keyed, and it would give a 1,485-row held-out pool -- 65x the 23 rows the memorisation
    gap was measured on.\n\nSUPABASE REMAINS THE HARD CASE AND IS NOT SOLVED BY ANY
    OF THIS: 302 windows, ONE subject, currently in train. Held out, the model gets
    zero examples of the deployment distribution; kept in, the deployment metric stays
    in-sample. That is a corpus design decision for Anton, not something to settle
    from here -- and the honest interim position is to report supabase numbers as
    IN-SAMPLE and use the 1,485-row held-out pool for generalisation claims.\n\nORDER
    OF WORK IMPLIED: scorer --split flag first (cheap, no GPU, unlocks a 1,485-row
    honest readout of every EXISTING capture), then a split-enabled training arm,
    then the supabase question. Nothing queued -- the family is mid-flight and this
    is a recipe change."
- at: '2026-09-01T11:06:12+00:00'
  text: "HELD-OUT READOUT OF EVERY EXISTING CAPTURE, NO GPU (scratch/_heldout_readout.py).
    The scorer has no --split flag but does not need one for this: captures store
    per-row emissions with an idx, and the eval bundle's subject keys match split_subjects.json
    exactly, so the held-out subset of any post-rebuild capture can be scored after
    the fact.\n\nRANKED BY HELD-OUT MAE, dense draw (23 held-out rows, trivial baseline
    5.961 on those rows):\n\n  v28affect01 s43      1.906   skill  +4.055   scale
    0.93\n  v33dispersion        2.239   skill  +3.722   scale 1.10\n  v36labels s44
    \       2.395   skill  +3.566   scale 1.09\n  v28affect01 s42      2.593   skill
    \ +3.368   scale 1.27\n  v35wide              4.135   skill  +1.825   scale 1.15\n
    \ v34nopeak            4.495   skill  +1.466   scale 1.03\n  v36labels s43        4.937
    \  skill  +1.024   scale 2.08  <- outside band\n  v36labels s42        4.960   skill
    \ +1.000   scale 1.59  <- outside band\n  v25r30               8.686   skill  -2.725
    \  scale 2.22  <- outside band\n  v27affect            9.814   skill  -3.853   scale
    2.34  <- outside band\n  v31detached         10.080   skill  -4.119   scale 2.00
    \ <- outside band\n  v32detachaffect     11.309   skill  -5.348   scale 2.61  <-
    outside band\n  v30state            19.935   skill -13.975   scale 2.77  <- outside
    band\n\nTHREE THINGS THIS CHANGES:\n\n1. THE INCUMBENT TAKES FIRST AND FOURTH,
    both in-band on calibration. On the in-sample reading v36labels looked competitive;
    on held-out rows only its s44 seed keeps up, while its other two sit at 4.94-4.96
    with scale 2.08 and 1.59 -- they emit at roughly twice the true spread on unseen
    subjects.\n\n2. THE CALIBRATION COLUMN SPLITS THE TABLE CLEANLY. Every arm above
    4.9 held-out MAE is also outside [0.85,1.35], most near 2.0-2.8. On seen subjects
    these arms emit sanely; on unseen ones they over-express range. One coherent failure
    mode, invisible in whole-draw numbers where seen rows dominate 96 to 23.\n\n3.
    SEVEN ARMS ARE WORSE THAN A CONSTANT ON UNSEEN SUBJECTS. v30state reads -13.975.
    And v31detached -- the campaign's best geometry at eff_rank 10.106 -- reads 10.080
    bpm against a 5.961 baseline. Geometry and generalisation are not the same axis;
    this is the sharpest evidence of that on record.\n\nCAVEAT WITH THE NUMBER: 23
    rows. The honest figure available without a re-run, not a substitute for the scorer
    flag. The 1,485 held-out rows in the eval bundle need re-scoring, which costs
    GPU.\n\nFOR THE PENDING VERDICT: read the family's dense comparison on held-out
    rows too. Full draw gives v36labels 3.072 against the incumbent's 2.816; held-out
    gives 4.097 against 2.250. Same direction, larger margin."
- at: '2026-09-01T20:05:51+00:00'
  text: '2026-09-01 — SCOPE WIDENED FROM ONE SUBJECT TO THE WHOLE POOL, and one sentence
    of my own rule-out CORRECTED.


    (1) WIDENED. Rule-out `that-the-scorer-s-split-heldout-flag-makes-the-d88faa`
    measured the defect on the rows that actually produced the incumbent''s headline:
    125 of 128 scored rows belong to subjects present in the Phase A/B roster AND
    the Phase C roster, at all three seeds. 241 of the 277 declared held-out subjects
    (87.0%) are inside the Phase A/B roster. So this problem is not about `optihealth_supabase`;
    supabase is where it was noticed. The pooled 2.969 "held-out" figure is an in-sample
    number at the subject level, and every gate written against it (v41''s g1 included)
    is anchored to the wrong bar.


    (2) CORRECTED, and this is the part that changes what to do next. My rule-out''s
    closing sentence read: "Relative comparisons between arms that share this defect
    remain valid, because the leak is identical across them." That is the natural
    conclusion and the `eval-leakage-audit` skill names it as WRONG: a leak is NOT
    symmetric across arms, because an arm whose inputs carry provenance — or which
    simply has more memorisation capacity — can exploit it MORE than one that does
    not. The gap must be measured PER ARM before any ranking is assumed to survive.


    This bites hardest on exactly the comparison queued next. Track 1 (the Tan ablation)
    sets a frozen-tower + 4B-parameter LoRA LLM against a frozen-tower + small attention
    head on the same rows. Those two arms have wildly different capacity to memorise
    27 subjects, so a leaked comparison between them is not merely imprecise, it is
    biased in a known direction — toward the LLM. Track 1 therefore cannot be run
    against the existing leaked capture as its reference and must train BOTH consumers
    under `split: train` and score on val/test rows only.


    CONSEQUENCE FOR THE ROADMAP: Track 0 is no longer instrumentation that can run
    beside the optimisation tracks. It is a precondition for Tracks 1 and 2, because
    both of their gates are currently written against 2.969.'
- at: '2026-09-03T06:47:04+00:00'
  text: '2026-09-03 — v39scaled CLOSED AS IN-SAMPLE, no further GPU. Its capture (logs/emission_v39scaled_heldout_dense_summary.json)
    reads hr_raw 2.569 at scale 1.218, but its manifest records split: None, so the
    128 scored rows include training subjects and the number is not comparable to
    v41rebased''s split-honest 2.843; it has no vault experiment and gets none. Reason
    not to re-run it split-honest (~2 GPU-h): its lever is a Phase C label-scaling/target
    change, and exp-a-hr-tower-localisation-20260903 showed 75% of the LLM''s hr_raw
    loss is upstream of emission, with the emission-side branch (D-a) withdrawn as
    unpowered against the measured 0.208 seed floor. A Phase C target lever therefore
    has at most ~0.5 bpm to win on this diagnostic. Rule of record stays: never compare
    a split: None capture to a split-honest one.'
- at: '2026-09-03T12:57:08+00:00'
  text: '2026-09-03 — RESOLVED as a measurement problem. The scorer has --split heldout
    on the 1,485-row pool, v41rebased is the first chain with split: train on all
    three stages (confirmed, 2.843 vs trivial 6.876), every gate since has been written
    against split-honest numbers, and the leak''s asymmetry was measured (identical
    recipe 2.969 leaked / 4.429 honest). What remains is a CORPUS DESIGN decision,
    not a defect: optihealth_supabase is one subject and cannot be both trained on
    and held out; the standing recommendation (NEXT.md §6.4) is day-forward inside
    the subject AND subject-held-out, reported together, re-merged on Anton''s machine.
    v39scaled closed as in-sample today for the same reason this problem exists.'
---

## Description
MEASURED 2026-09-01 (scratch/_train_eval_corpus_relation.py, plus direct reads of split_subjects.json, cli_train.py:819 and datasets.py:4155-4180).

THE CHAIN, each link verified:

1. optihealth_supabase is ONE SUBJECT. data/canonical/split_subjects.json lists exactly one supabase key -- optihealth_supabase::optihealth_supabase::9e73d06e-1006-4266-bde7-85659f5de69d -- and it is in the TRAIN split. val and test contain no supabase subject at all.

2. THE EVAL BUNDLE'S SUPABASE ROWS ARE THAT SAME SUBJECT. data/eval_v3_1min's 230 supabase windows all carry subject_id optihealth_supabase::9e73d06e-1006-4266-bde7-85659f5de69d.

3. THE EVAL WINDOWS ARE PHYSICALLY PRESENT IN THE TRAINING TENSOR. Probed 12 eval supabase windows against the 302 supabase windows of data/canonical by matching HR trace length, mean and sd to 1e-9: 12 of 12 found. The two bundles share subjects 519/519 (every eval subject is a training subject; 1101 training subjects are not in eval).

4. AND THE SPLIT FILTER IS OFF BY DEFAULT. datasets.py applies the subject filter only when split is not None (line 4158). cli_train.py:819 reads it from the profile with no default, so an absent key means None means NO FILTERING. Measured across configs/training.yaml: only 6 of 93 profiles set `split`. The incumbent's lora_proxy_12ch_wl_band04, its _s43/_s44 seed variants, phase_a_12ch_v36labels and phase_b_12ch_v25bigbatch ALL leave it unset.

SO: every arm in the current campaign trains on the same subject-days it is then scored on, and the deployment number -- the one Anton's field annotations are supposed to validate, the one this whole problem is named after -- is an in-sample measurement on a single subject.

WHAT THIS DOES NOT MEAN. It does not mean the arms are worthless or that the ranking is inverted: every arm shares the defect equally, so RELATIVE comparisons between arms on the same draw remain meaningful. The seed family's verdict is unaffected in that sense. [CORRECTED 2026-09-01: the leak is NOT symmetric across arms -- see the updates.]

WHAT IT DOES MEAN. The ABSOLUTE supabase MAEs (1.862 through 10.058) are not generalisation estimates. 'hr_raw MAE 2.1 bpm on the deployment population' is really 'MAE 2.1 bpm on 200 days of one subject the model was trained on'. Any claim about deployment readiness, and any comparison against the trivial baseline as evidence of skill, is measuring memorisation plus skill with no way to separate them.

WHY IT SURVIVED THIS LONG: the split file exists, is correct, is subject-namespaced, and carries a rationale ('hr_mean between-subject ICC is 0.755; a row split leaks identity'). Someone built the right instrument. The profiles simply never switched it on, and nothing warns when it is absent -- an unset key is indistinguishable from a deliberate choice.

FIRST THING TO SETTLE, and it is cheap: whether supabase can be held out at all. It is one subject, so putting it in test empties it from train entirely -- which may be the correct design (the deployment cohort should be unseen) or may leave the model with no supabase-like data at all. That is a decision about the corpus, not a bug fix.

RESOLVED 2026-09-03: the measurement defect is fixed (split-honest scorer and chain); the supabase corpus design remains Anton's decision (NEXT.md §6.4).

## Updates
- **2026-09-01T11:02:51+00:00** — MEMORISATION GAP MEASURED WITHOUT A RE-RUN, AND IT SPLITS THE TWO ARMS (scratch/_memorisation_gap.py). The dense draw is not dataset-restricted, so it carries a mix of subjects, and split_subjects.json labels each one. Within ONE capture, on identical rows, per-row error on subjects the model trained on can be compared against subjects it never saw -- same model, same draw, only seen-ness differs.

PER-CAPTURE, dense draw (96 train rows, 23 held-out = 9 val + 14 test; 9 rows have no split entry):

  capture              train   held-out      gap
  v28affect01 s42      2.730      2.593    +0.137
  v28affect01 s43      2.758      1.906    +0.852
  v36labels   s42      3.672      4.960    -1.289
  v36labels   s43      2.317      4.937    -2.620
  v36labels   s44      2.189      2.395    -0.205
  mean gap -0.625 bpm (sd 1.356 over 5 captures)

THE SIGNS SEPARATE BY ARM, WHICH IS THE FINDING. Both incumbent captures are POSITIVE -- slightly WORSE on subjects it trained on, which is what a model that has not memorised looks like. All three v36labels captures are NEGATIVE, twice by more than a bpm, s43 at -2.620: held-out 4.937 against train 2.317.

SO THE LABEL CLAUSES MAY BE BUYING ACCURACY WITH SUBJECT MEMORISATION. The campaign already has evidence pointing the same way from another direction: the clause-leakage audit measured stress_level at bal-acc 0.879 against 0.500 chance for predicting WHICH CORPUS a window came from, valence at +0.294 lift. A caption that identifies the source helps identify the subject.

WHAT THIS DOES NOT ESTABLISH: 23 held-out rows is thin, gaps span -2.620 to +0.852, sd 1.356 over five points, and v36labels s44 reads only -0.205 -- so the effect is not consistent even across its own seeds. A signal worth acting on, not a settled number.

WHAT IT DOES ESTABLISH: the promotion comparison is NOT symmetric under the leak. I recorded earlier that 'every arm shares the defect equally, so relative comparisons stand'. That was wrong -- an arm whose caption carries provenance can exploit the leak MORE than one whose caption does not, and the measurement says v36labels does.

CONSEQUENCE FOR THE PENDING VERDICT: on held-out rows only, the dense ranking REVERSES. v28affect01 reads 2.593 / 1.906 against v36labels' 4.960 / 4.937 / 2.395. The incumbent wins on the rows that measure generalisation, by more than the in-sample comparison suggested. Cell 4 adds one more incumbent point.

NOT PROMOTING OR DEMOTING ON 23 ROWS. The right instrument is a split-enabled arm -- a recipe change, and it belongs after the family closes.
- **2026-09-01T11:04:17+00:00** — THE FIX IS TWO CHANGES, NOT ONE -- AND THE SCORING SIDE DOES NOT EXIST YET (scratch/_split_feasibility.py). Checked feasibility before queueing anything, because a half-applied fix here would look like a failed experiment.

SIDE 1, TRAINING: the profile key exists and works. What each roster would lose at split='train':
  band04         (6 datasets, 7592 windows)   train 5910 (77.8%) | val 824 | test 858
  wide roster    (20 datasets, 9530)          train 6723 (70.5%) | val 933 | test 981 | UNLISTED 893
  phase_a v36    (19 datasets, 9428)          train 6652 (70.6%) | val 922 | test 961 | UNLISTED 893
So enabling it costs ~22-30% of training windows. That is a real cost but not prohibitive.

SIDE 2, SCORING: does not exist. emission_diversity_audit.py contains the word 'split' exactly once (in prose) and its CLI flags are adapter, tag, n, out, dataset, impute, batch-size, presence-blind, stop-on-brace. It draws from the whole eval pool with no notion of splits.

SO A NAIVE 'TURN THE SPLIT ON' MAKES THINGS WORSE: the arm trains on 22-30% less data and is still scored on rows that include subjects it saw. Strictly worse on both axes, and it would read as 'the split hurts accuracy' when it actually tested nothing.

BUT THE SCORING SIDE IS BUILDABLE, AND CHEAPLY -- this is the part worth recording. Checked whether the eval bundle's subject keys match the training bundle's split file, since they are separate artefacts and nothing guarantees it:
  eval bundle by split: train 5027 | val 747 | test 738 | UNLISTED 406
  key format matches exactly (big_ideas_glycemic::big_ideas_glycemic::001 -> train)
  HELD-OUT ROWS AVAILABLE IN THE EVAL BUNDLE: 1,485 of 6,918 (21.5%)

So a --split flag on the scorer is a small change against data that is already correctly keyed, and it would give a 1,485-row held-out pool -- 65x the 23 rows the memorisation gap was measured on.

SUPABASE REMAINS THE HARD CASE AND IS NOT SOLVED BY ANY OF THIS: 302 windows, ONE subject, currently in train. Held out, the model gets zero examples of the deployment distribution; kept in, the deployment metric stays in-sample. That is a corpus design decision for Anton, not something to settle from here -- and the honest interim position is to report supabase numbers as IN-SAMPLE and use the 1,485-row held-out pool for generalisation claims.

ORDER OF WORK IMPLIED: scorer --split flag first (cheap, no GPU, unlocks a 1,485-row honest readout of every EXISTING capture), then a split-enabled training arm, then the supabase question. Nothing queued -- the family is mid-flight and this is a recipe change.
- **2026-09-01T11:06:12+00:00** — HELD-OUT READOUT OF EVERY EXISTING CAPTURE, NO GPU (scratch/_heldout_readout.py). The scorer has no --split flag but does not need one for this: captures store per-row emissions with an idx, and the eval bundle's subject keys match split_subjects.json exactly, so the held-out subset of any post-rebuild capture can be scored after the fact.

RANKED BY HELD-OUT MAE, dense draw (23 held-out rows, trivial baseline 5.961 on those rows):

  v28affect01 s43      1.906   skill  +4.055   scale 0.93
  v33dispersion        2.239   skill  +3.722   scale 1.10
  v36labels s44        2.395   skill  +3.566   scale 1.09
  v28affect01 s42      2.593   skill  +3.368   scale 1.27
  v35wide              4.135   skill  +1.825   scale 1.15
  v34nopeak            4.495   skill  +1.466   scale 1.03
  v36labels s43        4.937   skill  +1.024   scale 2.08  <- outside band
  v36labels s42        4.960   skill  +1.000   scale 1.59  <- outside band
  v25r30               8.686   skill  -2.725   scale 2.22  <- outside band
  v27affect            9.814   skill  -3.853   scale 2.34  <- outside band
  v31detached         10.080   skill  -4.119   scale 2.00  <- outside band
  v32detachaffect     11.309   skill  -5.348   scale 2.61  <- outside band
  v30state            19.935   skill -13.975   scale 2.77  <- outside band

THREE THINGS THIS CHANGES:

1. THE INCUMBENT TAKES FIRST AND FOURTH, both in-band on calibration. On the in-sample reading v36labels looked competitive; on held-out rows only its s44 seed keeps up, while its other two sit at 4.94-4.96 with scale 2.08 and 1.59 -- they emit at roughly twice the true spread on unseen subjects.

2. THE CALIBRATION COLUMN SPLITS THE TABLE CLEANLY. Every arm above 4.9 held-out MAE is also outside [0.85,1.35], most near 2.0-2.8. On seen subjects these arms emit sanely; on unseen ones they over-express range. One coherent failure mode, invisible in whole-draw numbers where seen rows dominate 96 to 23.

3. SEVEN ARMS ARE WORSE THAN A CONSTANT ON UNSEEN SUBJECTS. v30state reads -13.975. And v31detached -- the campaign's best geometry at eff_rank 10.106 -- reads 10.080 bpm against a 5.961 baseline. Geometry and generalisation are not the same axis; this is the sharpest evidence of that on record.

CAVEAT WITH THE NUMBER: 23 rows. The honest figure available without a re-run, not a substitute for the scorer flag. The 1,485 held-out rows in the eval bundle need re-scoring, which costs GPU.

FOR THE PENDING VERDICT: read the family's dense comparison on held-out rows too. Full draw gives v36labels 3.072 against the incumbent's 2.816; held-out gives 4.097 against 2.250. Same direction, larger margin.
- **2026-09-01T20:05:51+00:00** — 2026-09-01 — SCOPE WIDENED FROM ONE SUBJECT TO THE WHOLE POOL, and one sentence of my own rule-out CORRECTED.

(1) WIDENED. Rule-out `that-the-scorer-s-split-heldout-flag-makes-the-d88faa` measured the defect on the rows that actually produced the incumbent's headline: 125 of 128 scored rows belong to subjects present in the Phase A/B roster AND the Phase C roster, at all three seeds. 241 of the 277 declared held-out subjects (87.0%) are inside the Phase A/B roster. So this problem is not about `optihealth_supabase`; supabase is where it was noticed. The pooled 2.969 "held-out" figure is an in-sample number at the subject level, and every gate written against it (v41's g1 included) is anchored to the wrong bar.

(2) CORRECTED, and this is the part that changes what to do next. My rule-out's closing sentence read: "Relative comparisons between arms that share this defect remain valid, because the leak is identical across them." That is the natural conclusion and the `eval-leakage-audit` skill names it as WRONG: a leak is NOT symmetric across arms, because an arm whose inputs carry provenance — or which simply has more memorisation capacity — can exploit it MORE than one that does not. The gap must be measured PER ARM before any ranking is assumed to survive.

This bites hardest on exactly the comparison queued next. Track 1 (the Tan ablation) sets a frozen-tower + 4B-parameter LoRA LLM against a frozen-tower + small attention head on the same rows. Those two arms have wildly different capacity to memorise 27 subjects, so a leaked comparison between them is not merely imprecise, it is biased in a known direction — toward the LLM. Track 1 therefore cannot be run against the existing leaked capture as its reference and must train BOTH consumers under `split: train` and score on val/test rows only.

CONSEQUENCE FOR THE ROADMAP: Track 0 is no longer instrumentation that can run beside the optimisation tracks. It is a precondition for Tracks 1 and 2, because both of their gates are currently written against 2.969.
- **2026-09-03T06:47:04+00:00** — 2026-09-03 — v39scaled CLOSED AS IN-SAMPLE, no further GPU. Its capture (logs/emission_v39scaled_heldout_dense_summary.json) reads hr_raw 2.569 at scale 1.218, but its manifest records split: None, so the 128 scored rows include training subjects and the number is not comparable to v41rebased's split-honest 2.843; it has no vault experiment and gets none. Reason not to re-run it split-honest (~2 GPU-h): its lever is a Phase C label-scaling/target change, and exp-a-hr-tower-localisation-20260903 showed 75% of the LLM's hr_raw loss is upstream of emission, with the emission-side branch (D-a) withdrawn as unpowered against the measured 0.208 seed floor. A Phase C target lever therefore has at most ~0.5 bpm to win on this diagnostic. Rule of record stays: never compare a split: None capture to a split-honest one.
- **2026-09-03T12:57:08+00:00** — 2026-09-03 — RESOLVED as a measurement problem. The scorer has --split heldout on the 1,485-row pool, v41rebased is the first chain with split: train on all three stages (confirmed, 2.843 vs trivial 6.876), every gate since has been written against split-honest numbers, and the leak's asymmetry was measured (identical recipe 2.969 leaked / 4.429 honest). What remains is a CORPUS DESIGN decision, not a defect: optihealth_supabase is one subject and cannot be both trained on and held out; the standing recommendation (NEXT.md §6.4) is day-forward inside the subject AND subject-held-out, reported together, re-merged on Anton's machine. v39scaled closed as in-sample today for the same reason this problem exists.

## Links
Cites [[tan-2024-llm-useful-timeseries-2406-16964]], [[qiu-2026-rethinking-llm-tsf-2602-14744]], [[prajod-2022-ecg-stress-generalizability-2210-06225]].

#deployment-distribution #optihealth #readout #optihealth-eiv #subject-heldout #matched-baseline
