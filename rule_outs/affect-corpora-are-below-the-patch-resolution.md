---
created_at: '2026-09-01T10:26:00+00:00'
former_ids: []
id: affect-corpora-are-below-the-patch-resolution
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T10:26:00+00:00'
topic_tags: []
---

## Claim
The affect corpora cannot supervise the state axis because their inductions are 40-120 second clips inside a 1,440-minute window -- the label is finer than anything the architecture can address, so it is a resolution limit rather than a reduction choice.

## Method
Computed, per dataset, the annotated-epoch span per window and the number of distinct states falling inside one 120-minute patch, then read raw per-epoch state series for CASE and POPANE to confirm the reduction rather than the resolution is what removes the signal.

## Evidence
HALF RIGHT, AND THE HALF I GOT WRONG IS THE ACTIONABLE HALF (scratch/_patch_resolution_vs_events.py). The shipped resolution is patch_len 120 min / stride 60, so the finest addressable unit is 120 minutes -- but the events are NOT below it, because the affect sessions are short and their annotations are dense.

MEASURED, per dataset: window count, span of annotated epochs, windows carrying >1 state, and windows where two states share ONE 120-minute patch:

  dataset                    wins  span p50  >1 state/win  >1 state/PATCH
  case                         30        41            26              26
  consumer_grade_wearables     35        15            35              35
  neuro_stress_resilience_hci  35        30            26              26
  popane_s5                   199        27           103             103
  popane_s6                   140        34            81              81
  popane_s4                    83        17            43              43
  wesad                        15        93            15              15
  nurse_stress                102       100            24              12
  lifesnaps                  2070       489           899               4
  dryad_ifh_affect           3564         1           381               7
  pmdata                     1665         1             1               0
  wearable_exam_stress         30       210             0               0

TEN of twelve datasets have windows where TWO DISTINCT STATES SHARE A SINGLE PATCH. For CASE, consumer_grade, neuro_stress, popane_s4/s5/s6 and wesad the multi-state count at patch level EQUALS the count at window level -- every window that carries a transition carries it inside one patch, because the whole session is 15-41 minutes and fits in one 120-minute unit.

SO THE SIGNAL IS NOT BELOW THE PATCH. It is INSIDE the patch, and confirmed on raw epochs: a CASE window reads {neutral 33, flow 3, stress 5} over 41 annotated epochs and infer_state_label returns `neutral`; popane_s5 reads {neutral 26, stress 1} -> `neutral`; {neutral 23, flow 3} -> `neutral`. The minority states are real, measured, per-epoch, and the MAJORITY REDUCTION discards them.

WHAT THIS CORRECTS: my previous entry said the affect corpora 'reduce to neutral' because the induction is shorter than the window's majority, and framed that as an architecture resolution limit. The resolution is not the binding constraint -- the REDUCER is. A 41-minute CASE session sits inside one patch, so patch-level state supervision would inherit exactly the same majority problem, which is why state_patch_weight would not rescue it either.

WHAT WOULD: the soft/distributional target that already exists. CLAUDE.md documents state_target: hard | soft | ordinal, where `soft` is cross-entropy against the EMPIRICAL PER-EPOCH DISTRIBUTION rather than the argmax. {neutral 33, flow 3, stress 5} is a different target from one-hot neutral, and state_distribution() already computes it. That is a profile key, not new code.

TWO datasets are genuinely unreachable on the state axis and should be named: pmdata (1,665 windows, span 1 epoch, ZERO windows with two states -- one EMA answer per day, nothing to distribute) and wearable_exam_stress (30 windows, span 210, zero multi-state windows). For those the label really is constant over every addressable unit.

I AM NOT QUEUEING AN ARM ON THIS. state_target: soft is a recipe change and the seed family is mid-flight; it needs its own one-variable arm against the incumbent, after the family closes.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
