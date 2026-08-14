---
created_at: '2026-08-05T21:27:09+00:00'
former_ids:
- rb-e30-variance-localisation-where-does-the-hr-peak-signal-become-high-rank-and-
gates:
- gate-donor-null-all-stages
- gate-shuffled-null-all-stages
hypothesis_id: null
id: rb-e30-variance-localisation-completed-c898b9
notes: []
priority: 10
problem_id: phase-c-hr-peak-saturated-prior-20260805
status: abandoned
topic_tags:
- cross-modal
- medgemma
- collapse
updated_at: '2026-08-05T21:43:40+00:00'
---

## Prompt
rb-e30-variance-localisation -- COMPLETED as exp-e30-variance-localisation-20260805 (2026-08-05). Status 'abandoned' only because the server requires a hypothesis_id to mark a runbook 'done' and this runbook was registered without one; the WORK WAS DONE IN FULL and the experiment is closed. DO NOT RE-RUN.

=== RESULT ===
Full-dimensional Ridge MAE by stage (same samples/split/donors, one pass, n_train=700/n_test=300, always-median 18.492):
  S0 projector      7.605
  S1 layer0 prefix  7.608
  S2 L8             9.026
  S2 L17            9.723
  S2 L25           10.159
  S3 L34           10.270
Monotone after injection. The frozen forward pass costs 2.665 bpm of decodable hr_peak.
Anchor reproduced exp-e29 EXACTLY (full-dim 10.270/10.270; k=3 18.407/18.407) from an independently written script, so cross-stage comparison is admissible. Donor arms 19.2-19.4 and shuffled arms 19.2-19.7 at every k at every stage -- both worse than a constant, neither improving with k.

=== DIAGNOSIS: BOTH BRANCHES, NOT ONE ===
The runbook's decision rule assumed branch (a) and branch (b) were alternatives. They are not.
  (a) TRUE and LARGER: the projector output is ALREADY off-manifold. S0 alignment 0.1602; the S0 rank sweep needs k>=100 to decode, and k=3 gives 18.170 vs median 18.492 -- a rank-3 subspace of the PROJECTOR OUTPUT is already worthless.
  (b) TRUE but SMALLER: decodability degrades a further 2.665 bpm through the stack.

=== THE HEADLINE NUMBER ===
THE PROJECTOR OUTPUT DECODES AT 7.605 bpm -- 10.887 bpm better than always-predict-median and 2.665 better than anything at the emission position. Every experiment E24-E29 measured at or after the emission position and therefore measured a DEGRADED copy. The representation entering the LM is materially better than this problem record assumed.

=== METHOD NOTE FOR ANY SUCCESSOR ===
The preregistered PRIMARY statistic (signal-variance alignment = variance-weighted mean |corr(PC_i,y)| over the top 50 PCs) FAILED as an instrument: it is uniformly low (0.10-0.17) and non-monotone across stages. It averages over leading components and therefore discards exactly the high-rank low-variance structure exp-e29 proved carries this signal. Use the RANK SWEEP / full-dimensional decodability, not alignment, for this question.
APPARATUS: locate sensor positions with token_type_ids (1=sensor, gemma_runtime.py:373), NEVER with the slot mask from _slot_mask_from_channel_mask -- the latter indexes the 96 soft tokens rather than the assembled context and silently yields constant prompt positions, zero variance and NaN spectra. Caught by an n=12 smoke test before the real run.

=== SUCCESSOR ===
Target mechanism (a) first: variance-matching / whitening the projector output to the token-embedding distribution, and/or initialising soft tokens from real vocabulary embeddings. Near-free, and now motivated by measurement rather than analogy. Gated cross-attention (arXiv:2303.16199) and Q-Former alignment (BLIP-2) remain live for mechanism (b) but address the smaller 2.665 bpm component and should not be launched first. A per-slot (rather than mean-pooled) analysis of S0 is the obvious cheap follow-up.

Artifacts: outputs/probes/e30_variance_localisation.json, logs/e30_localisation.log. Script scratch/probe_e30_variance_localisation.py.

## Preconditions
Zero training. One GPU extraction pass (~20 min, same cost as E24/E26 extraction) on the same seed/samples/donors, strict-clean population, E24c neutral context, adapter outputs/adapters/medgemma_12ch_e23v2, 4-bit. Reuse the E26/E27/E28/E29 train/test split so every number is comparable. RTX 5070, stay under 11.25 GiB.

## Links
Queued for problem [[phase-c-hr-peak-saturated-prior-20260805]].
Intended gates: `gate-donor-null-all-stages`, `gate-shuffled-null-all-stages`.
This is a PLAN, not a preregistration — the prediction is committed via propose_hypothesis at run time.

#cross-modal #medgemma #collapse
