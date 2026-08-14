---
created_at: '2026-08-05T21:21:00+00:00'
former_ids:
- the-soft-token-prefix-influences-the-lm-as-a-low-rank-bias-on-the-residual-strea
gates:
- blocking: true
  description: PRIMARY. RidgeCV on the top 3 principal components of the frozen hidden
    state (PCA fit on TRAIN ONLY) must reach within 0.5 bpm of full-dimensional Ridge,
    i.e. test MAE <= 10.770 given E28's full-dim 10.270. PASS confirms the peak information
    is carried by <=3 directions. Additionally the paired bootstrap CI95 of (best-k
    minus k=3) must not show a material further gain; if MAE keeps improving materially
    past k=3 the low-rank claim FAILS and the prefix is writing distributed high-rank
    information.
  diagnostic_id: null
  id: gate-rank3-matches-full
  mde: null
- blocking: true
  description: The DONOR arm (identical PCA+Ridge pipeline on donor-prefix hidden
    states Hd) must show NO skill at ANY k -- MAE no better than roughly always-predict-median
    (18.492 bpm). If a donor arm gains skill as k grows, the sweep is fitting the
    marginal target distribution through extra components rather than reading this
    sample's prefix, and the whole sweep is uninterpretable. This control is mandatory
    because donor-null failures already produced false passes in E25 and E26.
  diagnostic_id: null
  id: gate-donor-no-skill-any-k
  mde: null
- blocking: true
  description: 'SHUFFLED-TARGET control at EVERY k: refit the real pipeline on a permuted
    training target. Must score no better than always-predict-median at all k. This
    is specifically required at high k, where 300 or 700 components against n_train=700
    could overfit and manufacture an apparent ''more rank helps'' signal that would
    spuriously REFUTE the low-rank hypothesis. The control protects the null branch
    as much as the positive one.'
  diagnostic_id: null
  id: gate-shuffled-null-all-k
  mde: null
- blocking: false
  description: 'Report the explained-variance ratio (PC1, PC1-3, PC1-10, PC1-50) and
    the participation ratio (effective rank) of the train hidden states, plus corr(PC
    score, hr_peak) for the top 10 PCs. This shows low rank rather than asserting
    it, and distinguishes two different situations: hidden states that are globally
    low-rank (the whole residual stream is degenerate) versus hidden states that are
    high-rank overall but whose PEAK-PREDICTIVE content is confined to a few directions.
    Only the latter supports the bias reading.'
  diagnostic_id: null
  id: gate-spectrum-reported
  mde: null
- blocking: false
  description: SCOPE HONESTY. Petrov et al. (arXiv:2310.19698) concerns a FIXED prefix;
    here the prefix varies per sample, so the theory does not transfer verbatim and
    this experiment tests a CONSEQUENCE of it, not the theorem. A low-rank result
    is consistent with the bias reading but does not prove the theorem applies. Equally,
    low rank at the emission position does not establish where the rank collapse happened
    (projector, injection, or forward pass) -- that requires a layer sweep. State
    both limits explicitly in the notes rather than claiming the theory has been confirmed.
  diagnostic_id: null
  id: gate-theory-scope-stated
  mde: null
id: the-soft-token-prefix-influences-the-lm-82bbde
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_digit_readout_decoded_mae
  direction: none
  magnitude_note: 'PRIMARY, committed BEFORE running. Predicting that RidgeCV restricted
    to the top 3 principal components of the frozen hidden state will match full-dimensional
    Ridge within 0.5 bpm -- i.e. test MAE at k=3 lands at or below 10.770 against
    E28''s full-dim 10.270 -- and that MAE will not improve materially beyond k=3.
    Direction is ''none'' because the claim is that ADDING rank BUYS NOTHING: the
    k=3 and full-dimensional readouts are predicted equivalent. Reference points on
    this identical split: full Ridge 10.270, always-predict-median 18.492, donor-prefix
    Ridge 19.380. If instead MAE falls steadily with k (say k=50 or k=100 materially
    beating k=3 with a CI excluding zero), the low-rank bias reading is REFUTED and
    the prefix writes distributed information that the readout genuinely fails to
    exploit -- which would reopen the readout-capacity question that E28 just closed.'
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
The soft-token prefix influences the LM as a LOW-RANK BIAS on the residual stream, not as a reprogramming of the LM's computation. Consequently the hr_peak-predictive information in the frozen hidden state at the emission position is confined to a very small number of directions: RidgeCV restricted to the top 3 principal components (PCA fit on TRAIN ONLY) will match full-dimensional Ridge (E28's 10.270 bpm) to within 0.5 bpm, and will not improve materially at higher rank.

