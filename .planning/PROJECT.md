# Derek's Guitar Rig

## What This Is

Infrastructure-as-Code configuration for a personal guitar rig (HX Stomp + Mood MkII + MC6 controller, plus Wombtone MkII and Brothers AM). YAML files declare devices, presets, and scenes; rig-cli validates, plans, and applies those configs to physical MIDI devices. One person, one rig, fully git-tracked.

## Core Value

A single `rig validate` should confirm the config repo is consistent and ready to apply — no guessing, no manual cross-referencing.

## Current Milestone: v1.3 — 90s Rock Sound

**Goal:** Configure all rig devices with a Weezer-style 90s rock preset set — transparent boost, aggressive distortion, chorus textures, and an envelope-filter wah — then wire everything into a new HX Stomp Mesa Boogie amp scene.

**Target features:**
- Brothers AM — Channel A: light/transparent boost preset; Channel B: kicking distortion preset
- Mood MkII — new chorus-y preset
- Wombtone MkII — new envelope-filter/auto-wah preset
- HX Stomp — Mesa Boogie + 4x12 cab patch with delay, reverb, and optional chorus in FX loop
- New rock scene — wire all devices into a MC6-accessible rock performance scene

## Current State

**Shipped: v1.2 — Flesh Out the Rig (2026-06-09)**

The rig config is now a single `rig.yaml` (756 lines) with 5 devices, 15 presets, and 4 real scenes. `rig validate` reports "5 pedals, 4 scenes — All cross-references valid."

**Deferred:** Phase 2 documentation debt (missing VERIFICATION.md, REQUIREMENTS.md traceability not updated). Nyquist validation never run. These were accepted at close.

## Requirements

### Validated

- ✓ `pedals/` directory renamed to `devices/` — v1.1
- ✓ `signal-chain.yaml` uses `device:` field — v1.1
- ✓ In-progress changes committed cleanly (mc6, Mood presets, scenes) — v1.1
- ✓ SCHEMA-01: `rig.yaml` has `devices:` list with all devices and presets inline — v1.2
- ✓ SCHEMA-02: Scenes under MC6 `config.scenes` — v1.2
- ✓ SCHEMA-03: Legacy files removed (`signal-chain.yaml`, `devices/`, `scenes/`) — v1.2
- ✓ SCHEMA-04: `rig validate` reports actual device and scene counts — v1.2
- ✓ PRESET-01: Billy Strings Wombtone device with real presets — v1.2
- ✓ PRESET-02: Brothers AM device with real presets — v1.2
- ✓ PRESET-03: Mood MkII presets reviewed and annotated — v1.2
- ✓ PRESET-04: Non-obvious parameter values annotated — v1.2
- ✓ SCENE-01: Accurate scene descriptions — v1.2
- ✓ SCENE-02: Wombtone and Brothers in scenes — v1.2
- ✓ SCENE-03: MC6 bank/switch performance workflow — v1.2

### Active

- [ ] **GEN-01**: `rig generate mc6` produces valid MC6 bank JSON from scene definitions
- [ ] **GEN-02**: Generated MC6 JSON can be loaded onto hardware without manual editing

### Out of Scope

| Feature | Reason |
|---------|--------|
| New pedal/device additions | No new gear being added |
| rig-cli source changes | This is a rig config repo only |
| HX Stomp preset content changes | HX presets stay as-is; format not managed here |

## Context

- **rig-cli**: v1.2+ (single-file schema); `devices/` directory-based loading deprecated
- **rig.yaml**: 756 lines, 5 devices, 15 presets, 4 scenes
- **Devices**: brothers (Brothers AM), billy-strings-wombtone (Wombtone MkII), hx-stomp (HX Stomp), mood (Mood MkII), mc6 (MC6 controller)
- **Signal chain**: brothers → wombtone → hx-stomp → mood → mc6
- **MIDI channels**: brothers=4, wombtone=3, hx-stomp=1, mood=2, mc6=1
- **Scenes**: clean (Fender + vibe + doubling), crunch (British crunch + OD + pad), lead (Boogie + boost + broken tape), wacky (BOC lead + trem + drone)
- **MC6 Bank 1**: A=clean, B=crunch, C=lead, D=wacky
- **`rig validate`**: "5 pedals, 4 scenes — All cross-references valid"
- **Controls per device**: brothers=28, wombtone=12, hx-stomp=0, mood=45, mc6=0
- **Doc debt**: Phase 2 missing VERIFICATION.md; no Nyquist validation run on any phase

## Constraints

- **Compatibility**: Must pass `rig validate` against rig-cli as installed at `~/dev/rig-cli`
- **Scope**: Config-only changes — no Python/rig-cli source modifications

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Version rig repo at v1.1 to match rig-cli counterpart | Keeps version numbers in sync | ✓ Good |
| Rename `pedals/` → `devices/` | rig-cli TODOs signal `pedals/` will break | ✓ Good |
| Defer single-file migration to v1.2 | v1.1 scope was naming only | ✓ Accomplished in Phase 2 |
| MIDI channels: mood=2, wombtone=3, brothers=4 | CBA defaults; avoids conflicts | ✓ Good |
| Signal chain: brothers → wombtone → hx-stomp → mood → mc6 | Gain staging: drive → modulation → amp → wet → controller | ✓ Good |
| MC6 Bank 1: A=clean, B=crunch, C=lead, D=wacky | Performance order: most-used first | ✓ Good |
| Mood presets from official doc recipes | Avoids low-quality fuzz; uses vetted sounds | ✓ Good |
| Accept Phase 2 doc debt at close | Verification and traceability are documentation-only gaps | ⚠ Revisit |

---
*Last updated: 2026-06-09 — v1.2 milestone shipped*
