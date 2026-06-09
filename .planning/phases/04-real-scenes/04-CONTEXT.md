# Phase 4: Real Scenes - Context

**Gathered:** 2026-06-09
**Status:** Ready for planning

<domain>
## Phase Boundary

Update the 4 existing scenes (clean, crunch, lead, wacky) to incorporate Wombtone and Brothers where musically appropriate, rewrite all Mood presets from the official doc recipes, sharpen scene descriptions, and confirm MC6 switch ordering. No new devices added, no rig-cli source changes.

Requirements: SCENE-01, SCENE-02, SCENE-03 from REQUIREMENTS.md.

</domain>

<decisions>
## Implementation Decisions

### Scene device mapping
- **D-16:** Each scene's final device-to-preset assignments:
  - `clean`: `hx-stomp: princeton` + `billy-strings-wombtone: slow-vibe` + `brothers: am-crunch` + `mood: <new preset from doc>`
  - `crunch`: `hx-stomp: marshall` + `brothers: am-crunch` + `mood: <new preset from doc>`
  - `lead`: `hx-stomp: boogie-pb` + `brothers: am-crunch` + `mood: <new preset from doc>`
  - `wacky`: `hx-stomp: doogie-pb` + `billy-strings-wombtone: trem-pulse` + `mood: <new preset from doc>`

- **D-17:** Brothers `am-crunch` in the `clean` scene is intentional. "Clean" refers to the HX Stomp Princeton tone — Brothers is present in the scene for optional grit when the footswitch is engaged, not always-on. No additional Brothers presets needed.

- **D-18:** Wombtone `slow-vibe` in `clean`, `trem-pulse` in `wacky`. Wombtone not included in `crunch` or `lead` (Brothers OD pushes those scenes). Signal chain: brothers → wombtone → hx-stomp → mood, so when both are active in `clean`, Brothers is upstream of Wombtone ("DIRT FIRST" from the Wombtone doc).

### Mood preset rewrite
- **D-19:** All 4 Mood presets are a full rewrite from the official doc recipes. Planner chooses the recipe with the best musical fit for each scene context:
  - `clean` context → subtle ambient wash or looper pad that doesn't overwhelm a clean tone
  - `crunch` context → rhythmic/washy texture that supports a mid-gain crunch tone
  - `lead` context → more aggressive or granular texture fitting a high-gain lead
  - `wacky` context → Tier 2 territory (drone machine, reverse-stretch, melting tape)
  - New preset IDs are TBD — planner establishes them; scene preset references in mc6.config.scenes must use the new IDs.
  - Existing preset IDs (sigur-ros-drone, ambient-pad-machine, broken-tape-memories, second-guitarist) are replaced.

### MC6 switch ordering
- **D-20:** Bank 1 "Main Rig" switch order stays unchanged: A→clean, B→crunch, C→lead, D→wacky. This already matches the user's performance workflow.

### Scene descriptions
- **D-21:** Updated descriptions (concise style, anchor to amp + key texture):
  - `clean`: `"Clean Fender with phaser vibe and doubling"`
  - `crunch`: `"British crunch with Brothers OD and ambient pad"`
  - `lead`: `"High gain Boogie lead with Brothers boost and broken tape"`
  - `wacky`: `"BOC saturated lead with tremolo and Sigur Rós drone"`

### Scene names
- **D-22:** Keep existing scene names: clean, crunch, lead, wacky. No renames.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Device reference docs (user-provided — primary sources for preset parameters)
- `~/Public/Drop Box/mood-mkii-presets.md` — Official Mood MkII recipes, Tier 1 and Tier 2. **Use for Mood preset rewrite (D-19).** Tier 1: ①Liquid Ambient Wash, ②Dub Tape Delay, ③Clean Looper Pad, ④Granular Bloop Cloud. Tier 2: ⑤–⑧ extreme/wacky recipes. All knob positions are clock-based.
- `~/Public/Drop Box/wombtone-billystrings-presets.md` — Official Billy Strings Wombtone manual. Controls, dip switches, envelope, and phaser recipes. Reference for any Wombtone parameter questions.
- `~/Public/Drop Box/brothers-am-presets.md` — Brothers AM signal flow, MIDI CCs, and recipe notes. Reference for Brothers parameter questions.

