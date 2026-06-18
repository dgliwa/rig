# Architecture Research

**Domain:** rig.yaml integration — adding 90s rock presets to existing config
**Researched:** 2026-06-18
**Confidence:** HIGH

## Standard Architecture

### System Overview

```
rig.yaml
├── devices:
│   ├── brothers        ← add: rock-boost preset (new)
│   ├── billy-strings-wombtone  ← add: wah-phaser preset (new)
│   ├── hx-stomp        ← add: mesa-rock preset (new, refs hlx/Mesa Rock.hlx)
│   ├── mood            ← add: chorus preset (new)
│   └── mc6
│       └── config.scenes
│           ├── clean   (existing)
│           └── rock    ← add: new scene (new)
│       └── config.banks
│           └── bank 1
│               ├── A: clean (existing)
│               └── B: rock  ← add switch B (or next available)

hlx/
└── Mesa Rock.hlx       ← new file (created in HX Edit, exported)
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| Device preset block | Declares parameters for one sound | New `- id:` entry under the device's `presets:` list |
| Scene block | Wires one preset per device into a named snapshot | New entry under `mc6.config.scenes` |
| MC6 bank switch | Maps a footswitch to a scene | New entry under `mc6.config.banks[0].switches` |
| .hlx file | Actual HX Stomp patch (amp+cab+FX blocks) | Created in HX Edit app, saved to `hlx/` dir |

## Build Order

Build device presets first, then the scene that references them. Build the .hlx file before the rig.yaml HX preset entry (so the hlx_file path exists).

```
Phase 5: Brothers + Wombtone + Mood presets (CBA devices — no external files needed)
Phase 6: HX Stomp Mesa rock patch (.hlx file + rig.yaml entry)
Phase 7: Rock scene + MC6 Bank 1 Switch B wiring
```

Or compressed into fewer phases if scoping allows.

## MC6 Bank/Switch Placement

Currently active in rig.yaml:
```yaml
banks:
  - bank: 1
    name: Main Rig
    switches:
      A:
        scene: clean
```

The commented content shows B=crunch, C=lead, D=wacky as the intended layout. The rock scene fits naturally at **Bank 1 Switch B** (currently commented out). This preserves the intended bank design and doesn't require a new bank.

**Recommended:** Use Bank 1 B for the rock scene. Keep C/D commented (or leave as-is) until those scenes are built in a future milestone.

## Integration Points

### New Presets → Existing Devices

| Device | New Preset ID | Approach |
|--------|---------------|----------|
| brothers | rock-boost | New entry in `presets:` list; both Ch1+Ch2 active (no `channel_2_bypass`) |
| billy-strings-wombtone | wah-phaser | New entry; rate=0, high feed for static resonant peak |
| mood | weezer-chorus | New entry; `wet_channel: 1` (delay) or `2` (slip) for chorus-like shimmer |
| hx-stomp | mesa-rock | New entry; `preset_number: 37` (or next available); `hlx_file: hlx/Mesa Rock.hlx` |

### New Scene → New Presets

```yaml
# Under mc6.config.scenes:
rock:
  description: "90s rock — Mesa Boogie with Brothers drive and chorus shimmer"
  presets:
    hx-stomp: mesa-rock
    billy-strings-wombtone: wah-phaser
    brothers: rock-boost
    mood: weezer-chorus
```

## Anti-Patterns

### Anti-Pattern 1: Forgetting dip_master on dual-channel Brothers preset

**What people do:** Set both gain_1 and gain_2 without enabling `dip_master: 1`
**Why it's wrong:** Without MASTER, Vol1 + Vol2 stack unpredictably; can't control final output level
**Do this instead:** Always add `dip_master: 1` when both channels are active

### Anti-Pattern 2: Referencing a scene preset ID that doesn't exist

**What people do:** Add the scene before adding the preset, or mistype the preset ID
**Why it's wrong:** `rig validate` will fail with a cross-reference error
**Do this instead:** Add device presets first, verify `rig validate` passes, then add the scene

### Anti-Pattern 3: Referencing a missing hlx_file path

**What people do:** Add HX Stomp preset entry in rig.yaml before the .hlx file exists
**Why it's wrong:** `rig validate` may fail on file existence check (confirm behavior)
**Do this instead:** Create/export the .hlx file to `hlx/` before adding the rig.yaml entry

## Sources

- rig.yaml — current architecture (live reference)
- PROJECT.md — MC6 bank structure (Bank 1: A=clean, B=crunch, C=lead, D=wacky design intent)

---
*Architecture research for: 90s rock sound presets — Derek's Guitar Rig*
*Researched: 2026-06-18*
