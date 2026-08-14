---
direction: higher_better
id: eiv_text_cache_batch_invariance_cosine
name: EiV cached text batch-invariance cosine
unit: cosine
---

## Description
Median cosine between a frozen cached text target and a fresh required MedGemma-4B hidden_states_last_token embedding of the identical caption under a changed batching context. A valid semantic cache should remain near 1; large decreases expose padding/index or batch-context contamination.

*Unit:* `cosine` — *higher_better*
