---
direction: higher_better
id: eiv_clm_value_gradient_share
name: Share of the CLM gradient into the soft-token prefix from VALUE tokens
requires_control: null
topic_tags: []
unit: fraction
---

## Description
||d L_value / d soft_tokens|| / (||d L_value / d soft|| + ||d L_template / d soft||), where L_value is the summed cross-entropy over the @@-marked sensor-dependent spans and L_template is the summed CE over every other response token. Measures whether the 86.2%-of-characters fixed template actually COMPETES for gradient, or is already learned to ~zero loss and therefore contributes little. A character-count argument ('the template is 86% of the text') is NOT evidence about gradient; this is.

*Unit:* `fraction` — *higher_better*
