---
created_at: '2026-08-13T21:05:42+00:00'
gates:
- g1-supabase-hrv-raw-mae-below-trivial-8-31
- g2-hrv-collapse-breaks-top1-below-0-5-and-stdratio-above-0-3
- g3-BLOCKING-hr-preserved-supa-le-3-45-dense-le-4-73
- g4-dense-hrv-skill-ge-1-5
hypothesis_id: hrv-raw-fails-on-deployment-for-the-same-reason-hr-raw-did-pre-band-law-the-trai
id: rb-e3-hrv-band-20260813
priority: 1
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
status: done
topic_tags: []
updated_at: '2026-08-13T22:08:24+00:00'
---

## Prompt
E3: train lora_proxy_12ch_wl_band04_hrv (v10hrv, seed 42, single variable vs baseline: hrv band law on) via scratch/run_e3_hrv.sh -> Phase C 2600 steps from v5c/phase_b, then supabase-200 + dense-128 captures (batch 1), then score hr with scratch/score_e1b.py and hrv/state slots with scratch/score_slots_deployment.py. Gates preregistered on exp-e3-hrv-band-20260813; g3 is BLOCKING (hr axes must stay within worst v9band04 seed values or the law interferes and the arm is rejected regardless of hrv gains). Hypothesis: hrv-raw-fails-on-deployment-for-the-same-reason-hr-raw-did-pre-band-law-the-trai.

## Preconditions
GPU free (one 4-bit medgemma job at a time); PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True; check *_RC= markers not wrapper exit codes; captures batch_size=1 only (batched generate ruled out 2026-08-13).

## Links
Queued for problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Run as [[hrv-raw-fails-on-deployment-for-the-same-reason-hr-raw-did-pre-band-law-the-trai]].
Intended gates: `g1-supabase-hrv-raw-mae-below-trivial-8-31`, `g2-hrv-collapse-breaks-top1-below-0-5-and-stdratio-above-0-3`, `g3-BLOCKING-hr-preserved-supa-le-3-45-dense-le-4-73`, `g4-dense-hrv-skill-ge-1-5`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.