MOTIVATED BY EXTERNAL THEORY, committed before running. Petrov, Torr & Bibi (ICLR 2024, arXiv:2310.19698) prove a prefix cannot change the relative attention over content tokens and can only bias the attention block's output. If a consequence of that binds here, the prefix supplies a few scalar degrees of freedom rather than an input-dependent computation -- which retrodicts every E24-E28 result simultaneously: coarse-magnitude-only decoding, non-linear readouts LOSING to Ridge, no non-leading digit position beating its donor, and bin accuracy collapsing from 0.58 at K=4 to 0.08 at K=32.

CONSEQUENCE IF CONFIRMED: the diagnosis becomes a BANDWIDTH limit at the prefix-to-residual-stream interface, not a readout defect (already ruled out) and not a target-encoding defect (already ruled out). The implied interventions are exactly those that change what the LM COMPUTES rather than what it is biased by: zero-init gated cross-attention (LLaMA-Adapter, arXiv:2303.16199) and Q-Former-style two-stage contrastive alignment of projector output into the text-embedding subspace before the frozen LLM (BLIP-2). These should be preregistered as the next arms and ranked ahead of rb-e4.

CONSEQUENCE IF REFUTED (MAE keeps falling materially past k=3): the prefix writes DISTRIBUTED, high-rank information into the residual stream, the bias reading does not bind, and the readout-capacity question that E28 closed would have to be reopened -- because a high-rank signal that no tested readout exploits would mean my readout sweep was somehow underpowered rather than information-limited.

SCOPE LIMITS STATED UP FRONT, both of which constrain how a PASS may be reported. (1) Petrov et al. concern a FIXED prefix, whereas mine varies per sample; this experiment tests a CONSEQUENCE of the theory, not the theorem, and a pass must not be written up as confirming the theorem. (2) Low rank measured at the emission position does not localise WHERE rank was lost -- projector, injection, or forward pass -- which needs a layer sweep this experiment does not run.

## Rationale
This hypothesis comes from the LITERATURE, not from extending my own prior framing, which matters because my last four experiments were self-generated and two of them had to be corrected.

Petrov, Torr & Bibi, 'When Do Prompting and Prefix-Tuning Work? A Theory of Capabilities and Limitations' (ICLR 2024, arXiv:2310.19698) prove a structural limitation: a prefix CANNOT change the relative attention pattern over content tokens; it can only add a bias to the attention block's output. A prefix nudges the residual stream, it does not reprogram the computation the LM runs over its own content. Related literature converges on the same interface framing from a different direction -- the CLIP modality/contrastive gap (Liang et al. arXiv:2203.02053; arXiv:2405.18570), where the measured cos = -0.015 between the prefix's HR direction and the LM's native numeric direction is a textbook instance.

If a consequence of that theory binds here, the prefix's influence on the hidden state at the hr_peak emission position should be effectively LOW-RANK: h(sample) ~= h_base + sum of a few alpha_i(sample) * v_i. That single structural prediction retrodicts EVERY result E24-E28 produced, which is what makes it worth testing rather than another lever:
  * only coarse quartile-level magnitude decodes -> few scalar degrees of freedom
  * non-linear readouts LOSE to Ridge (E28, CI [+0.574,+2.441]) -> no non-linear structure exists in a low-rank projection
  * zero non-leading digit positions beat their donor (E26) -> digits require computation, a bias cannot supply it
  * bin accuracy collapses 0.58 -> 0.08 as K goes 4 -> 32 (E27) -> one scalar cannot resolve 32 levels
