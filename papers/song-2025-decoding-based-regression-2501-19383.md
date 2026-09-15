---
arxiv_id_or_url: '2501.19383'
id: song-2025-decoding-based-regression-2501-19383
title: Decoding-based Regression
topic_tags:
- optihealth
- readout
- target-style
---

## Key claims
- A small autoregressive decoder head trained with plain cross-entropy over digit tokens matches or beats a pointwise MSE head on OpenML/AMLB tabular regression and BBOB, given the SAME encoder features — decoding numbers as tokens is not intrinsically lossy.
- The conditions of that result: digit-by-digit tokenization, CONSTRAINED decoding that can only emit a valid number, a head trained from scratch, and no intermediate language tokens. Sec. 5 names each departure as expected to HURT: LLM vocabularies without single-digit tokens, top-p/top-k sampling, and interleaved language tokens (a JSON template) — the last is explicitly untested.
- Pointwise estimate must be the loss-matched statistic of the decoded distribution: median of samples for L1/MAE, mean for L2, mode/greedy for L0. Repeating the number K times and majority-voting per digit (error correction) cuts outlier-driven MSE substantially.
- Head size beyond ~3 layers/128 units overfits; the decoder head itself is <10% of parameters — precision loss in a large LLM is therefore not a capacity problem.

#optihealth #readout #target-style
