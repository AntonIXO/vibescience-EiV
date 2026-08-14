---
created_at: '2026-08-14T09:00:33+00:00'
gates:
- blocking: true
  description: 'BLOCKING two-sided decision: best projector-output readout (pooled
    or slot mean+max) subject-grouped OOF R2 on raw daily hrv_rmssd (ms) >= 0.35 =>
    hrv is READOUT-limited (the prefix carries it; E9''s lm_head arm plausibly lifts
    it and its g4 hrv capture becomes a confirmation read). < 0.15 => ENCODER-limited
    (readout arms can never fix hrv; lever moves to encoder/data). 0.15-0.35 partial.'
  id: g1-hrv-in-prefix
- blocking: false
  description: 'Informational thermometer control: increment of the same readout beyond
    R2(hrv | true daily hr mean, 1 feature). If the raw R2 is high but the increment
    ~0, the prefix ''hrv'' readout is just an hr thermometer and the dense corr 0.60
    was hr-mediated — a different, worse verdict for hrv than readout-limited.'
  id: g2-beyond-hr-thermometer
id: hyp-e10-hrv-localization-20260814
interventions: []
paper_refs: []
predicted_effects:
- diagnostic_id: eiv_nonhr_channel_grouped_r2
  direction: up
  magnitude_note: 'Channel = hrv_rmssd (diagnostic covers any non-HR canonical channel;
    its recorded reference values are rr -0.0757 / sleep_deep -0.1847 on the OLD 12ch_v2
    checkpoint). Committed: on v9band04_s42 the projector output carries daily hrv
    at OOF R2 0.40-0.65 (readout-limited zone) — emitted dense corr 0.60 lower-bounds
    prefix content at ~0.36 and linear probes usually beat the emission path here
    (hr analogy: prefix 0.53 vs emitted corr 0.17-0.35 for peak). Thermometer increment
    prediction: >= 0.15 beyond hr-mean control (hrv is not just an hr readout; corr(hrv,hr)
    is moderate negative physiologically).'
problem_id: phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling
problem_tags: []
status: confirmed
supersedes: null
topic_tags:
- cross-modal
- sensor-text
- wearables
- collapse
- deployment-distribution
- optihealth
- baseline
---

## Statement
H-E10 (hrv localization): the v9band04 prefix (projector output) carries the daily hrv_rmssd level at subject-grouped OOF R2 >= 0.35 — i.e. hrv's mediocre dense emission (corr 0.60) is readout-limited like hr_peak, not encoder-limited — and the readout-stage lever (E9 lm_head arm) is therefore in scope for hrv too.

## Rationale
E3 refuted the hrv band law (destroyed generalization). The dissociation criterion justified that law from dense corr 0.60 — but 0.60 is midway: neither the hr_raw pattern (dense ~0.9, pure prototype problem) nor the hr_peak pattern (dense 0.17-0.35, representation problem). The E6-E8 instrument chain now exists to split this properly: measure hrv content at the projector output directly instead of inferring from emissions. Runs entirely on CPU while E9 occupies the GPU (Anton's standing continuous-mode directive). Whichever zone it lands in kills one class of future hrv arms.

## Plan
E10 CPU-only probe scratch/e10_hrv_prefix_probe.py (CUDA_VISIBLE_DEVICES= forced; GPU is running E9). Frozen v9band04_s42 encoder+fusion+projector, eval_v3_1min rows with >= 60 present hrv epochs, n<=2500. Targets: raw daily hrv mean (ms, present-only, ch1 denormalized). Arms: pooled soft tokens, slot mean+max. RidgeCV GroupKFold(5) by subject. Controls: R2(hrv|true hr mean) on same folds (thermometer), shuffled-target null. Output logs/e10_hrv_prefix_probe.json. Runs in systemd scope MemoryMax=10G. Verdict routes: g1 >= 0.35 -> hrv readout-limited; read E9 g4 hrv capture as the intervention test; if E9 lifts hrv too, one arm fixed two slots. g1 < 0.15 -> rule_out 'readout arms for hrv'; encoder/data lever (hrv coverage 0.31-mean when present; maybe M_held-aware pooling). Middle -> partial, report split.

## Links
Tests problem [[phase-c-arms-fail-on-the-deployment-distribution-optihealth-supabase-hr-raw-floor-atom-at-30-0-plus-unlearned-sparsity-handling]].
Predicts [[eiv_nonhr_channel_grouped_r2]] (up).
Gate `g1-hrv-in-prefix` (blocking): BLOCKING two-sided decision: best projector-output readout (pooled or slot mean+max) subject-grouped OOF R2 on raw daily hrv_rmssd (ms) >= 0.35 => hrv is READOUT-limited (the prefix carries it; E9's lm_head arm plausibly lifts it and its g4 hrv capture becomes a confirmation read). < 0.15 => ENCODER-limited (readout arms can never fix hrv; lever moves to encoder/data). 0.15-0.35 partial.
Gate `g2-beyond-hr-thermometer` (advisory): Informational thermometer control: increment of the same readout beyond R2(hrv | true daily hr mean, 1 feature). If the raw R2 is high but the increment ~0, the prefix 'hrv' readout is just an hr thermometer and the dense corr 0.60 was hr-mediated — a different, worse verdict for hrv than readout-limited.

#cross-modal #sensor-text #wearables #collapse #deployment-distribution #optihealth #baseline
