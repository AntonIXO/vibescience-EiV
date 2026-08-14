---
direction: neutral
id: eiv_gen_bucket_acc_over_majority
name: Generated HR-bucket accuracy over the majority-class baseline
requires_control: null
topic_tags: []
unit: accuracy_delta
---

## Description
Generated-HR-bucket accuracy MINUS the always-predict-majority baseline on the SAME pool.
Higher is better; ZERO means the LLM's emitted bucket is worth exactly nothing over a constant.

WHY THIS EXISTS. On 2026-08-08 a full end-to-end 1-minute run was closed as "supports" while its
actual headline finding was that the pipeline EMITS worse than a constant. That was possible because
no registered diagnostic captured emission accuracy against its own trivial baseline: the closure
was scored on encoder readout (eiv_peak_excursion_increment_r2, eiv_heldout_hr_r2), both of which
looked good, while p4 hr_acc 0.4609 against a 0.569 majority baseline went unrecorded. A metric that
cannot fail cannot gate anything.

DEFINITION. acc(model) - max_class_frequency(pool), where acc(model) is p4_bucket_accuracy.hr_acc
from src/probes/bucket_parser.py AFTER commit bba9bcf (which fixed it to grade the daily PEAK with
the corpus cut points recorded in the adapter manifest, instead of the daily MEAN with hardcoded
60/80/100 -- under the old scorer a PERFECT model capped at 24.11%).
The majority baseline MUST be recomputed on the same evaluation pool and quoted alongside, never
assumed: on the strict clean 1-minute pool the training-label marginal is resting 11.0% /
elevated 56.9% / tachycardic 32.1%, so the baseline is 0.569.

CURRENT VALUE, 1-minute end-to-end adapter medgemma_12ch_v3_1min_e2e: 0.4609 - 0.569 = -0.108.
The emitted bucket is ten points WORSE than always saying "elevated", while a linear probe on the
same model's hidden states reads hr at R2 0.9649. That gap is the deliverable-side failure, and this
diagnostic is what makes it impossible to close a run without confronting it.

*Unit:* `accuracy_delta` — *neutral*
