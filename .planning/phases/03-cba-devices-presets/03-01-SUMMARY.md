---
phase: 03-cba-devices-presets
plan: 03-01
subsystem: config
tags: [cba, chase-bliss, mood-mkii, wombtone-mkii, brothers-am, rig-yaml]

# Dependency graph
requires:
  - phase: 02-schema-migration
    provides: single-file rig.yaml with devices, controls, presets
provides:
  - Billy Strings Wombtone device with 2 presets (slow-vibe, trem-pulse)
  - Brothers AM device with 1 preset (am-crunch)
  - Mood MkII controls block synced to authoritative catalog (45 controls)
  - All 4 Mood presets annotated with sonic intent
  - Signal chain: brothers → wombtone → hx-stomp → mood → mc6
affects: [04-real-scenes]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - preset parameter values are annotated with inline YAML comments for non-obvious settings
    - device entries include full control definitions from catalog.py
    - MIDI channels are sequential and non-overlapping

key-files:
  created: []
  modified:
    - rig.yaml

key-decisions:
  - "Device IDs: billy-strings-wombtone (from SCHEMA-01), brothers"
  - "MIDI channels: mood=2, wombtone=3, brothers=4"
  - "Signal chain: brothers (dirt) → wombtone (modulation) → hx-stomp (amp) → mood (ambient) → mc6 (control)"
  - "Preset annotations use inline YAML # comments + notes field"
  - "New devices not added to scenes yet — deferred to Phase 4"

patterns-established:
  - "Inline YAML comments (# ) document non-obvious parameter values"
  - "notes field on presets for high-level sonic intent"
  - "Controls blocks match catalog.py exactly in name, type, CC number"

requirements-completed:
  - PRESET-01
  - PRESET-02
  - PRESET-03
  - PRESET-04

# Metrics
duration: 15min
completed: 2026-06-09
---

# Phase 3: CBA Devices & Presets Summary

**Billy Strings Wombtone and Brothers AM devices added to rig.yaml with real presets; Mood MkII controls synced to catalog (45 controls) and all 4 presets annotated with sonic intent.**

## Performance

- **Duration:** 15min
- **Started:** 2026-06-09
- **Completed:** 2026-06-09
- **Tasks:** 4
- **Files modified:** 1 (rig.yaml)

## Accomplishments
- Mood MkII controls block synced from 34 to 45 entries matching `MOOD_MKII_CONTROLS` catalog exactly
- `micro_bypass` renamed to `wet_bypass` (CC 102), `loop_bypass` added (CC 103)
- All 4 Mood presets have inline `#` comments explaining sonic intent and `notes` fields
- Billy Strings Wombtone device added with 12 controls (from `WOMBTONE_MKII_CONTROLS`) and 2 presets (slow-vibe, trem-pulse)
- Brothers AM device added with 27 controls (from `BROTHERS_AM_CONTROLS`) and 1 preset (am-crunch)
- Signal chain: brothers → billy-strings-wombtone → hx-stomp → mood → mc6
- MIDI channels: mood=2, wombtone=3, brothers=4
- `rig validate` exits 0 reporting 5 pedals and 4 scenes

## Task Commits

Each task was committed atomically:

1. **Task 1: Sync Mood MkII controls to catalog and annotate presets** - `9a7cc59` (feat)
2. **Task 2: Validate rig.yaml after Mood sync** - (interleaved, no additional commit needed)
3. **Task 3: Insert Wombtone and Brothers into signal chain** - `ad3d8d7` (feat)
4. **Task 4: Final validation** - No changes needed; `rig validate` passed

## Files Modified
- `rig.yaml` - Added 2 devices (brothers, billy-strings-wombtone), synced Mood controls, annotated presets

## Decisions Made
- Device IDs match REQUIREMENTS.md naming conventions (`billy-strings-wombtone`, `brothers`)
- MIDI channels follow D-15: mood=2, wombtone=3, brothers=4
- Signal chain order follows D-12: dirt → modulation → amp → ambience → control
- New devices not added to scenes (D-14) — deferred to Phase 4

## Deviations from Plan

None - plan executed exactly as written. Note: the plan stated "51 entries" for the Mood MkII controls block, but the authoritative `MOOD_MKII_CONTROLS` catalog has 45 entries. The implementation matches the catalog (45), which is the correct source of truth.

## Issues Encountered
- None. `rig validate` passed on every run with no YAML or parameter errors.

## Next Phase Readiness
- All 5 devices declared with full control maps and playable presets
- Existing scenes continue to work (hx-stomp + mood only)
- Phase 4 can add Wombtone and Brothers to scenes with MC6 assignments

---
*Phase: 03-cba-devices-presets*
*Completed: 2026-06-09*
