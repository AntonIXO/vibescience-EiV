---
ambition_class: mechanism
created_at: '2026-09-01T19:59:44+00:00'
former_ids: []
gap_closure_estimate: 0.15
gates:
- blocking: true
  description: 'BLOCKING. by_subject fit, conditioned on SUBJECT, in at least one
    lab corpus (case, wesad, popane_s4, popane_s5, popane_s6): over-null CMI clears
    z >= 3 on BOTH estimator families. Both is the requirement, not either -- exp-055''s
    do_not_retry records eda_scr_rate flipping SIGN between readout families on identical
    rows, so a single-family pass is not evidence. mde is given in nats for the ledger;
    the operative form of the gate is the z, because sigma is re-measured per cell
    from that cell''s own 200 permutations rather than borrowed (per-family sd varies
    58x across recipes in this project).'
  diagnostic_id: eiv_eda_state_cmi_over_null
  id: g1-conditional-signal-clears-null
  mde: 0.005
- blocking: true
  description: 'BLOCKING ON THE WRITE-UP, not on the science: I(EDA ; dataset) must
    be reported in the same table as any conditional figure. A conditional MI quoted
    without its marginal confound is the exact shape of claim this project has retracted
    before. If the marginal is large and the conditional is null, the verdict is ''corpus
    indicator'' and Yang 2022 predicts a distillation arm would HURT -- which is a
    decision, not a near-miss.'
  diagnostic_id: eiv_eda_state_cmi_over_null
  id: g2-confound-must-be-reported-beside-it
  mde: null
- blocking: true
  description: BLOCKING PRE-GATE. At least 40 joined (window, epoch) cells with BOTH
    an EDA observation and a state label, carrying >= 2 distinct state classes, in
    at least one lab corpus. CLAUDE.md records only 103 windows historically holding
    EDA together with an affect label; if the joined supply is thin the honest verdict
    is 'inconclusive for want of overlap', NOT a null, and no rule-out may be written
    from it.
  diagnostic_id: eiv_eda_state_cmi_over_null
  id: g3-supply-must-be-real
  mde: null
id: eda-carries-state-information-beyond-corpus-identity
interventions: []
override_reason: ''
paper_refs:
- yang-2022-privileged-features-distillation-2209-08754
- lopez-paz-2015-generalized-distillation-1511-03643
predicted_effects:
- diagnostic_id: eiv_eda_state_cmi_over_null
  direction: up
  magnitude_note: 'PRIMARY. by_subject standardisation, conditioned on SUBJECT, within
    a lab corpus: over-null CMI strictly positive and clearing z >= 3 on BOTH the
    binned plug-in and the k-NN estimator in at least one of case/wesad/popane_s4/s5/s6.
    Small in absolute nats (I expect well under 0.05) -- the claim is that it is non-zero
    after the corpus and the subject are conditioned away, not that it is large.'
problem_id: privileged-eda-target-validity-is-unmeasured
problem_tags:
- optihealth-eiv
reopen_reason: ''
reopened_from: null
status: confirmed
supersedes: null
topic_tags: []
---

## Statement
TRACK 3 PRE-CHECK. Privileged EDA carries momentary-state information that SURVIVES conditioning on the corpus (and on the subject) -- i.e. it is a physiological signal and not a dataset fingerprint -- and therefore is a legitimate target for generalised distillation (Lopez-Paz 2015, arXiv:1511.03643).

COMMITTED PREDICTION, stated before any number is computed. Under the by_subject standardisation (the narrowest fit `privileged_patch_targets` prefers) and conditioning on SUBJECT, at least one of {eda_tonic, eda_scr_rate} clears its within-subject permutation null at z >= 3 on BOTH estimator families in at least one lab corpus (case, wesad, popane_s4/s5/s6). Direction: UP.

I ALSO COMMIT TO THE SHAPE I EXPECT, so a pass cannot be read more strongly than it deserves: I expect the CONFOUND I(EDA ; dataset) to be LARGE relative to the conditional signal. Textbook psychophysiology says skin conductance tracks arousal, so I expect the conditional term to be positive; the 111x-280x between-subject gain spread inside single datasets says most of the pooled association is nuisance. A positive result therefore licenses a SMALL-WEIGHT head on the narrowest fit, never the pooled one.

WHAT WOULD MAKE ME WRONG, and it is a live outcome rather than a formality: if the conditional MI sits at its null while I(EDA;dataset) is large, EDA is a corpus indicator, Yang 2022's non-monotone law predicts distillation will actively HURT, and the GPU justification is withdrawn.

WHY THIS IS NOT exp-055 RE-RUN AS A SUPPLY EXPERIMENT (whose do_not_retry forbids exactly that). exp-055 asked whether the ENCODER's frozen tokens decode EDA and answered null at two supplies. This trains nothing and touches no encoder: it asks whether the TARGET carries state at the data level. Different object, different failure mode, and its answer does not depend on any checkpoint.

