# Phase 6: HX Stomp Mesa-Rock Entry - Research

**Researched:** 2026-06-18
**Domain:** rig.yaml YAML config edit — HX Stomp preset entry
**Confidence:** HIGH

---

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** `preset_number: 37` — the commented `marshall` entry currently occupies this slot annotation; `mesa-rock` takes slot 37 instead.
- **D-02:** `hlx_file: hlx/MesaRock.hlx` — matches what the user will name the file in HX Edit. No spaces in the filename.
- **D-03:** Entry is **active (uncommented)** in `rig.yaml`. File-existence check is advisory, not blocking.

### Claude's Discretion
- `notes` field content: short description capturing the Mesa Boogie amp character (e.g., "Mesa Boogie amp model, high-gain rock tone").

### Deferred Ideas (OUT OF SCOPE)
None.
</user_constraints>

---

## Summary

This phase adds a single YAML list item to the `hx-stomp.presets` array in `rig.yaml`. All structural decisions are locked. Research confirms the exact field shape from the existing `clean` preset, verifies slot 37 is free (the `marshall` entry that held that annotation is commented out), and establishes that `rig validate` performs no path-format or file-existence checks that would block the entry.

**Primary recommendation:** Copy the `clean` preset block verbatim, swap in the mesa-rock values, and append it immediately after the `clean` entry. No code changes. No dependency installs.

---

## Confirmed `clean` Preset Structure (Template)

Source: `rig.yaml` lines 171–175 [VERIFIED: codebase]

```yaml
      - id: clean
        name: Clean
        preset_number: 36
        hlx_file: hlx/Clean.hlx
        notes: Clean Fender tone
```

**Five fields, all present:**

| Field | Type | Required by Pydantic model |
|-------|------|---------------------------|
| `id` | `str` | Yes — base `Preset` |
| `name` | `str` (default `""`) | Optional — base `Preset` |
| `preset_number` | `int` | Yes — `HXStompPreset` |
| `hlx_file` | `str` | Yes — `HXStompPreset` |
| `notes` | `str \| None` (default `None`) | Optional — base `Preset` |

Source: `rig-cli/packages/rig-hx/src/rig_hx/preset.py` and `rig-cli/packages/rig/src/rig/models/preset.py` [VERIFIED: codebase]

**New entry to write:**

```yaml
      - id: mesa-rock
        name: Mesa Rock
        preset_number: 37
        hlx_file: hlx/MesaRock.hlx
        notes: Mesa Boogie amp model, high-gain rock tone
```

---

## Slot 37 Status

Source: `rig.yaml` lines 176–180 [VERIFIED: codebase]

```yaml
      # - id: marshall
      #   name: Marshall D
      #   preset_number: 37
      #   hlx_file: hlx/Marshall D.hlx
      #   notes: British crunch, D path
```

The `marshall` entry is fully commented out. Slot 37 is **safe to use** — no active preset in `rig.yaml` holds `preset_number: 37`. The commented entries for slots 38 (`boogie-pb`) and 39 (`doogie-pb`) are unaffected.

---

## `hlx_file` Path Constraints

Source: `rig-cli/packages/rig-hx/src/rig_hx/preset.py` [VERIFIED: codebase]

`hlx_file` is declared as a bare `str` on `HXStompPreset`. The loader (`rig-cli/packages/rig/src/rig/config/loader.py`) performs **no path-format validation and no file-existence check** on `hlx_file`. The field is stored as a string and never resolved to a filesystem path during `rig validate`.

`_validate_references()` in `loader.py` checks only:
1. Signal chain device IDs exist in the device list.
2. Scene preset references (`hx-stomp: some-id`) resolve to a known `id` in the device's `presets` list.

**No check on `hlx_file` value.** `hlx/MesaRock.hlx` (no spaces, PascalCase filename) is a valid value that will not cause any validation error, and it follows a cleaner naming convention than the existing commented entries which had spaces (e.g., `hlx/Marshall D.hlx`).

---

## Cross-Reference Chain: Phase 7 → mesa-rock ID

Source: `.planning/ROADMAP.md` §Phase 7 [VERIFIED: codebase]

> "A `rock` scene exists in mc6.config.scenes that references rock presets for brothers, wombtone, **hx-stomp**, and mood"

Source: `06-CONTEXT.md` §Integration Points [VERIFIED: codebase]

> "Phase 7 `rock` scene will reference `hx-stomp: mesa-rock` by ID — the `id:` value must be exactly `mesa-rock` for that cross-reference to resolve."

The `_validate_references()` function in `loader.py` will enforce this: when Phase 7 adds a scene with `hx-stomp: mesa-rock`, the loader iterates `rig.devices["hx-stomp"].presets` and checks that `"mesa-rock"` is in the set of preset IDs. If `id: mesa-rock` is present in the YAML, this resolves correctly. If the ID is wrong (e.g., `mesa_rock` with underscore), `rig validate` will raise a `MissingReferenceError` at Phase 7.

**Chain confirmed:** Phase 7 rock scene → `hx-stomp: mesa-rock` → `id: mesa-rock` in `rig.yaml`.

---

## Implementation Risk: LOW

| Risk Factor | Assessment |
|-------------|------------|
| YAML edit complexity | Single list item append, 5 fields, exact template available |
| Validation blocking | `hlx_file` not checked at all; only `id` must match Phase 7 reference |
| Slot conflict | Slot 37 is fully commented-out — no active preset holds it |
| ID correctness | `mesa-rock` with hyphen is exact match to Phase 7 spec |
| File existence | Advisory only per CONTEXT.md D-03; entry can be live before HX Edit file exists |
| Breaking existing behavior | `clean` preset and scenes are untouched; append-only change |

No blockers. One mechanical edit required.

---

## Phase Requirements Traceability

| ID | Description | Research Support |
|----|-------------|-----------------|
| HX-01 | `rig.yaml` contains a `mesa-rock` HX Stomp preset entry pointing to a user-provided `.hlx` file | Template confirmed from `clean` entry; slot 37 free; `hlx_file` string-only (no path check); `id` must be `mesa-rock` exactly |

---

## Sources

- `rig.yaml` lines 163–191 — hx-stomp device section, existing `clean` preset, commented slots 37–39
- `rig-cli/packages/rig-hx/src/rig_hx/preset.py` — `HXStompPreset` model (`preset_number: int`, `hlx_file: str`)
- `rig-cli/packages/rig/src/rig/models/preset.py` — base `Preset` model (`id`, `name`, `notes`, `tags`)
- `rig-cli/packages/rig/src/rig/config/loader.py` — `_validate_references()` logic (no hlx_file check)
- `.planning/REQUIREMENTS.md` — HX-01 acceptance criterion
- `.planning/ROADMAP.md` — Phase 6 and Phase 7 success criteria
- `06-CONTEXT.md` — locked decisions D-01, D-02, D-03 and integration point note
