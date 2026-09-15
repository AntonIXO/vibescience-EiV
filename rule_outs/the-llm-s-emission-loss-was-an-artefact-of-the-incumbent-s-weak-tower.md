---
created_at: '2026-09-02T18:59:01+00:00'
former_ids: []
id: the-llm-s-emission-loss-was-an-artefact-of-the-incumbent-s-weak-tower
problem_id: is-the-frozen-llm-load-bearing-or-a-formatter
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-02T18:59:01+00:00'
topic_tags:
- readout
- control
- baseline
- optihealth
---

## Claim
That exp-t1's "the LLM is a formatter" result was an artefact of the tower it was measured on -- i.e. that MedGemma+LoRA lost 3.38 bpm to a ~2M attention head because the incumbent's PL120 Phase B tokens were poor, and that a better representation would close the gap. ELIMINATED, and the gap WIDENS on the better tower. Also eliminated: that v41rebased's 1.586 bpm win over arm A0 shows the LLM path improving -- it shows the TOWER improving, with the LLM still discarding most of what the better tower supplies.

## Method
scratch/t1b_heads_v41_tower.py -- scratch/t1_llm_ablation.py with four constants changed (BUNDLE -> outputs/adapters/v41rebased_c, PROFILE -> lora_proxy_12ch_v41pl60, token cache, output path) and nothing else: same extract/train code, same 3,000-step attention head and mean-pool ridge, same seed 0, same 5,889-row training roster and same 1,485-row held-out eval pool, and its own random-init tower control comes along unchanged.

Scored PAIRED against the LLM captures on the 128 row indices all six arms share, truth values verified equal row-for-row, trivial always-predict-median MAE recomputed on those rows (6.876 hr_raw / 17.979 hr_peak), 10,000-resample bootstrap CIs. Artefacts: logs/t1b_extract.log, logs/t1b_train.log, logs/t1b_heads_v41_tower.json, logs/t1b_paired_six_arm.json.

## Evidence
Six consumers, one pool, n=128 shared rows.

hr_raw (trivial 6.876)            MAE     skill    corr   scale
  head attn ~2M  on PL60  tower   0.812   +6.064   0.992   1.02
  head attn ~2M  on PL120 tower   1.047   +5.829   0.987   1.02
  head attn ~2M  on PL120 RANDOM  1.687   +5.189   0.961   1.04
  head attn ~2M  on PL60  RANDOM  2.086   +4.790   0.938   1.04
  LLM MedGemma+LoRA on PL60 (v41) 2.843   +4.033   0.924   0.99
  LLM MedGemma+LoRA on PL120 (A0) 4.429   +2.447   0.820   0.99

hr_peak (trivial 17.979)          MAE     skill    corr   scale
  head attn ~2M  on PL60  tower  11.169   +6.810   0.726   0.72
  LLM MedGemma+LoRA on PL60 (v41)14.288   +3.691   0.576   0.75
  head attn ~2M  on PL120 tower  14.467   +3.512   0.501   0.52
  head attn ~2M  on PL120 RANDOM 16.791   +1.188   0.354   0.47
  head attn ~2M  on PL60  RANDOM 16.915   +1.064   0.312   0.47
  LLM MedGemma+LoRA on PL120 (A0)18.360   -0.381   0.380   0.79

THE ELIMINATING NUMBER. Paired, LLM minus the head reading its OWN frozen tower:
  on the PL120 tower (exp-t1)  +3.382 bpm hr_raw, CI95 [+2.857, +3.935], head better on 89.8%
  on the PL60  tower (this)    +2.031 bpm hr_raw, CI95 [+1.606, +2.485], head better on 83.6%
                               +3.120 bpm hr_peak, CI95 [+1.625, +4.686], head better on 66.4%
The absolute gap narrows in bpm and the RELATIVE loss does not: the PL60 tower supports 0.812 bpm and the LLM emits 2.843, so it discards 66% of the skill available over the trivial constant (+6.064 -> +4.033). On the PL120 tower it discarded 58% (+5.829 -> +2.447). A better representation did not make the LLM a better reader of it.

THE TOWER, NOT THE LLM, IS WHAT v41 IMPROVED. Head on PL60 minus head on PL120, same head, same rows: -0.235 bpm hr_raw (CI95 [-0.427, -0.046]) and -3.299 bpm hr_peak (CI95 [-4.835, -1.821]). Both CIs exclude zero, and the hr_peak effect is the large one -- 47 patches of 60 minutes resolve an excursion that 23 patches of 120 minutes average away.

AND THAT IMPROVEMENT IS LEARNED, NOT GEOMETRIC. Against its own random twin the PL60 tower gains -1.275 bpm hr_raw (CI95 [-1.687, -0.894]) and -5.746 bpm hr_peak (CI95 [-7.626, -3.893]); the PL120 tower gains only -0.390 / -1.789 (exp-t1). At RANDOM init PL60 is WORSE than PL120 on hr_raw (2.086 vs 1.687) and level on hr_peak (16.915 vs 16.791). So Phase A/B extracts more from the finer grid; the finer grid alone gives nothing. This is the exact opposite of what the same twin control says about momentary state on the same two towers (problem no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state), which is why the control has to be run per target and never inherited.

## Links
Rules out for problem [[is-the-frozen-llm-load-bearing-or-a-formatter]].

#readout #control #baseline #optihealth
