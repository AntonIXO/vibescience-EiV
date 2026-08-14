---
created_at: '2026-07-28T08:19:06+00:00'
former_ids: []
gates: []
id: hyp-eiv-multipositive-dcl-20260728
interventions:
- eiv-dcl-package
- eiv-multi-positive-dcl
paper_refs:
- paper-dcl-2110-06848
- paper-supcon-2004-11362
- paper-debiased-cl-neurips2020
- paper-dclr-2205-00656
- paper-hard-neg-2010-04592
- paper-align-uniform-2005-10242
- paper-dimensional-collapse-2110-09348
- paper-modality-gap-2203-02053
predicted_effects:
- diagnostic_id: eiv_duplicate_recall5
  direction: up
  magnitude_note: 'Primary. Against same-seed trained DCL on frozen validation indices:
    pilot mean paired delta >= +0.005 with at least 2/3 positive; confirmation mean
    paired delta >= +0.010, all five paired seeds complete, one-sided exact sign-flip
    p <= 0.05, and a preregistered 95% paired-seed bootstrap CI lower bound > 0.'
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Treatment must clear the unchanged absolute native rank gate >=5.0
    in every eligible pilot and confirmation seed; mean paired direction must be positive.
- diagnostic_id: eiv_crossmodal_median_rank
  direction: down
  magnitude_note: Mean paired validation median-rank improvement is predicted to be
    at least 3 ranks; no treatment seed may worsen by more than 2 ranks.
- diagnostic_id: eiv_false_negative_pairs_per_batch
  direction: down
  magnitude_note: 'Exact mechanism check: off-diagonal canonical equivalents placed
    in the negative denominator must fall from the audited positive baseline count
    to exactly zero in the treatment while the batch stream remains identical.'
problem_id: optihealth-multipositive-dcl-20260728
problem_tags:
- false-negatives
- representation-collapse
- subject-heldout
- optihealth-eiv
status: testing
supersedes: null
topic_tags:
- dcl
- multi-positive
- contrastive
- sensor-text
- false-negatives
- cross-modal
---

## Statement
Replacing each one-positive main and auxiliary DCL term with a frozen exact-caption multi-positive DCL term will improve duplicate-aware cross-modal retrieval and clear the native effective-rank gate by removing measured caption-equivalent false negatives, without changing the incumbent optimizer, batch stream, uniformity term, or downstream representation.

## Rationale
The corrected sampler removed repeated raw rows, but 25/48 audited batches still contained exact caption-equivalent off-diagonal pairs. In the incumbent DCL these known equivalents remain in the negative-only denominator. The intervention uses P(i) = every candidate with the same frozen canonical-caption hash, N(i) = every candidate not in P(i), and per-anchor loss -mean_{p in P(i)} s(i,p) + logsumexp_{n in N(i)} s(i,n), symmetrized sensor-to-text and text-to-sensor. This is the SupCon L_out mean-per-positive attraction adapted to true DCL semantics: unlike a naive formula, every equivalent positive is excluded from the denominator. The diagonal guarantees at least one positive; the harness rejects an anchor with no negative. Predicate version canonical_caption_sha256_ws_casefold_v1 has frozen spec SHA256 46d57eecd32d0db7bdbe96e0434072204d90167b0a555a87511644179459cede. Projected CCA/VICReg, adaptive-HSIC selection, native KTA, population remerge, and spectral regularization are outside this hypothesis.

## Plan
Freeze a new output root and manifest before science. Engineering smokes (2 steps, tagged/excluded) first. Stage 1 validation pilots use paired seeds [17,23,29], 500 steps, bf16, batch102, one-positive DCL then multi-positive DCL, same seed/data order and fixed evaluation indices. The only resolved training-config difference may be contrastive_objective/multi-positive mask mode; output/state fields and equivalence metadata are separately allowlisted. Pilot eligibility additionally requires native median cosine <=0.90, mean median-rank delta <=+2, finite loss, no OOM, peak reserved VRAM <11.25 GiB, treatment training time <=110% paired baseline, identical source/split/mask/evaluation hashes, and all reports/checkpoints/predictions checksummed. Only after all pilot runs are aggregated may an immutable confirmation lock be created. Stage 2 uses new paired validation seeds [31,37,41,43,47], again 500 steps. Confirmation requires primary magnitude/statistical gates plus every treatment rank>=5, cosine<=0.90, no per-seed median-rank regression >2, and all integrity/resource gates. No multiple-comparison correction is needed because there is one confirmatory alternative. The locked test remains unread and unevaluated unless every confirmation gate passes; if unlocked, evaluate the frozen five baseline/treatment checkpoint pairs once with no selection or tuning. If Recall@5 improves but rank remains below 5, close this hypothesis without confirmation/test and propose weak native batch-Gram spectral entropy as a separate campaign, never by amending this one.

## Links
Tests problem [[optihealth-multipositive-dcl-20260728]].
Applies [[eiv-dcl-package]], [[eiv-multi-positive-dcl]].
Predicts [[eiv_duplicate_recall5]] (up).
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_crossmodal_median_rank]] (down).
Predicts [[eiv_false_negative_pairs_per_batch]] (down).
Cites [[paper-dcl-2110-06848]], [[paper-supcon-2004-11362]], [[paper-debiased-cl-neurips2020]], [[paper-dclr-2205-00656]], [[paper-hard-neg-2010-04592]], [[paper-align-uniform-2005-10242]], [[paper-dimensional-collapse-2110-09348]], [[paper-modality-gap-2203-02053]].

#dcl #multi-positive #contrastive #sensor-text #false-negatives #cross-modal #false-negatives #representation-collapse #subject-heldout #optihealth-eiv
