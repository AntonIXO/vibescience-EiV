---
aliases:
- degenerate-targets
- label-contamination
- missingness-encoding
- canonical-schema
axis: problem
created_at: '2026-08-05T10:28:26+00:00'
id: data-contract-violation
---

## Description
The stored data violates its own schema/contract in a way that silently produces wrong supervision rather than an error: absence encoded as present-and-zero, mask polarity or coverage flags disagreeing with the values they describe, or normalisation scales that make a channel uninterpretable. Distinct from ordinary label noise because the pipeline reports the data as valid, so downstream marginals, targets and evaluation splits are all computed on corrupted inputs and look self-consistent.

*Axis:* `problem`

## Aliases
- degenerate-targets
- label-contamination
- missingness-encoding
- canonical-schema
