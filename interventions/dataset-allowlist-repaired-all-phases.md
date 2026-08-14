---
id: dataset-allowlist-repaired-all-phases
name: dataset-allowlist-repaired-all-phases
topic_tags: []
---

## Description
Replace the three DIFFERENT per-phase dataset allowlists with ONE repaired allowlist applied identically in Phase A, Phase B and Phase C: {lifesnaps, pmdata, optihealth_supabase, big_ideas_glycemic, mmash, dreamt}.

WHAT IT REPLACES (all measured on the rebuilt [10338,1440,12] corpus):
  Phase A `phase_a_12ch_pmdata`  : 10 datasets, six of which contribute 0 strict-clean days
  Phase B `phase_b_12ch`         : the same 10 via the *id_12ch YAML anchor
  Phase C `lora_proxy_12ch`      : a 3-key WEIGHTED DICT {lifesnaps, pmdata, optihealth_supabase}
So the encoder is contrastively aligned on a cohort Phase C never grounds on, and vice versa.

CONCRETE CONFIG DELTA (configs/training.yaml):
  phase_a_12ch_1min_wl  - dataset_filter list -> the 6 names, anchored as &id_wl_12ch
  phase_b_12ch_wl       - `<<: *phase_b12` + `dataset_filter: *id_wl_12ch` (nothing else differs)
  lora_proxy_12ch_wl    - `<<: *lora12c` + the E40 pure-CLM balance (probe_weight 0,
                          hr_regression_weight 0, lora_warmup_steps_aux_only 0) + the 6 names
                          as a dict at weight 1.0 each. Unit weights matter: this key doubles as
                          a per-dataset SAMPLING WEIGHT, so any non-unit value would confound the
                          allowlist variable with a re-weighting.

DROPPED: wesad, nurse_stress, wearable_exam_stress, neuro_stress_resilience_hci, brain_wearable_monitoring, consumer_grade_wearables (HR coverage 0.000-0.157, zero strict-clean days, and every one of them is a 100%-pure state_label identifier that Phase B mines triplets on).
ADDED to A/B: optihealth_supabase, mmash. ADDED to C: big_ideas_glycemic, mmash, dreamt.
STILL EXCLUDED EVERYWHERE: dryad_ifh_affect - 15-minute native (measured_cov 0.070 vs present_cov 0.313, 77.6% carried), degenerate peak marginal (tachy 0.000, median peak 89.0 bpm vs the corpus 131.8), and independently shown to be the sole cause of the Phase-C provenance shortcut.
