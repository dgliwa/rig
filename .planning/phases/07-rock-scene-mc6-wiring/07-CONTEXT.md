# Phase 7: Rock Scene & MC6 Wiring - Context

**Gathered:** 2026-06-18
**Status:** Ready for planning

<domain>
## Phase Boundary

Wire all four devices to their rock presets in a new `rock` scene under `mc6.config.scenes` in `rig.yaml`, then assign that scene to MC6 Bank 1 Switch B. `rig validate` must pass with no cross-reference errors.

The `rock` scene key is fixed by REQUIREMENTS.md. Commented-out scenes and switches (boogie, wacky, marshall, switches C–D) are left as-is — this phase does not touch them.

</domain>

<decisions>
## Implementation Decisions

### Scene Description
- **D-01:** `description: "90s rock: Mesa Boogie amp, Brothers distortion, Wombtone wah, Mood chorus"`

### Device-to-Preset Wiring
- **D-02:** The `rock` scene wires exactly these four device–preset pairs (all IDs established in Phases 5–6):
  - `brothers: rock-drive`
  - `billy-strings-wombtone: envelope-filter`
  - `hx-stomp: mesa-rock`
  - `mood: chorus-shimmer`

### Commented Content
- **D-03:** Leave all existing commented-out scenes and switches as-is. No cleanup — Phase 7 only adds, does not remove.

### Switch Assignment
- **D-04:** MC6 Bank 1 Switch B → `scene: rock`. Structure mirrors Switch A (`scene: clean`).

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Config
- `rig.yaml` — Primary config. The `mc6.config.scenes` block (around line 302) is where the `rock` scene is added. The `mc6.config.banks[0].switches` block (around line 334) is where Switch B is assigned. Study the existing `clean` scene and Switch A as the exact structural template.

### Requirements
- `.planning/REQUIREMENTS.md` — SCENE-01, SCENE-02, SCENE-03 are the three acceptance criteria for this phase.
- `.planning/ROADMAP.md` §Phase 7 — Success criteria: (1) rock scene exists referencing all four device presets, (2) Switch B mapped to rock, (3) `rig validate` passes.

### Prior Phase Decisions
- `.planning/phases/05-cba-device-presets/05-CONTEXT.md` — Preset IDs for brothers, wombtone, mood established here.
- `.planning/phases/06-hx-stomp-mesa-rock-entry/06-CONTEXT.md` — HX Stomp `mesa-rock` preset ID and `hlx/MesaRock.hlx` path established here.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `mc6.config.scenes.clean` in `rig.yaml` (~lines 302–309) — Direct template for the `rock` scene. Same structure: `description:`, `presets:` map keyed by device ID.
- `mc6.config.banks[0].switches.A` (~line 335) — Direct template for Switch B assignment: `scene: <scene-key>`.

### Established Patterns
- Scene preset keys use device `id` field exactly: `billy-strings-wombtone` (not `wombtone`), `hx-stomp`, `brothers`, `mood`.
- Generated MC6 JSON (`generated/mc6/bank1.json`) only emits MIDI PC commands for MIDI-capable devices (hx-stomp + mood). Brothers and wombtone appear in `rig.yaml` scenes for documentation/validation but produce no MIDI commands — this is expected behavior.

### Integration Points
- `rig validate` cross-checks that device IDs and preset IDs referenced in scenes exist in the devices list. All four preset IDs (`rock-drive`, `envelope-filter`, `mesa-rock`, `chorus-shimmer`) are already present.
- `rig generate mc6` regenerates `generated/mc6/bank1.json` from `rig.yaml` — running it after the change will add Switch B with the rock presets' PC numbers.

</code_context>

<specifics>
## Specific Ideas

No specific references — implementation is fully specified by the decisions above and the existing `clean` scene/Switch A as templates.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 7-Rock Scene & MC6 Wiring*
*Context gathered: 2026-06-18*
