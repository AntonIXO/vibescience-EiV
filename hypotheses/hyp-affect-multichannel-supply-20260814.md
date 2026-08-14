---
created_at: '2026-08-14T07:47:39+00:00'
gates:
- blocking: true
  description: 'BLOCKING. lifesnaps TENSE/ANXIOUS (y>0 binarized): multichannel RF
    AUC minus rich-HR-only RF AUC >= +0.03, subject-grouped OOF, AND within-subject
    permutation p < 0.05 on the delta (>=20 perms).'
  id: g1-existence-lifesnaps
- blocking: true
  description: BLOCKING. Absolute multichannel AUC >= 0.58 on lifesnaps tense (chance
    0.50). A significant delta on top of chance-level absolute skill is existence-without-magnitude
    and does NOT justify encoder work.
  id: g2-magnitude-absolute
- blocking: false
  description: 'Non-blocking, informational. pmdata stress: (HR+sleep+time) Spearman
    minus HR-only Spearman, with the same within-subject permutation null. Only sleep
    can be added on this cohort.'
  id: g3-pmdata-sleep
- blocking: false
  description: Non-blocking. Per-family addition (HR+sleep, HR+temp, HR+rr, HR+hrv,
    HR+time on lifesnaps) reported so the winning family is named. If ONE family carries
    the full delta, the next lever is captioning/reading THAT channel, not generic
    fusion.
  id: g4-attribution
id: hyp-affect-multichannel-supply-20260814
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_affect_multichannel_increment
  direction: up
  magnitude_note: 'Anton''s hypothesis says up. My committed expectation: small positive
    on lifesnaps, primary delta AUC in +0.02..+0.06 driven mostly by the sleep family
    (sleep->next-day affect is the best-supported physiological link; temp is dense
    but mostly a nightly trait/season proxy; rr is a nightly point value). pmdata
    sleep delta near zero (stress label is a saturated 3-heavy ordinal with 16 subjects).
    Genuine uncertainty whether g1 clears the +0.03 bar; the bar decides usefulness,
    not direction.'
problem_id: affect-state-multichannel-supply-20260814
problem_tags:
- subject-heldout
- representation-learning
status: refuted
supersedes: null
topic_tags:
- cross-modal
- wearables
- optihealth
- baseline
---

## Statement
H-E5 (supply-side): the non-HR canonical channels (sleep probabilities, skin_temperature, respiratory_rate, hrv) carry affect-label information BEYOND a rich HR-only daily feature set on subject-grouped held-out folds — i.e. the data supports Anton's 'sleep/rr will improve state detection' at the supply level, making encoder/objective repair (not target-law reshaping) the justified next lever.

## Rationale
Multimodal-AI lecture 04: shallow model first on low-dimensional daily features — RF routinely beats deep stacks on tabular/sensor summaries, and the encoder can only be blamed AFTER the data is shown to carry the signal. Lecture 08: cross-modal transfer and any fusion architecture move only information actually present in the channels — measuring supply first prevents building fusion for a signal that does not exist. Lecture 06/07: if supply exists, the interaction type (redundant with HR vs unique to sleep/temp) determines the method family; the per-family attribution arm measures exactly that. The state rule-out killed HR-level levers via corr(label, emitted hr) <= 0.09 but left 'richer channels' explicitly open and unmeasured. The dissociation criterion (2026-08-13) does not apply here: it gates target-law levers on dense corr of an EMITTED slot; state has no deployment truth and the emitted slot is already ruled out, so the supply question must be answered from raw data.

## Plan
E5 CPU probe scratch/e5_affect_supply_probe.py, data/canonical (train corpus, largest labeled n). Per-day per-channel present-only summary features (masked by M_mask). Arms per cohort: trivial (majority/median), HR-only rich, HR+each-family, HR+all-nonhr, all. RandomForest(400 trees, min_leaf 5) + logistic/ridge as linear check, GroupKFold(5) by subject_id. lifesnaps tense binarized y>0 -> OOF AUC; pmdata stress ordinal -> OOF Spearman. Null: >=20 within-subject label permutations, same permuted labels for both arms of the delta. Output logs/e5_affect_supply_probe.json with n, per-arm skill, deltas, perm z/p, RF feature importances (top 15). No GPU. Verdict routes: PASS both blocking gates -> next experiment is encoder-side (re-measure eiv_nonhr_channel_grouped_r2 on v9band04 Phase B, then Phase A caption enrichment with the winning family). FAIL g1 -> rule_out: non-HR channels carry no affect signal beyond HR at daily resolution on the only labeled cohorts; multichannel-fusion-for-state program dead at supply level; do not spend GPU arms on it. PASS g1 FAIL g2 -> existence-without-magnitude: file as measured bound, park the program, name the bound.

## Links
Tests problem [[affect-state-multichannel-supply-20260814]].
Predicts [[eiv_affect_multichannel_increment]] (up).
Gate `g1-existence-lifesnaps` (blocking): BLOCKING. lifesnaps TENSE/ANXIOUS (y>0 binarized): multichannel RF AUC minus rich-HR-only RF AUC >= +0.03, subject-grouped OOF, AND within-subject permutation p < 0.05 on the delta (>=20 perms).
Gate `g2-magnitude-absolute` (blocking): BLOCKING. Absolute multichannel AUC >= 0.58 on lifesnaps tense (chance 0.50). A significant delta on top of chance-level absolute skill is existence-without-magnitude and does NOT justify encoder work.
Gate `g3-pmdata-sleep` (advisory): Non-blocking, informational. pmdata stress: (HR+sleep+time) Spearman minus HR-only Spearman, with the same within-subject permutation null. Only sleep can be added on this cohort.
Gate `g4-attribution` (advisory): Non-blocking. Per-family addition (HR+sleep, HR+temp, HR+rr, HR+hrv, HR+time on lifesnaps) reported so the winning family is named. If ONE family carries the full delta, the next lever is captioning/reading THAT channel, not generic fusion.

#cross-modal #wearables #optihealth #baseline #subject-heldout #representation-learning
