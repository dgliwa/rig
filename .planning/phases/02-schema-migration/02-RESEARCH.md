# Phase 2: Schema Migration — Research

**Phase:** 02-schema-migration
**Researched:** 2026-06-08
**Sources:** loader.py, catalog.py, device.py, sample_rig/rig.yaml, current config files, `rig validate` output

---

## RESEARCH COMPLETE

---

## 1. Current State

`rig.yaml` exists but only contains top-level metadata:
```yaml
name: Derek's Rig
description: MC6 controller → Mood MkII → HX Stomp
midi_channel: 1
```

`rig validate` currently outputs: `✓ Derek's Rig — 0 pedals, 0 scenes` (no `devices:` list loaded).

Separate files still in use: `devices/hx-stomp.yaml`, `devices/mood.yaml`, `devices/mc6.yaml`, `scenes/clean.yaml`, `scenes/crunch.yaml`, `scenes/lead.yaml`, `scenes/wacky.yaml`, `signal-chain.yaml`.

---

## 2. Target Schema (v1.2 Single-File Format)

From `loader.py` docstring and `load_rig()` implementation:

```yaml
name: <rig name>
description: <description>
devices:
  - id: hx-stomp
    manufacturer: Line 6
    model: HX Stomp
    type: modeler
    config:
      type: midi
      midi_channel: 1
    presets:
      - id: princeton
        name: Princeton D
        preset_number: 36
        hlx_file: hlx/Princeton D.hlx
        notes: Clean Fender tone, D path
      # ...

  - id: mood
    manufacturer: Chase Bliss Audio
    model: Mood MkII
    type: digital
    config:
      type: chase_bliss
      midi_channel: 2
      controls:
        - name: time
          type: knob
          midi_cc: 14
          min: 0
          max: 127
        # ... full MOOD_MKII_CONTROLS list
    presets:
      - id: sigur-ros-drone
        name: Sigur Ros Drone
        preset_number: 3
        parameters:
          time: 70
          # ...

  - id: mc6
    manufacturer: Morningstar
    model: MC6
    type: controller
    config:
      type: controller
      midi_channel: 1
      scenes:
        clean:
          description: Clean Fender with second guitarist
          presets:
            hx-stomp: princeton
            mood: second-guitarist
        # ... all 4 scenes
      banks:
        - bank: 1
          name: Main Rig
          switches:
            A:
              scene: clean
            # ...
```

**Device list order defines the signal chain** — `load_rig()` builds `signal_chain` by iterating `device_list` in order. Order must be: hx-stomp (1), mood (2), mc6 (3).

---

## 3. Config Type Strings (Confirmed)

From `get_registry().get_model(config_type)` in loader.py:
- `hx-stomp`: `config.type = "midi"` → `HXStompDevice`
- `mood`: `config.type = "chase_bliss"` → `ChaseBlissDevice`
- `mc6`: `config.type = "controller"` → `MC6Device`

These must match entry point registry keys exactly.

---

## 4. Mood Controls Block (Required for `rig apply`)

`ChaseBlissConfig.get_cc_params()` iterates `self.controls` to build CC messages. Without `controls:`, no CC parameters are sent during `rig apply`.

**Full MOOD_MKII_CONTROLS to include in `mood.config.controls`:**

```yaml
controls:
  - {name: time, type: knob, midi_cc: 14, min: 0, max: 127}
  - {name: mix, type: knob, midi_cc: 15, min: 0, max: 127}
  - {name: length, type: knob, midi_cc: 16, min: 0, max: 127}
  - {name: wet_modify, type: knob, midi_cc: 17, min: 0, max: 127}
  - {name: clock, type: knob, midi_cc: 18, min: 0, max: 127}
  - {name: loop_modify, type: knob, midi_cc: 19, min: 0, max: 127}
  - {name: ramp_speed, type: knob, midi_cc: 20, min: 0, max: 127}
  - {name: wet_channel, type: toggle, midi_cc: 21, min: 0, max: 127, positions: [reverb, delay, slip]}
  - {name: routing, type: toggle, midi_cc: 22, min: 0, max: 127, positions: [in, wet_and_in, wet]}
  - {name: loop, type: toggle, midi_cc: 23, min: 0, max: 127, positions: [env, tape, stretch]}
  - {name: wet_clock, type: toggle, midi_cc: 53, min: 0, max: 7, positions: [32nd, 16th, eighth_triplet, eighth, dotted_eighth, quarter, half, whole]}
  - {name: loop_clock_division, type: toggle, midi_cc: 54, min: 0, max: 7, positions: [32nd, 16th, eighth_triplet, eighth, dotted_eighth, quarter, half, whole]}
  - {name: true_bypass, type: switch, midi_cc: 55, min: 0, max: 127}
  - {name: micro_bypass, type: switch, midi_cc: 102, min: 0, max: 127}
  - {name: wet_bypass, type: switch, midi_cc: 103, min: 0, max: 127}
  - {name: freeze, type: switch, midi_cc: 105, min: 0, max: 127}
  - {name: loop_overdub, type: switch, midi_cc: 106, min: 0, max: 127}
  - {name: tap_tempo, type: switch, midi_cc: 107, min: 0, max: 127}
  - {name: dip_l_time, type: dipswitch, midi_cc: 61, min: 0, max: 127}
  - {name: dip_l_wet_modify, type: dipswitch, midi_cc: 62, min: 0, max: 127}
  - {name: dip_l_clock, type: dipswitch, midi_cc: 63, min: 0, max: 127}
  - {name: dip_l_loop_modify, type: dipswitch, midi_cc: 64, min: 0, max: 127}
  - {name: dip_l_length, type: dipswitch, midi_cc: 65, min: 0, max: 127}
  - {name: dip_l_bounce, type: dipswitch, midi_cc: 66, min: 0, max: 127}
  - {name: dip_l_sweep, type: dipswitch, midi_cc: 67, min: 0, max: 127}
  - {name: dip_l_polarity, type: dipswitch, midi_cc: 68, min: 0, max: 127}
  - {name: dip_r_classic, type: dipswitch, midi_cc: 71, min: 0, max: 127}
  - {name: dip_r_miso, type: dipswitch, midi_cc: 72, min: 0, max: 127}
  - {name: dip_r_spread, type: dipswitch, midi_cc: 73, min: 0, max: 127}
  - {name: dip_r_dry_kill, type: dipswitch, midi_cc: 74, min: 0, max: 127}
  - {name: dip_r_trails, type: dipswitch, midi_cc: 75, min: 0, max: 127}
  - {name: dip_r_latch, type: dipswitch, midi_cc: 76, min: 0, max: 127}
  - {name: dip_r_no_dub, type: dipswitch, midi_cc: 77, min: 0, max: 127}
  - {name: dip_r_smooth, type: dipswitch, midi_cc: 78, min: 0, max: 127}
```

