---
created_at: '2026-09-04T22:51:33+00:00'
former_ids: []
id: dryad-ifh-affect-samsung-ppg-is-19-6-hz-so-it-cannot-be-a-ppg-foundation-model-free-living-cell
problem_id: no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state
retracted: false
retraction_reason: ''
ruled_out_at: '2026-09-04T22:51:33+00:00'
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
Median step 50-51 ms on every participant (measured 19.61 Hz; fewer than 3% of steps <= 20 ms), so Nyquist is 9.8 Hz. scipy rejects both filter designs ('Digital filter critical frequencies must be 0 < Wn < fs/2 (fs=19.607...)'); the preprocessing is undefined, not merely degraded. 0 usable segments from 5,866 hours; the extractors raise RateBelowBandError unless --allow-narrowed-band is passed, which changes the instrument and is not the published front end. Upsampling 19.6 -> 50/125 Hz is out-of-distribution (PaPaGei's objective reads the dicrotic notch, absent below ~12 Hz). Consequence: Track P's free-living claim is made on nurse_stress (E4 64 Hz, observer-coded, schedule-confounded, scored solo with a clock control) or not at all; the 49-subject LifeSnaps cell was already unreachable (Fitbit export, no waveform). NOT covered by this rule-out: WHOOP 4.0 REALTIME_RAW_DATA (~437 Hz single AC-coupled channel, per the NOOP session 2026-09-05) — above both towers' rates, a plausible future cell; no capture exists yet.

## Links
Rules out for problem [[no-trained-representation-has-ever-beaten-its-own-random-twin-on-momentary-state]].

#wearables #baseline #optihealth
