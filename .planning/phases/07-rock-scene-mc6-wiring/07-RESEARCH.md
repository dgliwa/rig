# Phase 7: Rock Scene & MC6 Wiring — Research

**Researched:** 2026-06-18
**Domain:** rig.yaml YAML editing — scenes and MC6 switch mapping
**Confidence:** HIGH

---

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** `description: "90s rock: Mesa Boogie amp, Brothers distortion, Wombtone wah, Mood chorus"`
- **D-02:** Device-to-preset wiring: `brothers: rock-drive`, `billy-strings-wombtone: envelope-filter`, `hx-stomp: mesa-rock`, `mood: chorus-shimmer`
- **D-03:** Leave all commented-out scenes and switches as-is
- **D-04:** MC6 Bank 1 Switch B → `scene: rock`. Structure mirrors Switch A (`scene: clean`)

### Claude's Discretion
None — implementation is fully specified.

### Deferred Ideas (OUT OF SCOPE)
None.
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| SCENE-01 | `rock` scene added to `mc6.config.scenes` with all four device-preset pairs | Template confirmed: mirror `clean` scene structure exactly |
| SCENE-02 | Switch B in MC6 Bank 1 wired to `scene: rock` | Template confirmed: mirror Switch A `scene: clean` structure |
| SCENE-03 | `rig validate` passes with no cross-reference errors | All four preset IDs verified present; validate currently outputs 2 clean lines |
</phase_requirements>

---

## Summary

Phase 7 is a minimal YAML edit: add one `rock` scene under `mc6.config.scenes` and uncomment/add Switch B under `mc6.config.banks[0].switches`. The `clean` scene (lines 303–309) and Switch A (lines 335–336) are the exact structural templates to mirror. All four preset IDs required by the decisions already exist in `rig.yaml`. The `rig validate` command passes on the current file and will be the acceptance gate after editing.

There is no `rig generate` or `rig generate mc6` command — the CLI offers only: `apply`, `diff`, `edit`, `plan`, `status`, `validate`. The CONTEXT.md reference to `rig generate mc6` is inaccurate; that command does not exist. The `generated/mc6/` directory may be maintained separately or via a different mechanism, but no planner task should invoke `rig generate mc6`.

**Primary recommendation:** Copy the `clean` scene block as the template, substitute `rock` key and the four D-02 preset values, then add `B:\n  scene: rock` under switches. Run `rig validate` to confirm.

---

## Architectural Responsibility Map

| Capability | Primary Tier | Secondary Tier | Rationale |
|------------|-------------|----------------|-----------|
| Scene definition | rig.yaml config | — | Declarative config drives all downstream behavior |
| Switch assignment | rig.yaml config | — | Controller mapping is config-layer concern |
| Cross-reference validation | `rig validate` CLI | — | CLI reads rig.yaml and checks all IDs exist |

---

## Standard Stack

No packages to install. This phase is a YAML edit only.

---

## Package Legitimacy Audit

Not applicable — no packages installed in this phase.

---

## Architecture Patterns

### Exact YAML Structure Confirmed

**`clean` scene template (lines 303–309, [VERIFIED: direct file read]):**

```yaml
scenes:
  clean:
    description: "Clean Fender with phaser vibe and doubling"
    presets:
      hx-stomp: clean
      billy-strings-wombtone: slow-vibe
      brothers: transparent-lift
      mood: ambient-reverse-tape
```

**`rock` scene to add (mirrors clean exactly):**

```yaml
  rock:
    description: "90s rock: Mesa Boogie amp, Brothers distortion, Wombtone wah, Mood chorus"
    presets:
      hx-stomp: mesa-rock
      billy-strings-wombtone: envelope-filter
      brothers: rock-drive
      mood: chorus-shimmer
```

The `rock:` key goes after `clean:` and before the `# boogie:` comment block. Indentation is 8 spaces (4 for scenes key indent + 4 for scene name).

**Switch A template (lines 335–336, [VERIFIED: direct file read]):**

```yaml
          switches:
            A:
              scene: clean
            # B:
            #   scene: crunch
```

**Switch B to add (replaces the `# B:` comment block):**

```yaml
            B:
              scene: rock
```

Switch B replaces the existing `# B:` comment lines (337–338). The comment `# scene: crunch` is replaced entirely — the CONTEXT.md decision D-03 says leave other commented switches (C, D) as-is, but the existing `# B:` block is specifically what Switch B replaces.

### Insertion Points

| Edit | Location in rig.yaml | Action |
|------|----------------------|--------|
| `rock` scene | After line 309 (end of `clean` presets), before line 310 (`# boogie:`) | Insert 7 lines |
| Switch B | Replace lines 337–338 (`# B:` and `#   scene: crunch`) | Replace 2 comment lines with 2 active lines |

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Preset ID validation | Manual grep | `rig validate` | CLI checks all cross-references automatically |

