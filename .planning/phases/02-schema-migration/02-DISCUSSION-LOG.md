# Phase 2: Schema Migration - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-06-08
**Phase:** 2-Schema Migration
**Areas discussed:** Devices scope, Mood controls: block, Mood preset parameter names

---

## Devices Scope

| Option | Description | Selected |
|--------|-------------|----------|
| Migrate 3 existing devices only | Phase 2 = exact migration. Wombtone + Brothers get added in Phase 3 when their presets are defined. | ✓ |
| Stub all 5 devices now | Add Wombtone + Brothers as empty device entries, Phase 3 fills in presets. Satisfies SCHEMA-01 in Phase 2. | |
| 3 existing + minimal stubs (id/type only) | Add Wombtone + Brothers with just enough to register them, Phase 3 fills out config and presets. | |

**User's choice:** Migrate 3 existing devices only
**Notes:** SCHEMA-01 references all 5 devices but that requirement is fully satisfied when Phase 3 adds the new CBA devices. Phase 2 is a clean migration of what exists.

---

## Mood `controls:` Block

| Option | Description | Selected |
|--------|-------------|----------|
| Omit it | Keep rig.yaml lean. controls block is reference info; rig validate doesn't require it. | |
| Include full controls block | Copy the full MIDI CC control map from MOOD_MKII_CONTROLS catalog. Needed for rig apply to send CC parameters. | ✓ |
| Include only if rig validate requires it | Check whether rig-cli requires controls: for validation; include only if needed. | |

**User's choice:** Include full controls block
**Notes:** `ChaseBlissConfig.get_cc_params()` iterates `self.controls` to build CC messages for preset parameters. Without the controls list, no CC parameters are sent during `rig apply`. User wants the full list for completeness and correctness.

---

## Mood Preset Parameter Names

| Option | Description | Selected |
|--------|-------------|----------|
| Carry over as-is | Keep current parameter names. Rename only if rig validate rejects them. | |
| Rename to match controls block | Align parameter names with the MIDI CC control map. | |
| Check rig-cli first, then decide | Run rig validate against a test migration to see what it accepts. | |

**User's choice:** "they should match the definition in rig-cli"
**Notes:** Checked `rig_chasebliss/catalog.py` — existing parameter names in mood.yaml already match MOOD_MKII_CONTROLS exactly. No renaming needed. The user's intent is satisfied by the current names.

---

## Claude's Discretion

None — all three areas had clear user direction.

## Deferred Ideas

- Adding Wombtone (billy-strings-wombtone) and Brothers devices — Phase 3
- Tuning Mood preset parameter values — Phase 3 (PRESET-01)
- MC6 JSON generation — v1.3+
