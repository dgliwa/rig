# Derek's Guitar Rig

## What This Is

Infrastructure-as-Code configuration for a personal guitar rig (HX Stomp + Mood MkII + MC6 controller). YAML files declare pedals, presets, and scenes; rig-cli validates, plans, and applies those configs to physical MIDI devices. One person, one rig, fully git-tracked.

## Core Value

A single `rig validate` should confirm the config repo is consistent and ready to apply — no guessing, no manual cross-referencing.

## Current Milestone: v1.2 Architecture Migration

**Goal:** Migrate rig configs to match rig-cli's current (post-v1.1) preferred structure so `rig validate` passes cleanly and the repo is free of deprecated conventions.

**Target features:**
- Rename `pedals/` → `devices/` to match rig-cli's preferred directory name
- Update `signal-chain.yaml` to use `device:` instead of legacy `pedal:` field
- Commit in-progress changes (mc6 moved to pedals/, Mood presets revised, scenes revised)
- Validate cleanly against current rig-cli codebase

## Requirements

### Validated

(None yet — first milestone)

### Active

- [ ] **MIGS-01**: `pedals/` directory renamed to `devices/` so rig-cli uses preferred path
- [ ] **MIGS-02**: `signal-chain.yaml` uses `device:` field for all chain entries
- [ ] **MIGS-03**: In-progress work committed — mc6 in devices/, revised Mood presets, revised scenes
- [ ] **MIGS-04**: `rig validate` passes with zero errors against current rig-cli

### Out of Scope

| Feature | Reason |
|---------|--------|
| Adding new scenes | Scope is migration only — content stays, structure transforms |
| New pedal/device additions | No new gear being added in this milestone |
| rig-cli source changes | This is a rig config repo milestone only |

## Context

- rig-cli is at v1.1 (plugin architecture shipped 2026-06-07); v1.2 is next
- The loader prefers `devices/` over `pedals/` and has a TODO to drop `pedals/` support
- Signal chain `pedal:` field still works via AliasChoices but `device:` is canonical
- Uncommitted changes already exist: mc6.yaml moved from root → pedals/, Mood presets updated (actual MIDI parameters from physical pedal), scene descriptions revised
- 4 active scenes: clean (Princeton + Second Guitarist), crunch (Marshall + Ambient Pad), lead (Boogie + Broken Tape), wacky (Doogie + Sigur Ros Drone)

## Constraints

- **Compatibility**: Must pass `rig validate` against rig-cli as installed at `~/dev/rig-cli`
- **Scope**: Config-only changes — no Python/rig-cli source modifications

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Version rig repo at v1.2 to match rig-cli counterpart | Keeps version numbers in sync across repos | — Pending |
| Rename pedals/ → devices/ rather than waiting | rig-cli TODOs signal pedals/ will eventually break | — Pending |

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
*Last updated: 2026-06-07 after milestone v1.2 initialized*
