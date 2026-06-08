# Roadmap: Derek's Guitar Rig

## Milestones

- ✅ **v1.1 Architecture Migration** — Phase 1 (shipped 2026-06-08)
- 📋 **v1.2 Flesh Out the Rig** — Phases 2-4 (migrate schema, add CBA devices, build real scenes)

## Phases

<details>
<summary>✅ v1.1 Architecture Migration — SHIPPED 2026-06-08</summary>

- [x] Phase 1: Migrate & Validate (1/1 plans) — completed 2026-06-07

See: `.planning/milestones/v1.1-ROADMAP.md`

</details>

### v1.2 Flesh Out the Rig

- [ ] **Phase 2: Schema Migration** — Collapse all YAML files into single `rig.yaml`; `rig validate` reports real counts
- [ ] **Phase 3: CBA Devices & Presets** — Add Wombtone and Brothers devices with tuned presets; audit Mood presets
- [ ] **Phase 4: Real Scenes** — Build scenes that use all devices meaningfully with accurate MC6 assignments

## Phase Details

### Phase 2: Schema Migration
**Goal**: `rig.yaml` is the single source of truth and `rig validate` confirms it
**Depends on**: Phase 1 (complete)
**Requirements**: SCHEMA-01, SCHEMA-02, SCHEMA-03, SCHEMA-04
**Success Criteria** (what must be TRUE):
  1. `rig.yaml` contains a `devices:` list with all devices and their presets defined inline
  2. Scenes are defined under the MC6 device's `config.scenes` key in `rig.yaml` (no separate scenes files)
  3. `signal-chain.yaml`, `devices/*.yaml`, and `scenes/*.yaml` are deleted from the repo
  4. `rig validate` exits 0 and prints the actual device count and scene count (not "0 devices/0 scenes")
**Plans**: TBD

### Phase 3: CBA Devices & Presets
**Goal**: All CBA pedals (Mood, Wombtone, Brothers) have real, playable presets with clear intent
**Depends on**: Phase 2
**Requirements**: PRESET-01, PRESET-02, PRESET-03, PRESET-04
**Success Criteria** (what must be TRUE):
  1. Billy Strings Wombtone appears as a device in `rig.yaml` with at least one preset covering a distinct tremolo/vibe tone
  2. Brothers appears as a device in `rig.yaml` with at least one preset covering an AM-style overdrive tone
  3. Mood MkII presets have been reviewed; any placeholder values are replaced with real parameter values
  4. Each preset with non-obvious parameter values has an inline comment or annotation explaining the sonic intent
**Plans**: TBD

### Phase 4: Real Scenes
**Goal**: Every scene reflects an actual live context and MC6 assignments match performance intent
**Depends on**: Phase 3
**Requirements**: SCENE-01, SCENE-02, SCENE-03
**Success Criteria** (what must be TRUE):
  1. Every scene has a description that names the musical context it covers (e.g., "clean foundation", "lead with wash")
  2. At least one scene uses Wombtone and at least one scene uses Brothers where musically appropriate
  3. MC6 bank and switch assignments are ordered to match the performance workflow (most-used first, logical groupings)
**Plans**: TBD

## Progress

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 1. Migrate & Validate | v1.1 | 1/1 | Complete | 2026-06-07 |
| 2. Schema Migration | v1.2 | 0/? | Not started | - |
| 3. CBA Devices & Presets | v1.2 | 0/? | Not started | - |
| 4. Real Scenes | v1.2 | 0/? | Not started | - |
