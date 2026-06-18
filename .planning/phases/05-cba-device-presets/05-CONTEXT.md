# Phase 5: CBA Device Presets - Context

**Gathered:** 2026-06-18
**Status:** Ready for planning

<domain>
## Phase Boundary

Add three new preset entries to `rig.yaml` for the three CBA pedals:
- `rock-drive` under `brothers` — dual-channel boost+dist preset
- `chorus-shimmer` under `mood` — short-delay chorus-like shimmer preset
- `envelope-filter` under `billy-strings-wombtone` — static cocked-wah phaser preset

All three presets must be active (uncommented) and pass `rig validate`.

</domain>

<decisions>
## Implementation Decisions

### Brothers rock-drive (Ch1 boost + Ch2 DIST)

- **D-01:** Ch1 (BOOST type) targets a "Buddy Holly rhythm level" — a moderate boost noticeably louder and more driven than `transparent-lift` (which has gain_1: 13, volume_1: 83). Planner should push gain_1 to ~35-50 range and volume_1 above 83.
- **D-02:** Ch2 (DIST type, gain_2_type: 2) targets "Say It Ain't So solo territory" — high gain level (~85-95), aggressive and saturated.
- **D-03:** `dip_hi_gain_2: 127` — engaged on Ch2 to reach lead-sustain saturation. This is the defining difference from a rhythm DIST setting.
- **D-04:** Both channels active simultaneously (dual-channel rock preset). This is the dual-channel use case: Ch1 push → Ch2 DIST stack.

### Wombtone envelope-filter (cocked-wah phaser)

- **D-05:** `rate: 0` — frozen phase (static cocked-wah position). LOCKED from prior research.
- **D-06:** `feed: 96+` — high resonance for pronounced vowel/wah character. LOCKED from prior research.
- **D-07:** Depth: Full effect / near-max — high depth for a clearly audible phaser freeze. The cocked-wah character should be obvious and colored, not subtle.
- **D-08:** Mix: Moderate (~60-80 range, around noon). The filter effect is strong but the dry signal remains present — vowel quality without swallowing the guitar tone.

### Mood chorus-shimmer (short-delay path)

- **D-09:** `wet_channel: 1` — delay path for chorus-like shimmer. LOCKED from prior research.
- **D-10:** Density: Present chorus texture — moderate mix (~65-80), clock ~50-70. Audible and character-giving, not a subtle background shimmer. Think Smashing Pumpkins "Today" rhythm chorus.
- **D-11:** Routing: User wants shimmer on both live signal and loop output. Use `routing: 1` (out) which applies the wet effect to the full output, covering both the direct signal and loop content.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Config Schema
- `rig.yaml` — Primary config file; all three new presets are added here. Study existing preset structure (brothers, billy-strings-wombtone, mood) for field patterns.

### Requirements
- `.planning/REQUIREMENTS.md` — BROTHERS-01, MOOD-01, WOMBTONE-01 are the acceptance criteria for this phase.
- `.planning/ROADMAP.md` §Phase 5 — Success criteria (4 items) define what must be true.

### Prior Research (locked decisions)
- `.planning/STATE.md` §Accumulated Context / Decisions — Contains locked values: dipswitch encoding (0/127), Brothers gain_N_type encoding, Wombtone rate/feed semantics, Mood wet_channel mapping.

No external specs — all parameter decisions captured in decisions above.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `brothers.presets[transparent-lift]` in `rig.yaml` — Direct reference for Ch1 baseline (gain_1: 13, volume_1: 83). Rock-drive Ch1 must exceed this.
- `billy-strings-wombtone.presets[slow-vibe]` — Shows the Wombtone parameter set: feed, volume, mix, rate, depth, form, ramp, clock_division. Same fields apply to envelope-filter.
- `mood.presets[ambient-reverse-tape]` — Shows Mood parameter set: time, mix, length, wet_modify, clock, loop_clock_division, loop_modify, ramp_speed, wet_channel, routing, loop, dip_r_trails, dip_r_latch. Chorus-shimmer uses same schema.

### Established Patterns
- Preset structure: `id`, `name`, `preset_number`, `notes`, `parameters` (all presets follow this)
- Dipswitch encoding: 0=off, 127=on (never true/false/1) — hard rule
- Brothers gain_N_type: 0=boost, 1=od, 2=dist (0-indexed)
- Preset numbers are hardware recall slots — use the next available slot for new presets

### Integration Points
- New presets must be referenced by name in Phase 7's `rock` scene (brothers: rock-drive, mood: chorus-shimmer, billy-strings-wombtone: envelope-filter)
- `rig validate` cross-checks all preset IDs referenced in scenes — names must match exactly

</code_context>

<specifics>
## Specific Ideas

- **Musical references:** Weezer "Buddy Holly" for Brothers Ch1 push level; Weezer "Say It Ain't So" guitar solo for Brothers Ch2 DIST level.
- **envelope-filter character:** Not a slow sweep — a static wah "parked" in the sweet spot. High resonance creates the vowel peak. This is a texture device in the rock signal chain, not a performance effect.
- **chorus-shimmer character:** Think Smashing Pumpkins "Today" rhythm chorus — present and audible, adds depth without being a standalone chorus moment.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 5-CBA Device Presets*
*Context gathered: 2026-06-18*
