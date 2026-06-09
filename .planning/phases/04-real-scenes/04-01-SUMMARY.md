---
phase: 04-real-scenes
plan: 04-01
subsystem: config
tags: [scenes, mood-mkii, wombtone, brothers, rig-yaml, mc6]

# Dependency graph
requires:
  - phase: 03-cba-devices-presets
    provides: Billy Strings Wombtone, Brothers AM, and Mood MkII devices with presets
provides:
  - 4 real scenes with complete device-to-preset assignments
  - Mood MkII presets rewritten from official doc recipes (Tier 1 and Tier 2)
  - Wombtone and Brothers wired into scenes where musically appropriate
  - Scene descriptions matching D-21 exactly
  - MC6 Bank 1 switch order confirmed: A=clean, B=crunch, C=lead, D=wacky
affects: []

# Tech tracking
tech-stack:
  added: []
  patterns:
    - Mood presets are derived from named official doc recipes (not re-derived)
    - Clock position to MIDI: 7 o'clock ≈ 0, noon ≈ 64, 5 o'clock ≈ 127
    - Scene presets reference existing device preset IDs atomically

key-files:
  created: []
  modified:
    - rig.yaml

key-decisions:
  - "clean scene: princeton + slow-vibe + am-crunch + liquid-ambient-wash"
  - "crunch scene: marshall + am-crunch + dub-tape-delay"
  - "lead scene: boogie-pb + am-crunch + granular-bloop-cloud"
  - "wacky scene: doogie-pb + trem-pulse + reverse-stretch-drift"
  - "Mood preset IDs: liquid-ambient-wash, dub-tape-delay, granular-bloop-cloud, reverse-stretch-drift"
  - "Brothers in clean scene is intentional — optional grit footswitch, not always-on"
  - "Wombtone not in crunch or lead — Brothers OD drives those scenes"

requirements-completed:
  - SCENE-01
  - SCENE-02
  - SCENE-03

# Metrics
duration: 20min
completed: 2026-06-09
---

# Phase 4 Plan 01: Build Real Scenes Summary

**4 production-ready scenes fully wired with all devices; all Mood MkII presets rewritten from official doc recipes (Tier 1: ①a, ②, ④; Tier 2: ⑦).**

## Performance

- **Duration:** 20min
- **Started:** 2026-06-09
- **Completed:** 2026-06-09
- **Tasks:** 1 (single atomic file change)
- **Files modified:** 1 (rig.yaml)

## Accomplishments

### Mood Preset Rewrite (D-19)

All 4 Mood presets replaced with official doc recipes:

| New ID | Name | Doc Recipe | Musical Role |
|--------|------|-----------|-------------|
| `liquid-ambient-wash` | Liquid Ambient Wash | ①a Frozen pad, dry over top | Clean scene — subtle reverb pad under Fender tone; LATCH + TRAILS for hands-free freeze |
| `dub-tape-delay` | Dub Tape Delay | ② Dub Tape Delay | Crunch scene — rhythmic repeats with high feedback; roll CLOCK live for dub pitch-drops |
| `granular-bloop-cloud` | Granular Bloop Cloud | ④ Granular Bloop Cloud (Slip) | Lead scene — Slip mode shatters notes into re-pitched grains; freeze for grain cloud solo |
| `reverse-stretch-drift` | Reverse-Stretch Drift Cloud | ⑦ Reverse-Stretch Drift Cloud (Tier 2) | Wacky scene — chord becomes reversed time-stretched cloud; BOUNCE + CLOCK ramp = self-generating drone |

### Preset Recipe Selection Rationale