SCOPE LIMIT, BINDING ON THE WRITE-UP. This is a data-level measurement. Per the standing project rule that cost five retracted rule_outs on 2026-08-08, it cannot close a claim about the trained pipeline. A null here rules out "EDA as registered carries state information beyond corpus identity at the annotated epochs" and withdraws GPU justification. It does NOT refute generalised distillation, which stays UNTESTED.

INSTRUMENT CHOICES MADE IN ADVANCE. (a) Every decision statistic is an over-null z, never a raw MI, because plug-in MI is biased upward with a bias that depends on n and bin count; the within-stratum permutation holds n, bin edges and both marginals and destroys only the pairing, absorbing that bias. (b) Two estimator families (binned plug-in; Ross 2014 k-NN) are required to AGREE, because exp-055 recorded a sign flip on eda_scr_rate between readout families on identical rows. (c) All three standardisations are computed, because if the signal appears only under by_subject then the pooled and by_dataset arms were measuring gain spread.

## Rationale
The LUPI supply is 1.16M observations, 14 corpora, and zero arms have consumed it. Before any GPU, the cheapest possible question is whether the target is worth distilling. Yang et al. 2022 (arXiv:2209.08754) make this a pre-check rather than a nicety: privileged-feature distillation is non-monotone, so a too-predictive-of-the-wrong-thing teacher silently produces a worse student. CLAUDE.md independently documents that eda_tonic is bimodal by corpus (CASE 19.59 uS vs POPANE s4 2.06; pooled IQR 13.89 > pooled median 5.35) and that between-subject spread inside single datasets reaches 111x-280x, which is exactly the structure a corpus/subject indicator would have.

## Plan
CPU only, no GPU, no training, ~10 min. `scratch/t3_eda_target_validity.py`:
1. Join `privileged_events.parquet` (channel 0 eda_tonic, 1 eda_scr_rate -- positional per configs/canonical/privileged.yaml, verified against the registry rather than assumed, because a prior script had these labels TRANSPOSED) against the state label at the SAME (sample_idx, epoch), read through `state_series` from src.training.datasets so the scatter is never re-implemented.
2. Standardise three ways from `privileged_stats.json` (pooled / by_dataset / by_subject, with a pooled fallback where a subject has no entry because it fell under MIN_SUBJECT_FIT_ROWS).
3. Per (channel x standardisation x estimator): report I(EDA;dataset) as the confound, I(EDA;state|dataset) and I(EDA;state|subject) as the signal, each against its own within-stratum permutation null (200 permutations; 60 for the confound).
4. Per-lab-corpus gate table on the by_subject fit, plus a within-subject Spearman of EDA against the arousal-signed rank flow<neutral<stress -- the version immune to the between-subject gain spread.
5. Write logs/t3_eda_target_validity.json.

## Links
Tests problem [[privileged-eda-target-validity-is-unmeasured]].
Predicts [[eiv_eda_state_cmi_over_null]] (up).
Gate `g1-conditional-signal-clears-null` (blocking): BLOCKING. by_subject fit, conditioned on SUBJECT, in at least one lab corpus (case, wesad, popane_s4, popane_s5, popane_s6): over-null CMI clears z >= 3 on BOTH estimator families. Both is the requirement, not either -- exp-055's do_not_retry records eda_scr_rate flipping SIGN between readout families on identical rows, so a single-family pass is not evidence. mde is given in nats for the ledger; the operative form of the gate is the z, because sigma is re-measured per cell from that cell's own 200 permutations rather than borrowed (per-family sd varies 58x across recipes in this project).
Gate `g2-confound-must-be-reported-beside-it` (blocking): BLOCKING ON THE WRITE-UP, not on the science: I(EDA ; dataset) must be reported in the same table as any conditional figure. A conditional MI quoted without its marginal confound is the exact shape of claim this project has retracted before. If the marginal is large and the conditional is null, the verdict is 'corpus indicator' and Yang 2022 predicts a distillation arm would HURT -- which is a decision, not a near-miss.
Gate `g3-supply-must-be-real` (blocking): BLOCKING PRE-GATE. At least 40 joined (window, epoch) cells with BOTH an EDA observation and a state label, carrying >= 2 distinct state classes, in at least one lab corpus. CLAUDE.md records only 103 windows historically holding EDA together with an affect label; if the joined supply is thin the honest verdict is 'inconclusive for want of overlap', NOT a null, and no rule-out may be written from it.
Cites [[yang-2022-privileged-features-distillation-2209-08754]], [[lopez-paz-2015-generalized-distillation-1511-03643]].

#optihealth-eiv
