# Phase 4: Real Scenes - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-06-09
**Phase:** 4-Real Scenes
**Areas discussed:** Scene device mapping, MC6 switch ordering, Scene descriptions, Scene rename/restructure, Mood preset revisit

---

## Scene device mapping

| Option | Description | Selected |
|--------|-------------|----------|
| Brothers in crunch | Brothers am-crunch alongside Marshall | ✓ |
| Brothers in lead | Brothers as boost into Boogie high-gain | ✓ |
| Brothers in clean | Brothers as subtle push into Princeton | ✓ |
| Wombtone: slow-vibe in clean only | Subtle phaser/vibe on clean Fender | |
| Wombtone: trem-pulse in wacky only | Rhythmic tremolo behind Doogie/BOC lead | |
| Wombtone: both scenes | slow-vibe in clean, trem-pulse in wacky | ✓ |

**User's choice:** Brothers in crunch, lead, AND clean; Wombtone slow-vibe in clean, trem-pulse in wacky.

**Follow-up on clean scene (Brothers am-crunch there):**

| Option | Selected |
|--------|----------|
| am-crunch is fine — "clean" means HX tone, Brothers adds optional grit | ✓ |
| Add a clean boost preset to Brothers | |
| No Brothers in clean | |

**Notes:** am-crunch in clean is intentional. Brothers footswitch controls whether it's in the signal path. Wombtone also in clean — signal chain (brothers → wombtone) means when both are engaged, Brothers drives Wombtone ("DIRT FIRST" from the doc).

---

## MC6 switch ordering

| Option | Selected |
|--------|----------|
| Keep A→clean, B→crunch, C→lead, D→wacky | ✓ |
| Reorder to performance workflow | |

**Bank name:**

| Option | Selected |
|--------|----------|
| Keep 'Main Rig' | ✓ |
| Rename it | |

**User's choice:** No changes to switch ordering or bank name.

---

## Scene descriptions

| Option | Selected |
|--------|----------|
| Keep current style, update for new devices | ✓ |
| Name the musical moment (verse/chorus/etc.) | |

**Proposed descriptions (approved):**
- clean: "Clean Fender with phaser vibe and doubling"
- crunch: "British crunch with Brothers OD and ambient pad"
- lead: "High gain Boogie lead with Brothers boost and broken tape"
- wacky: "BOC saturated lead with tremolo and Sigur Rós drone"

---

## Scene rename/restructure

| Option | Selected |
|--------|----------|
| Keep clean, crunch, lead, wacky | ✓ |
| Rename one or more scenes | |

**User's choice:** No renames.

---

## Mood preset revisit (user-initiated mid-discussion)

User explicitly requested revisiting all Mood presets against `~/Public/Drop Box/mood-mkii-presets.md`.

| Option | Selected |
|--------|----------|
| Parameter audit only — keep names/intent, fix values | |
| Full rewrite from the doc recipes | ✓ |
| Add new presets alongside existing ones | |

**User's choice:** Full rewrite from the official doc recipes. Planner decides which recipe maps to each scene based on musical fit. Existing preset IDs (sigur-ros-drone, ambient-pad-machine, broken-tape-memories, second-guitarist) are replaced.

---

## Claude's Discretion

- Which specific Mood doc recipes map to each scene context — user delegated to planner ("Let the planner decide based on musical fit").
- Clock-position to MIDI value translation for Mood parameters.

## Deferred Ideas

- Envelope-routing presets for Wombtone — deferred until live testing confirms value
- More Brothers/Wombtone presets beyond existing 3 — future phase
- MC6 JSON generation — v1.3+
