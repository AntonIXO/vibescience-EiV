---
created_at: '2026-08-30T07:29:39+00:00'
former_ids: []
id: that-a-declaration-based-registry-in-this-repo-bd79da
problem_id: new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-30T07:29:39+00:00'
topic_tags:
- baseline
- denominator
- wearables
---

## Claim
That a declaration-based registry in this repo will make its own omissions visible. It will not: `configs/canonical/labels.yaml`, `configs/canonical/privileged.yaml`, the projector's `patch_pool_mode`/`patch_presence` pair, and every Phase-A/B/C `dataset_filter` all DEGRADE SILENTLY when a corpus grows underneath them. None raises, none warns, and each produces a plausible-looking number computed over a fraction of the data the manifest advertises.

## Method
Cross-cutting audit performed while preparing the v11 full-corpus retrain, 2026-08-30. Per-mechanism runners, each counting REACH rather than reading the registry: scratch/_valence_reachability.py (parquet rows vs windows with a non-empty target mask, per corpus), scratch/_undeclared_eda_census.py (privileged epochs per undeclared dataset, with measured medians/percentiles), scratch/_phasec_coverage_audit.py and scratch/_phasec_supervision_audit.py (per-profile window/label-epoch/EDA reach and per-channel zero-reach detection against the live bundle), scratch/_v11_reach_check.py (old vs new roster, side by side). W10's instance was measured earlier by torch.equal on the two pooling paths.

## Evidence
FOUR INSTANCES, ALL MEASURED, ALL IN THE 2026-08-28..30 window, on three different mechanisms:

(1) labels.yaml -- popane_s5/s6 had no source block, so `declared_range()` returned None -> `scale_to_declared_range()` None -> `affect_patch_targets()` an all-zero mask. 10,232 valence epochs sat in label_events.parquet invisible to training. Measured 0/40 sampled windows carrying any affect target BEFORE the fix, 40/40 after. Had exp-a2-affect-resupply not checked reachability first, it would have measured the OLD supply while reporting the new one.

(2) privileged.yaml -- dapper, k_emocon, popane_s5, popane_s6 all ship EDA and none were declared: 18,783 privileged epochs from undeclared datasets, with dapper alone ~63% of the corpus's entire EDA supply. This one WAS caught, by tests/test_privileged_channels.py::test_metadata_loads_the_events, which is the append-only registry's only enforcement.

(3) patch_pool_mode / patch_presence (W10) -- a masked_avg bundle loaded back as 'avg' and no inference site passed patch_presence; the projector absorbed it via a fallback to plain adaptive_avg_pool2d (torch.equal True, max abs diff 0.0). The W6 arms were TRAINED masked and SERVED avg for weeks.

(4) dataset_filter -- the designated baseline C profile `lora_proxy_12ch_wl_band04` admits 7,592 of 14,092 windows (53.9%) and 44.2% of label-epochs on the synced bundle, with SIX label channels at EXACTLY ZERO reach: valence 0/40,893, arousal 0/6,457, protocol_condition 0/10,650, exam_session_code 0/7,420, stress_raw 0/4,373, stimulus_class 0/1,230. Privileged EDA reaches 33.5%. Its roster predates POPANE s5/s6/s7, DAPPER and K-EmoCon entirely -- so every fresh-chain failure recorded on problem `new-corpus-...-healthy-on-the-same-draw` was confounded by a C stage training on roughly the OLD half of the corpus.

COMMON SHAPE: a name absent from a declaration is indistinguishable, downstream, from data that does not exist. The artifacts all still load, the runs all still exit 0, and the resulting numbers are confidently wrong about a supply that was never present.

THE ONLY THING THAT EVER CAUGHT ONE AUTOMATICALLY was (2)'s dedicated test. (1), (3) and (4) were each found by a human/agent deciding to count rows before trusting a number.

STANDING RULE THIS EARNS: after any corpus rebuild, count parquet rows AND windows with a non-empty target mask, per newly-added corpus, and require the second number to be non-zero -- before measuring anything with those labels. For a profile, print its realised window/label-epoch reach before quoting any result it produces.

## Links
Rules out for problem [[new-corpus-12-547-0-0-phase-a-b-encoders-produce-deployment-broken-phase-c-adapters-old-corpus-encoder-healthy-on-the-same-draw]].

#baseline #denominator #wearables
