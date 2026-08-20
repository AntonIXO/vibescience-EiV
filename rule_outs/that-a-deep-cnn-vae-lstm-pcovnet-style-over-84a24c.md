---
created_at: '2026-08-20T22:03:18+00:00'
former_ids: []
id: that-a-deep-cnn-vae-lstm-pcovnet-style-over-84a24c
problem_id: oh-missing-earlywarning-rhythm
retracted: false
retraction_reason: ''
ruled_out_at: '2026-08-20T22:03:18+00:00'
topic_tags:
- anomaly-detection
- deep-learning
- optihealth
- n-of-1
- resting-hr
- illness-early-warning
---

## Claim
That a deep CNN-VAE/LSTM (PCovNet+ style) over resting-HR windows is the RIGHT WAY to add early-warning to the optihealth_analysis worker. ELIMINATED ON CONTRACT GROUNDS, not on accuracy grounds -- and the distinction matters. The paper's higher sensitivity is not disputed and is not tested here. What is eliminated is the claim that this is the right way TO ADD IT TO THE WORKER, because the worker's operating contract is classical / no-train / 900MB-RAM / 2s-poll, and a torch training loop violates every clause of it. The hypothesis's own Plan field says so verbatim: '(Not pursued -- violates the classical/no-train/900m-RAM worker contract.)'

NOT ELIMINATED, and explicitly still open: (a) deep anomaly detection for EiV or any other non-worker consumer, where no such contract applies; (b) the accuracy claim itself -- nobody here has compared PCovNet+ against the shipped CuSum detector on the same data; (c) a distilled or inference-only variant whose training happens off-box and which ships as frozen weights, which would satisfy the RAM clause but not the no-new-deps clause (torch alone is ~2GB).

## Method
Record audit plus a contract check, no compute spent -- deliberately, because the claim is eliminated by its own preregistration rather than by measurement. Three steps: (i) read the hypothesis's predicted_effects and compare each against its diagnostic's registered `direction` field (new_deps and runtime_sec are both lower_better; the hypothesis predicts `up` on both); (ii) read the hypothesis's own Plan field, which records the non-pursuit and the reason; (iii) confirm the sibling contract-satisfying alternative is CONFIRMED with new_deps=0 by reading exp rhr-cusum-realdata-2026-07's recorded diagnostics. Cross-checked against this session's exp-rar-circadian-supabase-20260820, which independently demonstrated a numpy-only rhythm battery on the same subject.

## Evidence
SELF-REFUTING BY ITS OWN RECORD, plus a measured contract violation on the two diagnostics it predicted.

1. THE HYPOTHESIS PREDICTS ITS OWN FAILURE. Its two predicted_effects are new_deps `up` ('torch + GPU training') and runtime_sec `up` ('training loop per run'). Both diagnostics are registered LOWER_BETTER. So the hypothesis preregistered that it would move both of its own metrics in the WRONG direction. There is no version of this run that produces a win; the record is a proposal to violate a constraint, not a testable claim about the world.

2. THE CONSTRAINT IS REAL AND THE SIBLING MET IT. `rhr-cusum-adds-earlywarning` (status CONFIRMED, exp rhr-cusum-realdata-2026-07) delivered early-warning on the same problem with new_deps = 0 and runtime inside the 900m/2s-poll budget: 2 episodes on real 365d data, corroboration 0.67, using only numpy/scipy/statsmodels/sklearn already in the venv. An alternative that satisfies the contract is already shipped and confirmed, so the deep variant would have to beat it by enough to justify breaking the contract -- a bar nobody has attempted to measure.

3. INDEPENDENT CONFIRMATION FROM THIS SESSION THAT THE CLASSICAL ROUTE HAS HEADROOM LEFT. exp-rar-circadian-supabase-20260820 computed the full RAR battery (IS/IV/M10/L5/RA + cosinor) on Anton's own 188 usable days with numpy ALONE -- new_deps 0 -- and found a strong rhythm (cosinor amplitude 15.84 bpm, R2 0.4186). The classical toolbox is not exhausted; the next gain on this problem is a better classical detector (the RAR power curve shows the detection floor is ~4 robust SD and a CuSum on RA would likely reach 2), not a neural network.

4. TARGET REPO ABSENT ANYWAY. /opt/optihealth_analysis does not exist on this host (verified 2026-08-20), so the worker-integration claim could not be executed here even if the contract permitted it.

## Links
Rules out for problem [[oh-missing-earlywarning-rhythm]].

#anomaly-detection #deep-learning #optihealth #n-of-1 #resting-hr #illness-early-warning
