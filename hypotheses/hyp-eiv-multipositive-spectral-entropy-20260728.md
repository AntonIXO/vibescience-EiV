---
created_at: '2026-07-28T09:17:15+00:00'
former_ids: []
gates: []
id: hyp-eiv-multipositive-spectral-entropy-20260728
interventions:
- eiv-multi-positive-dcl
- eiv-multipositive-native-spectral-entropy
paper_refs:
- paper-dimensional-collapse-2110-09348
- paper-align-uniform-2005-10242
- paper-dcl-2110-06848
- paper-supcon-2004-11362
predicted_effects:
- diagnostic_id: eiv_effective_rank
  direction: up
  magnitude_note: Primary. Seed-17 grid candidate must reach absolute held-out native
    sensor effective rank >=5.0 and improve at least +0.25 versus paired multi-positive
    DCL. Confirmation requires every treatment seed rank>=5.0, mean paired delta>=+0.25,
    one-sided exact sign-flip p<=0.05, and deterministic 95% paired-seed bootstrap
    CI lower>0.
- diagnostic_id: eiv_sensor_spectral_entropy
  direction: up
  magnitude_note: The final logged training-batch normalized spectral entropy must
    be finite and higher than the paired multi-positive comparator; the penalty is
    computed directly on the downstream native sensor tensor without a learned head.
problem_id: optihealth-multipositive-dcl-20260728
problem_tags:
- representation-collapse
- anisotropy
- false-negatives
- optihealth-eiv
- subject-heldout
status: confirmed
supersedes: hyp-eiv-multipositive-dcl-20260728
topic_tags:
- multi-positive
- dcl
- spectral-entropy
- effective-rank
- batch-gram
- contrastive
- sensor-text
- false-negatives
- cross-modal
- anti-collapse
---

## Statement
A weak sensor-only batch-Gram spectral-entropy penalty applied directly to the evaluated normalized native embeddings will raise multi-positive DCL above the effective-rank gate while retaining its duplicate-aware retrieval benefit.

## Rationale
Exact-caption multi-positive DCL removed every audited false negative and produced small retrieval/rank gains, but native sensor geometry remained anisotropic: effective ranks 4.381/4.850/4.890. The remaining diagnosed failure is therefore low spectral entropy, not directional collapse (median cosine 0.135-0.169). The intervention exactly matches the evaluation tensor: L2-normalize native sensor rows in float32, center features, form the BxB Gram matrix, obtain nonnegative eigenvalues, normalize them to a probability distribution, and minimize 1-H(p)/log(B-1). This is bounded and projection-free. It is sensor-only because sensor effective rank is the registered collapse gate and avoids a second text-side mechanism. Multi-positive DCL remains the trained comparator; no population normalization, CCA/VICReg, HSIC/KTA, text loss head, or test access is introduced.

## Plan
New frozen output root and implementation manifest. Implement only a parameter-free float32 NativeSpectralEntropyLoss on native sensor embeddings and one Phase-A objective branch/weighted term. Engineering smokes are excluded. Pilot grid on validation: paired seed17 multi_positive_dcl comparator plus multi_positive_dcl_spectral weights [0.01,0.03,0.10], 500 steps, otherwise identical bf16 batch102 configuration and batches. Fully resolved diff allowlist: contrastive_objective, native_spectral_entropy_weight, output/state paths, and required objective metadata only. A candidate is eligible only if effective-rank delta>=+0.25 and absolute rank>=5, Recall@5 delta>=-0.005, median-rank regression<=2, cosine<=0.90, finite/no OOM, peak reserved VRAM<11.25GiB, runtime<=110%, and false negatives used remain zero. Select eligible candidate by highest Recall@5; within 0.002 choose smaller weight, then config id. Freeze confirmation only after all grid runs aggregate. Confirmation uses new paired validation seeds [31,37,41,43,47], 500 steps, selected weight; requires the primary rank statistical/magnitude gates, mean Recall@5 delta>=-0.005, at least 3/5 Recall deltas nonnegative, no per-seed median-rank regression>2, every cosine<=0.90, and all integrity/resource gates. Locked test remains unread unless every confirmation gate passes; then evaluate frozen paired checkpoints once without selection. Any pilot near miss produces selected=null and no confirmation/test.

## Links
Tests problem [[optihealth-multipositive-dcl-20260728]].
Applies [[eiv-multi-positive-dcl]], [[eiv-multipositive-native-spectral-entropy]].
Predicts [[eiv_effective_rank]] (up).
Predicts [[eiv_sensor_spectral_entropy]] (up).
Cites [[paper-dimensional-collapse-2110-09348]], [[paper-align-uniform-2005-10242]], [[paper-dcl-2110-06848]], [[paper-supcon-2004-11362]].
Supersedes [[hyp-eiv-multipositive-dcl-20260728]].

#multi-positive #dcl #spectral-entropy #effective-rank #batch-gram #contrastive #sensor-text #false-negatives #cross-modal #anti-collapse #representation-collapse #anisotropy #false-negatives #optihealth-eiv #subject-heldout