### rig-cli device and config models
- `~/dev/rig-cli/packages/rig-chasebliss/src/rig_chasebliss/catalog.py` — `MOOD_MKII_CONTROLS`, `WOMBTONE_MKII_CONTROLS`, `BROTHERS_AM_CONTROLS`. Canonical control names, types, CC numbers, and ranges.
- `~/dev/rig-cli/packages/rig-chasebliss/src/rig_chasebliss/device.py` — `ChaseBlissConfig`, `ChaseBlissDevice`, `get_cc_params()`, `validate_cc_params()`.
- `~/dev/rig-cli/packages/rig/src/rig/config/loader.py` — `_validate_references()` cross-checks scene preset references against device/preset IDs. Changing preset IDs in `mood.presets` requires updating `mc6.config.scenes` to match.

### Existing config
- `rig.yaml` — Current single-file config. Sections to update: `mood.presets` (full rewrite), `mc6.config.scenes` (add new devices, update Mood preset IDs, update descriptions).

### Requirements and roadmap
- `.planning/REQUIREMENTS.md` — SCENE-01, SCENE-02, SCENE-03 define acceptance criteria.
- `.planning/ROADMAP.md` — Phase 4 success criteria and scope.
- `.planning/PROJECT.md` — Out of Scope list (no rig-cli source changes, no new hardware).

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `rig.yaml` `mc6.config.scenes`: existing scene structure carries over — only update `description`, `presets` entries, and add new device references per D-16.
- `rig.yaml` `mood.presets`: all 4 presets are replaced. New preset IDs established during planning.
- `rig.yaml` `brothers.presets` and `billy-strings-wombtone.presets`: carry over unchanged (am-crunch, slow-vibe, trem-pulse already tuned in Phase 3).

### Validation behavior
- `_validate_references()` checks: scene preset references use real device IDs and preset IDs. If Mood preset IDs change in D-19, the `mc6.config.scenes` Mood references must be updated atomically or `rig validate` will fail.
- Adding `brothers` and `billy-strings-wombtone` entries to scenes just requires the preset IDs to match what's in `rig.yaml` (am-crunch, slow-vibe, trem-pulse — all present).
- Run `rig validate` after each scene update as acceptance gate.

### Established Patterns
- YAML `#` comments on non-obvious parameter values (carried from Phase 3, D-13).
- `notes` field on preset objects for high-level sonic intent.

</code_context>

<specifics>
## Specific Ideas

- Mood preset rewrite must use the doc's named recipes as the basis (e.g. "Liquid Ambient Wash", "Dub Tape Delay") — not re-derived from scratch. The doc's Tier 1 / Tier 2 organization maps cleanly to the scene tonal range.
- When translating clock-position knob values from the Mood doc to 0–127 MIDI values: 7 o'clock ≈ 0, 12 o'clock (noon) ≈ 64, 5 o'clock ≈ 127. "11 o'clock" ≈ 48, "1 o'clock" ≈ 80, "2–3 o'clock" ≈ 90–100.
- Dip switch settings from the doc recipes should be captured in the preset's `parameters` using the dip control names from the controls block.

</specifics>

<deferred>
## Deferred Ideas

- MC6 JSON generation (`rig generate mc6`) — v1.3+
- Adding more Brothers or Wombtone presets beyond the current 3 — future phase if needed
- Envelope-routing presets for Wombtone (requires dip switch sequences, deferred until live testing confirms value)

</deferred>

---

*Phase: 4-Real Scenes*
*Context gathered: 2026-06-09*