---

## Common Pitfalls

### Pitfall 1: Wrong indentation
**What goes wrong:** YAML silently parses with wrong nesting if indentation is off by 2 spaces.
**Why it happens:** The scenes block is nested 4 levels deep (device → config → scenes → scene-key).
**How to avoid:** Match existing `clean` block indentation exactly — 8 spaces for scene key, 10 for `description:`, 10 for `presets:`, 12 for each device key.
**Warning signs:** `rig validate` will error or the scene will not appear under the mc6 config.

### Pitfall 2: Wrong device key spelling
**What goes wrong:** Using `wombtone` instead of `billy-strings-wombtone` as the preset map key.
**Why it happens:** The device `id` field is `billy-strings-wombtone` — the full ID is required.
**How to avoid:** Match the `clean` scene exactly: `billy-strings-wombtone: envelope-filter`.
**Warning signs:** `rig validate` cross-reference error on unknown device ID.

### Pitfall 3: Calling `rig generate mc6`
**What goes wrong:** Command does not exist; will error.
**Why it happens:** CONTEXT.md mentions this command but it is not in the CLI.
**How to avoid:** Do not include a `rig generate mc6` task in the plan. The CLI has no `generate` subcommand.
**Warning signs:** `No such command 'generate'` error from rig CLI.

---

## Code Examples

### Verified preset IDs (from rig.yaml, [VERIFIED: grep of /Users/derekgliwa/dev/rig/rig.yaml])

| Preset ID | Line | Device |
|-----------|------|--------|
| `rock-drive` | 43 | brothers |
| `envelope-filter` | 122 | billy-strings-wombtone |
| `mesa-rock` | 176 | hx-stomp |
| `chorus-shimmer` | 225 | mood |

All four IDs exist. `rig validate` cross-checks these.

### `rig validate` current output ([VERIFIED: command run])

```
✓ Derek's Rig — 5 pedals, 1 scenes
✓ All cross-references valid
```

After adding the `rock` scene the output should read `2 scenes`. No other changes expected.

---

## State of the Art

Not applicable — this is a project-specific YAML config file, not an external library.

---

## Assumptions Log

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| A1 | Switch B should replace the existing `# B: / #   scene: crunch` comment lines rather than being inserted after them | Architecture Patterns | Leaving both comment and active entry would result in two B entries — YAML would silently use the first |

**Verification path for A1:** The comment block at lines 337–338 is `# B:` / `#   scene: crunch`. Replacing it with active `B:` / `  scene: rock` is the standard pattern used in this file. The clean structure of the file confirms this is correct.

---

## Open Questions

None. All structural templates verified directly from file reads and CLI runs.

---

## Environment Availability

| Dependency | Required By | Available | Version | Fallback |
|------------|------------|-----------|---------|----------|
| `rig validate` | Acceptance gate | Yes | — (mise-managed Python CLI) | — |

---

## Validation Architecture

### Phase Requirements → Test Map

| Req ID | Behavior | Test Type | Automated Command | Notes |
|--------|----------|-----------|-------------------|-------|
| SCENE-01 | `rock` scene exists with 4 device-preset pairs | smoke | `rig validate` | Passes when cross-references valid |
| SCENE-02 | Switch B maps to `rock` scene | smoke | `rig validate` | Validates scene key exists |
| SCENE-03 | `rig validate` exits 0 with no errors | smoke | `rig validate` | The acceptance gate itself |

**Full suite command:** `rig validate`
**Phase gate:** `rig validate` outputs 2 green lines before marking phase complete.

---

## Security Domain

Not applicable — this phase edits a local YAML config file with no authentication, network access, or sensitive data.

---

## Sources

### Primary (HIGH confidence)
- `/Users/derekgliwa/dev/rig/rig.yaml` lines 295–343 — direct file read, exact YAML structure confirmed
- `/Users/derekgliwa/dev/rig/rig.yaml` lines 43, 122, 176, 225 — preset ID existence confirmed via grep
- `rig validate` — CLI invoked, output confirmed
- `rig --help` — full command list confirmed, no `generate` subcommand exists

---

## Metadata

**Confidence breakdown:**
- YAML structure: HIGH — read directly from file
- Preset IDs: HIGH — grepped and confirmed with line numbers
- `rig validate` behavior: HIGH — run live
- `rig generate mc6` nonexistence: HIGH — `rig --help` output inspected

**Research date:** 2026-06-18
**Valid until:** Indefinite — this is a static config file with no external dependencies
