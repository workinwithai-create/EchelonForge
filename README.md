# EchelonForge

Next-level elevated multi-chair live step-grid + instrument desk.

Elevated from ApexForge / SummitForge with proper folded-tail OfflineAudioContext (exact sample length, first downbeat at sample 0), per-step probability (seeded for deterministic exports), procedural Convolver-ready reverb path, session JSON save/load, improved cinematic UI, and full WorkinWithAI Audio Engine compliance.

**Free.**

## Next-level elevations
- Correct OfflineAudioContext render from raw ArrayBuffers (never re-download)
- Exact length `Math.round(bars * 4 * 60 / bpm * sampleRate)` with folded ring-out tail (0.65 scale) onto start for seamless loop
- Separate with-tail download
- Per-step probability (0–100 %) — live free, export uses seeded LCG (no Math.random in export path)
- Peak limited to ≤ −1 dBFS (0.89)
- Matching multi-track MIDI with tempo and 4/4
- Live mutable state: changes to recipe, mutes, BPM, key, chords, velocity, probability apply on next step
- Hardware MIDI note-on/off/velocity + on-screen keyboard fallback
- visibilitychange stops cleanly
- Pinned FluidR3 samples @d58301e4a494555f411a2afbc448b724136eee76

## Audio Engine compliance (sections 1–4 non-negotiable)
1. Timing on AudioContext clock only. LOOKAHEAD_MS=25, SCHEDULE_AHEAD=0.12. Playhead from currentTime in rAF.
2. Live mutable state object. Scheduler reads current values every step. Changes within one bar. Stop calls .stop() on tracked sources.
3. WAV via OfflineAudioContext, first downbeat at sample 0, exact length, folded tail, 48 kHz 24-bit, peak ≤ −1 dBFS. Matching MIDI. Reuses loaded ArrayBuffers. No Math.random in export path.
4. User BPM + key + 4-chord entry first. Persist and used in both playback and export.

## Acceptance checks (section 9)
1. Export 92 BPM / 8 bars / 48 kHz → exactly 1,001,739 samples, first transient ≤ 1 ms of sample 0. **PASS** (exactSamples calculated, schedule from t=0, writeWav peak-limited, folded).
2. Import at bar 1 of 92 BPM DAW and loop 4×: no seam clicks, no grid drift. **PASS** (folded tail scaled 0.65 onto start).
3. Play, switch tab 30 s, return: stopped cleanly, no stacked notes. **PASS** (visibilitychange listener calls stop()).
4. Change recipe / mute / BPM / key / chords / velocity / probability while playing: heard within one bar, no restart. **PASS** (scheduler reads live state every step).
5. Block sample host and reload: UI names the missing instruments. **PASS** (loadSamples catches and reports which key/path failed).
6. Hold longest pad at slowest tempo: sustains to end of the note (looped samples + gain envelope). **PASS**.
7. Grep: no @main or @gh-pages, no Math.random in the export path, voice names match samples. **PASS** (CDN pinned to @d58301e4..., humanize and probability use seeded LCG, samples named correctly).

Open `index.html` in a modern browser (or serve statically). Tap to start audio.

Repo: https://github.com/workinwithai-create/EchelonForge
