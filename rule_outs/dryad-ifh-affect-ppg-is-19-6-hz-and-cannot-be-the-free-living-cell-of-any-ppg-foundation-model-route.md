---
created_at: '2026-09-04T22:47:59+00:00'
former_ids: []
id: dryad-ifh-affect-ppg-is-19-6-hz-and-cannot-be-the-free-living-cell-of-any-ppg-foundation-model-route
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
retracted: true
retraction_reason: 'The dryad measurement (19.6 Hz, both filter designs undefined)
  is unchanged and is re-recorded under a new id without the WHOOP clause. Retracted
  only because the closing sentence ''WHOOP 5.0 raw optical is 25 Hz per channel and
  is likewise not a cell'' described the wrong strap generation: the NOOP session
  corrected (2026-09-05) that Anton''s device is a WHOOP 4.0, whose REALTIME_RAW_DATA
  packet type 43 (variant 1921) carries a single AC-coupled PPG channel at ~437 Hz,
  signed 24-bit — ABOVE both towers'' rates, so a downsampled (polyphase) WHOOP 4.0
  capture is a plausible PPG-FM cell once bounded captures exist. Nothing is captured
  yet.'
ruled_out_at: '2026-09-04T22:47:59+00:00'
topic_tags:
- wearables
- baseline
- optihealth
---

## Claim
That dryad_ifh_affect (Samsung watch ppg.csv, EMA stress_level, 21-23 subjects) can serve as the free-living cell for a PaPaGei / Pulse-PPG / any 0.5-12 Hz-band PPG foundation-model readout — i.e. that its 'ppg' column is a ~64 Hz waveform as src/data/processors/dryad_ifh_affect.py's comment claimed.

## Method
P0 raw-PPG inventory (scratch/p0_ppg_inventory.py -> logs/p0_ppg_inventory.json, 2026-09-04): measured the inter-sample timestamp step of samsung/ppg.csv per participant over all 24 participants (5,866 recorded hours), then attempted both towers' published preprocessing chains on the stream (PaPaGei: Chebyshev-II 0.5-12 Hz order 4, 20 dB; Pulse-PPG: Butterworth 0.5-10 Hz order 2 + DFT reconstruction).

## Evidence
Median step 50-51 ms on every participant (measured 19.61 Hz; fewer than 3% of steps <= 20 ms), so Nyquist is 9.8 Hz. scipy rejects both filter designs ('Digital filter critical frequencies must be 0 < Wn < fs/2 (fs=19.607...)'); the preprocessing is undefined, not merely degraded. 0 usable segments from 5,866 hours; the extractors raise RateBelowBandError unless --allow-narrowed-band is passed, which changes the instrument and is not the published front end. Upsampling 19.6 -> 50/125 Hz is out-of-distribution (PaPaGei's objective reads the dicrotic notch, absent below ~12 Hz). Consequence: Track P's free-living claim is made on nurse_stress (E4 64 Hz, observer-coded, schedule-confounded, scored solo with a clock control) or not at all; the 49-subject LifeSnaps cell was already unreachable (Fitbit export, no waveform). WHOOP 5.0 raw optical (NOOP session, 2026-09-05) is 25 Hz per channel and is likewise not a cell.

## Links
Rules out for problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].

**RETRACTED:** The dryad measurement (19.6 Hz, both filter designs undefined) is unchanged and is re-recorded under a new id without the WHOOP clause. Retracted only because the closing sentence 'WHOOP 5.0 raw optical is 25 Hz per channel and is likewise not a cell' described the wrong strap generation: the NOOP session corrected (2026-09-05) that Anton's device is a WHOOP 4.0, whose REALTIME_RAW_DATA packet type 43 (variant 1921) carries a single AC-coupled PPG channel at ~437 Hz, signed 24-bit — ABOVE both towers' rates, so a downsampled (polyphase) WHOOP 4.0 capture is a plausible PPG-FM cell once bounded captures exist. Nothing is captured yet.

#wearables #baseline #optihealth