No prior experiment measured the RANK of the peak-predictive signal; every one of E24-E28 varied the readout while holding the feature set fixed. This is a different axis and it is testable for zero GPU on already-cached features.

If CONFIRMED, the diagnosis moves from 'the readout is weak' (already ruled out) to 'the prefix interface has ~1-3 scalars of usable bandwidth', and the implied intervention is the class that changes what the LM COMPUTES rather than what it is biased by -- zero-init gated cross-attention (LLaMA-Adapter arXiv:2303.16199), or Q-Former-style two-stage contrastive alignment into the text subspace BEFORE the frozen LLM (BLIP-2, Li et al. 2023), which the research explicitly flagged as the most-replicated fix for exactly this orthogonality symptom.

Script scratch/probe_e29_prefix_rank.py, decision rule in its docstring, written before the run.

## Plan


## Links
Tests problem [[phase-c-hr-peak-saturated-prior-20260805]].
Predicts [[eiv_digit_readout_decoded_mae]] (none).
Gate `gate-rank3-matches-full` (blocking): PRIMARY. RidgeCV on the top 3 principal components of the frozen hidden state (PCA fit on TRAIN ONLY) must reach within 0.5 bpm of full-dimensional Ridge, i.e. test MAE <= 10.770 given E28's full-dim 10.270. PASS confirms the peak information is carried by <=3 directions. Additionally the paired bootstrap CI95 of (best-k minus k=3) must not show a material further gain; if MAE keeps improving materially past k=3 the low-rank claim FAILS and the prefix is writing distributed high-rank information.
Gate `gate-donor-no-skill-any-k` (blocking): The DONOR arm (identical PCA+Ridge pipeline on donor-prefix hidden states Hd) must show NO skill at ANY k -- MAE no better than roughly always-predict-median (18.492 bpm). If a donor arm gains skill as k grows, the sweep is fitting the marginal target distribution through extra components rather than reading this sample's prefix, and the whole sweep is uninterpretable. This control is mandatory because donor-null failures already produced false passes in E25 and E26.
Gate `gate-shuffled-null-all-k` (blocking): SHUFFLED-TARGET control at EVERY k: refit the real pipeline on a permuted training target. Must score no better than always-predict-median at all k. This is specifically required at high k, where 300 or 700 components against n_train=700 could overfit and manufacture an apparent 'more rank helps' signal that would spuriously REFUTE the low-rank hypothesis. The control protects the null branch as much as the positive one.
Gate `gate-spectrum-reported` (advisory): Report the explained-variance ratio (PC1, PC1-3, PC1-10, PC1-50) and the participation ratio (effective rank) of the train hidden states, plus corr(PC score, hr_peak) for the top 10 PCs. This shows low rank rather than asserting it, and distinguishes two different situations: hidden states that are globally low-rank (the whole residual stream is degenerate) versus hidden states that are high-rank overall but whose PEAK-PREDICTIVE content is confined to a few directions. Only the latter supports the bias reading.
Gate `gate-theory-scope-stated` (advisory): SCOPE HONESTY. Petrov et al. (arXiv:2310.19698) concerns a FIXED prefix; here the prefix varies per sample, so the theory does not transfer verbatim and this experiment tests a CONSEQUENCE of it, not the theorem. A low-rank result is consistent with the bias reading but does not prove the theorem applies. Equally, low rank at the emission position does not establish where the rank collapse happened (projector, injection, or forward pass) -- that requires a layer sweep. State both limits explicitly in the notes rather than claiming the theory has been confirmed.

#cross-modal #medgemma #collapse #sensor-text #optihealth-eiv #conditioning-collapse #data-contract-violation