Note: The sample_rig fixture uses **outdated** control names (e.g., `micro_looper_modify`, `wet_bypass switch`). **Use catalog.py as the authoritative source**, not the fixture.

---

## 5. Mood Preset Parameter Name Verification

Existing parameters in `devices/mood.yaml` checked against `MOOD_MKII_CONTROLS`:

| Parameter | In catalog? | CC |
|-----------|-------------|-----|
| time | ✓ | 14 |
| mix | ✓ | 15 |
| length | ✓ | 16 |
| wet_modify | ✓ | 17 |
| clock | ✓ | 18 |
| loop_modify | ✓ | 19 |
| ramp_speed | ✓ | 20 |
| wet_channel | ✓ | 21 |
| routing | ✓ | 22 |
| loop | ✓ | 23 |
| wet_clock | ✓ | 53 |
| loop_clock_division | ✓ | 54 |
| dip_r_spread | ✓ | 73 |
| dip_r_trails | ✓ | 75 |
| dip_r_smooth | ✓ | 78 |
| dip_l_bounce | ✓ | 66 |
| dip_l_sweep | ✓ | 67 |

**All parameters match — no renaming required.**

---

## 6. Scene Structure Inside MC6

Scenes move from `scenes/*.yaml` into `mc6.config.scenes` keyed by scene name. Fields:
- Keep: `description`, `presets` (device_id → preset_id mapping)
- Drop: `mc6_bank`, `mc6_switch`, `name` (the key serves as name)

All 4 scenes to migrate:
| Scene | hx-stomp preset | mood preset |
|-------|-----------------|-------------|
| clean | princeton | second-guitarist |
| crunch | marshall | ambient-pad-machine |
| lead | boogie-pb | broken-tape-memories |
| wacky | doogie-pb | sigur-ros-drone |

---

## 7. Reference Validation (`_validate_references`)

`loader.py` validates:
1. Signal chain device IDs exist in `devices` dict → satisfied since device list order = signal chain
2. Scene `presets` device IDs exist → hx-stomp and mood are in devices list ✓
3. Scene preset IDs exist for the given device → all preset IDs carry over exactly ✓

**Cross-reference check will pass with the migrated data.**

---

## 8. Deletion Scope (Post-Validation)

Files to delete after `rig validate` passes:
- `signal-chain.yaml`
- `devices/hx-stomp.yaml`
- `devices/mood.yaml`
- `devices/mc6.yaml`
- `scenes/clean.yaml`
- `scenes/crunch.yaml`
- `scenes/lead.yaml`
- `scenes/wacky.yaml`

Directories to remove if empty: `devices/`, `scenes/`

---

## 9. Risks

| Risk | Severity | Mitigation |
|------|----------|------------|
| Pydantic model rejects unrecognized fields from old yaml | Low | We're constructing from scratch, not loading old files |
| controls: block causes parse error if type strings don't match ControlType enum | Medium | Use exact lowercase strings: `knob`, `switch`, `toggle`, `dipswitch` |
| `rig validate` path — must run from repo root or pass path | Low | Run `rig validate` from `/Users/derekgliwa/dev/rig/` |
| Accidental deletion before validation passes | High | Validate first, delete second — explicit ordering in plan |

---

## 10. Recommended Plan Structure

**Single plan** (this is a migration, not a multi-subsystem build):
1. Expand `rig.yaml` with full `devices:` list (hx-stomp, mood with controls, mc6 with scenes)
2. Run `rig validate` — must pass before proceeding
3. Delete legacy files (`signal-chain.yaml`, `devices/`, `scenes/`)
4. Verify `rig validate` still passes on clean repo state

Requirements satisfied: SCHEMA-01 (partial — 3 of 5 devices), SCHEMA-02, SCHEMA-03, SCHEMA-04.
