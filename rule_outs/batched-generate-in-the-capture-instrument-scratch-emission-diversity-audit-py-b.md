---
created_at: '2026-08-13T20:18:00+00:00'
id: batched-generate-in-the-capture-instrument-scratch-emission-diversity-audit-py-b
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-13T20:18:00+00:00'
topic_tags:
- optihealth
---

## Claim
Batched generate() in the capture instrument (scratch/emission_diversity_audit.py --batch-size >1) is a free speedup: greedy decode with an identical shared prompt is batch-invariant, so captures can be batched for ~4.7x throughput

## Method
A/B equivalence probe on GPU: same adapter (v9band04_s42), same 32-row frozen supabase draw (default_rng(0)), batch=1 vs batch=8, byte-diff of every emitted text. Timing: 2m15s vs 29s.

## Evidence
31/32 emission texts DIFFER at batch=8 despite identical prompts, greedy decoding, and identical prefix lengths (no padding asymmetry). Mechanism: cuBLAS batched-GEMM reduction-order nondeterminism perturbs logits enough to flip argmax on near-ties — e.g. hr_raw 59.5<->60.3, hr_pct 23<->25, hr_peak 139.1<->138.3. Side observation worth keeping: a float summation-order change flips ~1/3 of rows between adjacent lattice values, i.e. the model's numeric emissions sit ON near-ties between quantized prototypes — independent confirmation of the crystallization picture. Consequence: all quotable captures stay batch_size=1 (~14 min/200 rows); the flag carries a rejection warning in-script. Speed alternatives if ever needed: KV-cache prompt reuse or CUBLAS_WORKSPACE_CONFIG+deterministic algorithms, each requiring its own equivalence proof first.

## Links
Rules out for problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].

#optihealth
