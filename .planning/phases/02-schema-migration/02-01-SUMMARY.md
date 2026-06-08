---
phase: "02"
plan: "02-01"
status: complete
completed: "2026-06-08"
commit: f453ff2
---

# Plan 02-01: Migrate to single-file rig.yaml — COMPLETE

## What Was Built

Collapsed the multi-file rig config into a single authoritative `rig.yaml`. All device definitions (hx-stomp, mood, mc6) are now inline in signal-chain order, all 34 Mood MkII MIDI controls are present, and all 4 scenes live under `mc6.config.scenes`. Legacy files deleted.

## Must-Haves Verification

| Truth | Result |
|-------|--------|
| `rig validate` reports 3 devices and 4 scenes | ✅ "3 pedals, 4 scenes" |
| hx-stomp, mood, mc6 in signal-chain order | ✅ |
| All 4 scenes under mc6.config.scenes | ✅ clean/crunch/lead/wacky |
| mood.config.controls has 34 entries | ✅ `grep -c "midi_cc:" rig.yaml` = 34 |
| Legacy files deleted | ✅ signal-chain.yaml, devices/*.yaml, scenes/*.yaml |
| devices/ and scenes/ directories removed | ✅ |

## Requirements Satisfied

- **SCHEMA-01**: `devices:` list with hx-stomp, mood, mc6 and all presets inline
- **SCHEMA-02**: Scenes in `mc6.config.scenes`, not separate files
- **SCHEMA-03**: All legacy files and directories deleted
- **SCHEMA-04**: `rig validate` reports 3 pedals and 4 scenes

## Decisions Honored

- D-02: Signal-chain order (hx-stomp → mood → mc6)
- D-03: Scenes nested under mc6.config.scenes
- D-04: Legacy files deleted after validation passed
- D-05: Full MOOD_MKII_CONTROLS block (34 entries)
- D-07: Config type strings match registry (midi / chase_bliss / controller)

## Output

- Modified: `rig.yaml` (351 lines added, single-file format)
- Deleted: `signal-chain.yaml`, `devices/hx-stomp.yaml`, `devices/mood.yaml`, `devices/mc6.yaml`, `scenes/clean.yaml`, `scenes/crunch.yaml`, `scenes/lead.yaml`, `scenes/wacky.yaml`
- Commit: f453ff2
