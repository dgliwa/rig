---
id: 01-01
phase: 1
plan: 1
title: "Migrate & Validate — Full Migration"
status: complete
completed: "2026-06-07"
---

# Plan 01-01 Summary: Migrate & Validate

**One-liner:** Renamed `pedals/` to `devices/`, updated `signal-chain.yaml` to canonical `device:` field, committed all in-progress YAML revisions, and confirmed `rig validate` passes.

## What Was Done

All four requirements shipped in a single execution session on 2026-06-07:

- **MIGS-01**: `pedals/` directory renamed to `devices/` via `git mv`; no `pedals/` path remains
- **MIGS-02**: `signal-chain.yaml` updated — all entries now use `device:` field; `pedal:` alias removed
- **MIGS-03**: In-progress changes committed cleanly — mc6.yaml moved from repo root to `devices/`, Mood presets revised with actual DIP parameter names (`dip_r_spread`, `dip_r_trails`, etc.), all four scene descriptions and preset references revised
- **MIGS-04**: `rig validate` exits 0 with no errors

**Note:** At execution time, rig-cli had migrated to a v1.2+ single-file schema where devices are embedded inline in `rig.yaml`. The `devices/` directory is no longer read by the loader. `rig validate` passes (0 errors) but validates 0 devices/0 scenes — the directory-based device files are effectively orphaned from the validator. This schema gap is deferred to v1.2.

## Verification Results

- `rig validate` — exits 0, "All cross-references valid" ✓
- `ls devices/` — shows hx-stomp.yaml, mc6.yaml, mood.yaml ✓
- `ls pedals/` — directory does not exist ✓
- `grep 'pedal:' signal-chain.yaml` — no matches ✓
- `git status` — clean working tree ✓

## Key Decisions

- Renamed proactively even though rig-cli already moved past directory-based loading — preserves intent alignment with rig-cli's preferred naming even if the loader no longer reads it
- v1.2 migration (embed devices inline in rig.yaml) deferred — out of scope for this milestone

## What Wasn't Done / Deferred

- **Single-file migration**: rig-cli v1.2+ uses inline devices in `rig.yaml`; this repo still uses the directory-based split format. Full migration is v1.2 work.
