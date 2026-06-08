# Derek's Guitar Rig

## What This Is

Infrastructure-as-Code configuration for a personal guitar rig (HX Stomp + Mood MkII + MC6 controller). YAML files declare devices, presets, and scenes; rig-cli validates, plans, and applies those configs to physical MIDI devices. One person, one rig, fully git-tracked.

## Core Value

A single `rig validate` should confirm the config repo is consistent and ready to apply — no guessing, no manual cross-referencing.

## Current Milestone: v1.2 Flesh Out the Rig

**Goal:** Migrate to rig-cli v1.2 single-file schema and build out real, playable scenes with tuned CBA presets.

**Target features:**
- Schema migration — collapse `devices/*.yaml`, `scenes/*.yaml`, and `signal-chain.yaml` into a single `rig.yaml`
- CBA preset tuning — give the Mood MkII presets real, named configurations with meaningful parameter values
- Real scenes — replace placeholder scenes with actual musical combinations for live use

## Requirements

### Validated

- ✓ `pedals/` directory renamed to `devices/` so rig-cli uses preferred path — v1.1
- ✓ `signal-chain.yaml` uses `device:` field for all chain entries — v1.1
- ✓ In-progress changes committed cleanly (mc6, Mood presets, scenes) — v1.1

### Active

- [ ] **SCHEMA-01**: `rig validate` validates actual device and scene content (currently 0 devices/0 scenes)
- [ ] **SCHEMA-02**: All devices with presets defined inline in `rig.yaml` (v1.2 single-file format)
- [ ] **SCHEMA-03**: Scenes defined under MC6 device `config.scenes` in `rig.yaml`
- [ ] **SCHEMA-04**: `signal-chain.yaml` and `devices/*.yaml` and `scenes/*.yaml` removed (superseded)
- [ ] **PRESET-01**: Mood MkII presets have real, musically meaningful parameter values and names
- [ ] **SCENE-01**: All scenes map to real preset combinations that work together musically
- [ ] **SCENE-02**: Scene descriptions reflect actual live use context

### Out of Scope

| Feature | Reason |
|---------|--------|
| New pedal/device additions | No new gear being added |
| rig-cli source changes | This is a rig config repo only |
| MC6 JSON generation | Deferred — validate first, generate in v1.3+ |
| HX Stomp preset content changes | HX presets stay as-is; schema migration only |

## Context

- rig-cli is at v1.2+ (single-file schema); `devices/` directory-based loading is no longer active
- Config repo has 9 YAML files across devices/ and scenes/ directories + signal-chain.yaml
- 3 devices: hx-stomp (HX Stomp), mood (Mood MkII), mc6 (MC6 controller)
- 4 scenes: clean, crunch, lead, wacky
- `rig validate` exits 0 but loads 0 devices/0 scenes — schema migration required for v1.2
- v1.2 schema: devices are an ordered list in rig.yaml (order = signal chain); scenes live inside MC6 `config.scenes`

## Constraints

- **Compatibility**: Must pass `rig validate` against rig-cli as installed at `~/dev/rig-cli`
- **Scope**: Config-only changes — no Python/rig-cli source modifications

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Version rig repo at v1.1 to match rig-cli counterpart | Keeps version numbers in sync across repos | ✓ Good — repo is now v1.1 |
| Rename `pedals/` → `devices/` rather than waiting | rig-cli TODOs signal `pedals/` will eventually break | ✓ Good — done, canonical naming in place |
| Defer single-file migration to v1.2 | v1.1 scope was naming migration only; schema format change is a bigger lift | — Pending v1.2 |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-06-08 — v1.2 milestone started*
