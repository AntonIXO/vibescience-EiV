---
created_at: '2026-08-06T19:26:38+00:00'
gates: []
id: the-template-is-already-learned-so-completion-only-template-masked-loss-will-buy
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_clm_value_gradient_share
  direction: up
  magnitude_note: ''
problem_id: phase-c-annotation-target-is-86pct-template-20260806
problem_tags:
- optihealth-eiv
- data-contract-violation
- conditioning-collapse
status: confirmed
supersedes: null
topic_tags:
- cross-modal
- medgemma
- sensor-text
---

## Statement
THE TEMPLATE IS ALREADY LEARNED, SO COMPLETION-ONLY / TEMPLATE-MASKED LOSS WILL BUY MUCH LESS THAN THE 7.2x THE CHARACTER-COUNT ARGUMENT IMPLIES. Measured at the soft-token prefix, VALUE tokens will already supply the MAJORITY of the CLM gradient (eiv_clm_value_gradient_share > 0.5), because per-token CE on the template is near zero while CE on the value spans is large. Specifically: mean CE on template tokens < 0.30, mean CE on value tokens > 1.0, ratio > 4x.

## Rationale
I and the literature dossier both ranked completion-only masking as a top-3 cheap intervention on the strength of '86.2% of target characters are fixed template', and I priced it at ~7.2x effective gradient (1/0.138). That is a CHARACTER-COUNT argument, and character count is not gradient. A template reproduced at 97.5% parse rate with correct key order and types is, by definition, a solved sub-problem: its per-token cross-entropy should be near zero and near-zero loss contributes near-zero gradient regardless of how many tokens carry it. exp-e33 seed logs show llm_loss converging to ~0.41-0.53 overall, which is far too low to be consistent with 86% of tokens being unlearned. If this is right, the cheap intervention is much cheaper in effect than advertised and the gradient is already going where we want it -- which would relocate the problem back onto the CLM-vs-auxiliary loss balance (3.4-4.8%) rather than the within-CLM token balance.

## Plan


## Links
Tests problem [[phase-c-annotation-target-is-86pct-template-20260806]].
Predicts [[eiv_clm_value_gradient_share]] (up).

#cross-modal #medgemma #sensor-text #optihealth-eiv #data-contract-violation #conditioning-collapse
