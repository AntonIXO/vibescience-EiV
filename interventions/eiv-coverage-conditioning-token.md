---
id: eiv-coverage-conditioning-token
name: Explicit coverage/duration conditioning (embedding or FiLM)
topic_tags:
- extent-heterogeneity
- dataset-whitelist
- denominator
---

## Description
Feed the window's measured extent to the model as an EXPLICIT input rather than leaving it to be inferred from the fill pattern: a scalar coverage/duration embedding (or FiLM modulation of the encoder's hidden state) conditioned on hr_present_minutes and per-channel coverage. Rationale: the contraction naive = median + c*(present-median) is INVERTIBLE if and only if c is known to the reader, so handing the model c is the minimum information needed to de-dilute. Ambivalent by design: it also makes the provenance shortcut MORE accessible (extent alone gives dataset identity at 12.03x chance), so this intervention must always be scored together with a shortcut-decodability control.

#extent-heterogeneity #dataset-whitelist #denominator
