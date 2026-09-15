---
direction: lower_better
id: eiv_peak_vram_gib
name: eiv_peak_vram_gib
noise_k: 2.0
noise_n: null
noise_note: ''
noise_sigma: null
noise_source: null
requires_control: null
topic_tags: []
unit: GiB
---

## Description
Peak allocated CUDA memory for the training run on the RTX 5070; operational gate is below 11.25 GiB with no OOM.

*** 2026-09-01 IMPOSSIBLE-VALUE DEFECT -- READ BEFORE TRUSTING THIS GATE ***
Four of the six recorded evaluations report a value the hardware cannot produce (scratch/_vram_plausibility_audit.py, checked against the live device):
  exp-014 / exp-015 (dcl-vs-hard-infonce)          0.9639 -> 0.9652 GiB   plausible
  exp-eiv-multipositive-dcl-pilot-v2             113.7123 -> 113.6921 GiB  9.5x CARD CAPACITY
  exp-eiv-multipositive-spectral-entropy-pilot   114.3529 -> 114.2425 GiB  9.6x CARD CAPACITY
nvidia-smi reports the device as NVIDIA GeForce RTX 5070, 12227 MiB = 11.94 GiB usable. 113-114 'GiB' is therefore not a noisy reading of peak VRAM; it is a DIFFERENT QUANTITY recorded under this id (candidate: a percentage, a MiB figure mis-scaled, or a measurement taken elsewhere). The ~0.96 values are plausible for a small cached pilot.

WHY THIS IS WORSE THAN A BOOKKEEPING SLIP: this diagnostic carries an OPERATIONAL GATE ('below 11.25 GiB with no OOM'). Two experiments recorded values ~10x above that gate and were not marked as gate failures, so either the gate never evaluated or it evaluated a number that did not mean peak VRAM. Any past 'VRAM is fine' conclusion resting on those two runs is unsupported.

WHY NO NOISE FLOOR IS REGISTERED. doctor() asks for one. A pooled sigma over 0.96 and 114 lands near 60 GiB, which would put the 11.25 GiB gate permanently inside noise and turn a safety gate into a rubber stamp -- strictly worse than having no floor. The honest state is UNFLOORED with the defect recorded here.

ACTION REQUIRED (definition + provenance, not a sigma): establish what the two multipositive pilots actually measured, restate the diagnostic unambiguously, and re-express or discard those values. Until then, compare only values that are individually plausible against the card.

*Unit:* `GiB` — *lower_better*
