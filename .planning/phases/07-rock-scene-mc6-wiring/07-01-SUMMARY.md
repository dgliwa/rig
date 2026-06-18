---
phase: 07-rock-scene-mc6-wiring
plan: 01
subsystem: config
tags: [rig-yaml, mc6, scene, rock]

requires:
  - phase: 06-hx-stomp-preset-library
    provides: mesa-rock preset entry that rock scene references

provides:
  - rock scene in mc6.config.scenes with all four device-preset pairs
  - MC6 Bank 1 Switch B wired to scene: rock
  - 2-scene rig.yaml validated clean

affects: [future-scene-phases, mc6-bank-expansion]

tech-stack:
  added: []
  patterns: [scene-switch-wiring pattern — scene key in mc6.config.scenes mirrored to switch assignment in mc6.config.banks]

key-files:
  created: []
  modified: [rig.yaml]

key-decisions:
  - "Used billy-strings-wombtone (full device id) not wombtone as the device key in the scene presets"
  - "Left commented-out scenes (boogie, wacky, marshall) and switches (C, D) untouched per D-03"

patterns-established:
  - "Scene block: 8-space key, 10-space description/presets, 12-space device-preset pairs"
  - "Switch assignment: 12-space key, 14-space scene value — mirrors existing Switch A pattern"

requirements-completed: [SCENE-01, SCENE-02, SCENE-03]

duration: 5min
completed: 2026-06-18
---

# Phase 7: rock-scene-mc6-wiring Summary

**`rock` scene live in rig.yaml — mc6 Switch B activates Brothers distortion + Wombtone wah + mesa-rock amp + Mood chorus via a single scene press**

## Performance

- **Duration:** 5 min
- **Started:** 2026-06-18T00:00:00Z
- **Completed:** 2026-06-18T00:05:00Z
- **Tasks:** 3
- **Files modified:** 1

## Accomplishments
- Added `rock` scene to `mc6.config.scenes` with description and all four device-preset pairs (mesa-rock, envelope-filter, rock-drive, chorus-shimmer)
- Activated MC6 Bank 1 Switch B with `scene: rock` mapping (previously commented out as crunch placeholder)
- `rig validate` reports 2 scenes with all cross-references valid; exit code 0

## Task Commits

1. **Task 1+2+3: Add rock scene and wire Switch B** — `ef31940` (feat)

## Files Created/Modified
- `rig.yaml` — added 7-line `rock` scene block + activated Switch B (9 insertions, 2 deletions)

## Decisions Made
- All four device-preset pairs confirmed present before insertion (rock-drive, envelope-filter, mesa-rock, chorus-shimmer each verified in rig.yaml)
- Used full device id `billy-strings-wombtone` consistent with clean scene pattern

## Deviations from Plan

None — plan executed exactly as written.

Minor note: The plan's indentation spec for Switch B (12 spaces) matched the actual file structure confirmed at line 344. The initial edit attempt used wrong indentation from memory; corrected after grep confirmation. No impact on output.

## Issues Encountered
None — one quick grep to verify exact indentation before the Switch B edit. Both edits applied cleanly on first attempt after verification.

## User Setup Required
None - no external service configuration required.

## Next Phase Readiness
- Rock signal chain complete; all four devices activate on a single MC6 switch press
- Two scenes (clean, rock) are live and validated
- Additional scenes (boogie, wacky, marshall) remain commented in rig.yaml, ready to be activated in future phases using the same scene-switch-wiring pattern

---
*Phase: 07-rock-scene-mc6-wiring*
*Completed: 2026-06-18*
