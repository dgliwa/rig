# Phase 6: HX Stomp Mesa-Rock Entry - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-06-18
**Phase:** 6-HX Stomp Mesa-Rock Entry
**Areas discussed:** Preset slot number, HX file path & name, Active vs commented

---

## Preset Slot Number

| Option | Description | Selected |
|--------|-------------|----------|
| Use 40 (next available) | Logical sequence after commented entries at 37–39 | |
| Slot 37 (freeform) | Ignore the commented entries, use slot 37 directly | ✓ |

**User's choice:** "ignore the commented entries lets do 37"
**Notes:** The commented `marshall` entry at slot 37 is irrelevant — `mesa-rock` takes 37 as the active amp preset.

---

## HX File Path & Name

| Option | Description | Selected |
|--------|-------------|----------|
| hlx/Mesa Rock.hlx | Matches phase name with a space | |
| hlx/Mesa Boogie.hlx | Names it after the amp model | |
| hlx/MesaRock.hlx (freeform) | No spaces, user-specified exact filename | ✓ |

**User's choice:** `hlx/MesaRock.hlx`
**Notes:** User specified no spaces in the filename. This differs slightly from the commented-preset convention (e.g., `hlx/Marshall D.hlx`), but matches what the user plans to use in HX Edit.

---

## Active vs Commented

| Option | Description | Selected |
|--------|-------------|----------|
| Active (uncommented) | Entry is live; rig validate file check is advisory so it won't block even if .hlx missing | ✓ |
| Commented out | Added but commented until .hlx is built in HX Edit | |

**User's choice:** Active (uncommented)
**Notes:** `rig validate` file-existence check is advisory, not blocking — confirmed correct to ship the entry active.

---

## Claude's Discretion

- **Notes field content:** No strong preference on the description text. Claude will write a short description capturing the Mesa Boogie amp character.

## Deferred Ideas

None — discussion stayed within phase scope.
