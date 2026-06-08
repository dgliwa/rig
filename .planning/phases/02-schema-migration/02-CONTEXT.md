# Phase 2: Schema Migration - Context

**Gathered:** 2026-06-08
**Status:** Ready for planning

<domain>
## Phase Boundary

Collapse `devices/*.yaml`, `scenes/*.yaml`, and `signal-chain.yaml` into a single `rig.yaml` that passes `rig validate` reporting actual device and scene counts. Migrate existing 3 devices (hx-stomp, mood, mc6) and 4 scenes — no new devices added.

Requirements: SCHEMA-01, SCHEMA-02, SCHEMA-03, SCHEMA-04 from REQUIREMENTS.md.

</domain>

<decisions>
## Implementation Decisions

### Devices scope
- **D-01:** Migrate the 3 existing devices only (hx-stomp, mood, mc6). Wombtone and Brothers are Phase 3 additions. SCHEMA-01 "all devices" includes new devices but that clause is satisfied when Phase 3 completes.

### rig.yaml structure
- **D-02:** `devices:` is an ordered list. Order matches signal chain from signal-chain.yaml: hx-stomp (1), mood (2), mc6 (3). MC6 as controller always applies last regardless of list position.
- **D-03:** Scenes move from `scenes/*.yaml` into `mc6.config.scenes` keyed by scene name. Fields carried over: `description`, `presets`. The `mc6_bank`/`mc6_switch` fields in scene files are dropped — bank/switch assignments stay in `mc6.config.banks`.
- **D-04:** After successful `rig validate`, remove `signal-chain.yaml`, all files in `devices/`, and all files in `scenes/`.

### Mood MkII controls block
- **D-05:** Include the full `controls:` list inside `mood.config` using `Control` model fields (`name`, `type`, `midi_cc`, `min`, `max`, `positions`). Source of truth: `MOOD_MKII_CONTROLS` in `~/dev/rig-cli/packages/rig-chasebliss/src/rig_chasebliss/catalog.py`. Without `controls:`, `ChaseBlissConfig.get_cc_params()` returns an empty list and no CC parameters are sent to the pedal during `rig apply`.

### Mood preset parameter names
- **D-06:** Existing parameter names in `mood.yaml` already match the `MOOD_MKII_CONTROLS` catalog exactly. No renaming required. Confirmed matches: `time`, `mix`, `length`, `wet_modify`, `clock`, `loop_modify`, `ramp_speed`, `wet_channel`, `routing`, `loop`, `wet_clock`, `loop_clock_division`, `dip_r_spread`, `dip_r_trails`, `dip_r_smooth`, `dip_l_bounce`, `dip_l_sweep`.

### Config type strings (from entry point registry)
- **D-07:** `hx-stomp.config.type` = `"midi"` (maps to `HXStompDevice`); `mood.config.type` = `"chase_bliss"` (maps to `ChaseBlissDevice`); `mc6.config.type` = `"controller"` (maps to `MC6Device`). These must match exactly.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### rig-cli schema and device models
- `~/dev/rig-cli/packages/rig/src/rig/config/loader.py` — Single-file schema doc comment + full load_rig() implementation. Defines expected rig.yaml structure and how devices list → signal chain.
- `~/dev/rig-cli/packages/rig-chasebliss/src/rig_chasebliss/catalog.py` — `MOOD_MKII_CONTROLS` list: canonical control names, types, and MIDI CC numbers for all Mood MkII parameters.
- `~/dev/rig-cli/packages/rig-chasebliss/src/rig_chasebliss/device.py` — `ChaseBlissConfig` Pydantic model: `controls: list[Control]`. `get_cc_params()` maps preset parameters → CC messages using the controls list.
- `~/dev/rig-cli/packages/rig/tests/fixtures/sample_rig/rig.yaml` — Reference example of the v1.2 single-file format (note: uses outdated control names — defer to catalog.py for authoritative names).

### Current config files (being migrated)
- `devices/hx-stomp.yaml` — HX Stomp presets: princeton (36), marshall (37), boogie-pb (38), doogie-pb (39)
- `devices/mood.yaml` — Mood MkII presets: sigur-ros-drone, ambient-pad-machine, broken-tape-memories, second-guitarist
- `devices/mc6.yaml` — MC6 bank/switch assignments and controller config
- `scenes/clean.yaml`, `scenes/crunch.yaml`, `scenes/lead.yaml`, `scenes/wacky.yaml` — scene-to-preset mappings
- `signal-chain.yaml` — device order (hx-stomp: 1, mood: 2, mc6: 3)

### Requirements
- `.planning/REQUIREMENTS.md` — SCHEMA-01 through SCHEMA-04 define acceptance criteria
- `.planning/PROJECT.md` — Key Decisions table and Out of Scope list

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- All preset data in `devices/mood.yaml` and `devices/hx-stomp.yaml` carries over verbatim — no parameter changes needed
- `mc6.yaml` banks section carries over directly into `mc6.config.banks` in rig.yaml

### Established Patterns
- `rig validate` calls `load_rig(config)` which reads a single `rig.yaml`; validation = successful parse + cross-reference check. No separate schema file to satisfy.
- `_validate_references()` in loader.py checks: (a) signal_chain device IDs exist, (b) scene preset references point to real device/preset IDs. Both must pass.

### Integration Points
- `rig validate` is the acceptance gate — run it against the new rig.yaml before removing old files
- Scene preset references use device IDs (`hx-stomp`, `mood`) and preset IDs (e.g., `princeton`, `second-guitarist`) — these must be consistent between devices and mc6.config.scenes

</code_context>

<specifics>
## Specific Ideas

- Include the full `MOOD_MKII_CONTROLS` list in `mood.config.controls` — the user explicitly wants this for completeness and future `rig apply` CC parameter sending
- Run `rig validate` as the verification step before deleting old files

</specifics>

<deferred>
## Deferred Ideas

- Adding Wombtone (billy-strings-wombtone) and Brothers devices — Phase 3
- Tuning Mood preset parameter values — Phase 3 (PRESET-01)
- MC6 JSON generation — v1.3+

</deferred>

---

*Phase: 2-Schema Migration*
*Context gathered: 2026-06-08*
