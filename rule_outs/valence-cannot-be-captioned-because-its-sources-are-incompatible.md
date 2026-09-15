---
created_at: '2026-09-01T08:46:29+00:00'
former_ids: []
id: valence-cannot-be-captioned-because-its-sources-are-incompatible
problem_id: phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-01T08:46:29+00:00'
topic_tags: []
---

## Claim
valence cannot be carried in the caption for the same reason stress_level cannot: its seven sources are on incompatible native scales (CASE 0.5-9.5, DAPPER/K-EmoCon 1-5, POPANE 0-10), so a clause quoting it would be a dataset indicator.

## Method
Read the declared per-dataset ranges from labels.yaml's top-level `sources` block (they are NOT on the channel entry), scaled each source's label events to [0,1] by its own declared span, and compared the resulting quantiles and medians across all seven sources. Window mass tabulated per source. No GPU.

## Evidence
REFUTED. Unlike stress_level, valence's sources DECLARE their spans in labels.yaml's `sources` block, and scaling each to its own declared range puts six of the seven on one axis (scratch/_valence_caption_feasibility.py):

  dataset      declared      p10     p50     p90    windows
  popane_s6   [0.0,10.0]   0.360   0.490   0.670       140
  case        [0.5, 9.5]   0.405   0.500   0.669        30
  dapper      [1.0, 5.0]   0.250   0.500   0.750       548
  k_emocon    [1.0, 5.0]   0.312   0.500   0.750        31
  popane_s3   [0.0,10.0]   0.370   0.505   0.765       137
  popane_s5   [0.0,10.0]   0.450   0.510   0.840       199
  popane_s7   [0.0,10.0]   0.390   0.651   0.980       213

Six medians span 0.490-0.510 -- a 0.020 spread across two labs, three instruments (a 9-point joystick, a 5-point scale, a 0-10 dial) and 1,085 windows. That is the 'shared units' assumption CLAUDE.md flags as needing a check, and it HOLDS for those six. Compare stress_level, whose pairwise Jaccard over native values was 0.032-0.033.

THE ONE OUTLIER IS THE ONE THE VAULT ALREADY NAMED. popane_s7 sits at median 0.651 against 0.490-0.510, and its p90 is 0.980 against 0.670-0.840. This is an independent confirmation of an existing record: CLAUDE.md documents that study 7's resting dial sits higher (6.23 vs 5.48/4.96) and spreads far wider between subjects (baseline sd 1.47 vs 0.31), that a baseline epoch already clears valence>=5.5 62% of the time against 24%/4%, and that this is exactly why study 7 supervises NO state despite being the best-designed POPANE study. The measurement here reproduces that from the label events alone, without touching the stimulus sheet.

SO THE ARM IS: scale_to_declared_range over the six aligned sources, popane_s7 EXCLUDED and the exclusion stated, giving 1,085 windows (84% of the channel's mass). This is the transform CLAUDE.md prescribes for a two-instrument axis with declared spans, and it is NOT the transform stress_level needs (within-subject rank), which is the distinction that file warns must not be gotten backwards.

CAVEAT CARRIED FORWARD: dapper alone is 548 of the 1,085 windows (51%), so any effect must be checked for being a dapper effect. And CLAUDE.md's CASE warning applies to the patch axis -- every CASE session is 41 minutes and fits inside one 120-minute patch -- so a per-patch clause would be near-constant for CASE regardless of the scaling.

## Links
Rules out for problem [[phase-c-is-trainable-only-for-json-slot-filling-the-product-surface-is-conversational-annotation-coaching]].