- **Clean (①a):** A frozen reverb pad is the ideal companion for a clean Princeton tone — the LATCH dip keeps the freeze hands-free and TRAILS lets it fade smoothly on bypass. MIX at 11 o'clock (48) keeps it subtle, never drowning the clean signal. Clock at 64k keeps the reverb pristine.
- **Crunch (②):** Dub Tape Delay gives rhythmic texture that complements mid-gain crunch without competing with the amp drive. High feedback (MODIFY at 2-3 o'clock) lets the delay build into washy repeats; rolling CLOCK down live creates the "bloop" pitch-drop move. Keeps the rhythm alive under crunch.
- **Lead (④):** Slip granular mode adds an aggressive, gritty texture that sits well under high-gain Boogie lead — grains shatter single notes into scrambled clouds. Clock at ~16-32k introduces slight aliasing that pairs with the gain crunch. Hold left footswitch to freeze a grain cloud and solo over it.
- **Wacky (⑦):** Tier 2 reverse-stretch territory. Loop MODE=Stretch with small slice size + reversed MODIFY creates a smeared, reversed, pitch-stretched chord cloud. BOUNCE + CLOCK ramp dips turn it into a self-generating drone that rises and falls. This matches the BOC saturated lead + tremolo Wombtone scene — all textures, no clean ground.

### Clock Position Translations Used

| Clock Position | MIDI Value | Usage |
|---------------|-----------|-------|
| 2–3 o'clock | 95 | TIME (long reverb decay) |
| 1 o'clock | 80 | MODIFY (medium smear) |
| 11 o'clock | 48 | MIX (subtle wet blend) |
| 12 o'clock (noon) | 64 | Neutral positions |
| 10–11 o'clock | 45 | TIME (Slip fast refresh) |
| ~24–32k | 50 | CLOCK (tape character) |
| 64k (fully CW) | 127 | CLOCK (clean/hi-fi) |

### Scene Updates (D-16, D-17, D-18)

- `clean`: princeton + slow-vibe + am-crunch + liquid-ambient-wash — Brothers present for optional footswitch grit (D-17), Wombtone slow-vibe for phaser character
- `crunch`: marshall + am-crunch + dub-tape-delay — Wombtone excluded (Brothers drives this scene, D-18)
- `lead`: boogie-pb + am-crunch + granular-bloop-cloud — Wombtone excluded (D-18)
- `wacky`: doogie-pb + trem-pulse + reverse-stretch-drift — Wombtone trem-pulse for tremolo texture, no Brothers

### Scene Descriptions (D-21)

All match exactly:
- `clean`: "Clean Fender with phaser vibe and doubling"
- `crunch`: "British crunch with Brothers OD and ambient pad"
- `lead`: "High gain Boogie lead with Brothers boost and broken tape"
- `wacky`: "BOC saturated lead with tremolo and Sigur Rós drone"

### MC6 Bank 1 Switch Order (D-20)

Confirmed unchanged: A=clean, B=crunch, C=lead, D=wacky.

## Task Commit

- `096d444` — `feat(config): build real scenes with all devices and rewritten Mood presets`

## Files Modified

- `rig.yaml` — Replaced 4 Mood presets (sigur-ros-drone, ambient-pad-machine, broken-tape-memories, second-guitarist) with recipe-based presets; updated all 4 scenes with complete device assignments and new descriptions

## Decisions Made

- Mood preset IDs changed from old arbitrary names to recipe-anchored names matching the doc (liquid-ambient-wash, dub-tape-delay, granular-bloop-cloud, reverse-stretch-drift) — old IDs removed atomically with scene reference updates
- Brothers in clean scene is intentional (D-17): "clean" refers to the amp tone, not signal chain bypass
- Wombtone excluded from crunch and lead (D-18): Brothers OD is the primary texture driver in those scenes
- wacky description retains "Sigur Rós drone" as the semantic anchor even though the drone comes from Mood's reverse-stretch recipe, not a literal Sigur Rós preset name

## Deviations from Plan

None — plan executed exactly as written. The single file change (rig.yaml) covered all 4 tasks atomically. `rig validate` passed on first run.

## Known Stubs

None. All presets are derived from real named doc recipes with concrete parameter values. No placeholder text or TODO markers exist.

## Threat Flags

None. No new network endpoints, auth paths, or trust boundary changes. Config-only change.

## Self-Check: PASSED

- `rig.yaml` exists and modified: FOUND
- Commit `096d444` exists: FOUND
- `rig validate` exits 0: PASSED (5 pedals, 4 scenes, all cross-references valid)
- All 4 old Mood preset IDs removed: CONFIRMED (sigur-ros-drone, ambient-pad-machine, broken-tape-memories, second-guitarist replaced)
- All 4 new Mood preset IDs present: CONFIRMED (liquid-ambient-wash, dub-tape-delay, granular-bloop-cloud, reverse-stretch-drift)
- Scene descriptions match D-21: CONFIRMED
- MC6 switch order A=clean, B=crunch, C=lead, D=wacky: CONFIRMED

---
*Phase: 04-real-scenes*
*Completed: 2026-06-09*
