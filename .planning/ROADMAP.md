# Roadmap: Derek's Guitar Rig

## Milestones

- 🚧 **v1.2 Architecture Migration** - Phase 1 (in progress)

## Phases

### 🚧 v1.2 Architecture Migration (In Progress)

**Milestone Goal:** Migrate rig configs to rig-cli's current preferred structure so `rig validate` passes cleanly with no deprecated conventions.

- [ ] **Phase 1: Migrate & Validate** - Commit in-progress work, rename `pedals/` to `devices/`, update signal-chain field names, and confirm `rig validate` passes

## Phase Details

### Phase 1: Migrate & Validate
**Goal**: The config repo uses rig-cli's canonical structure and passes validation cleanly
**Depends on**: Nothing (first phase)
**Requirements**: MIGS-01, MIGS-02, MIGS-03, MIGS-04
**Success Criteria** (what must be TRUE):
  1. The `devices/` directory exists and contains all device YAML files; no `pedals/` directory remains
  2. Every entry in `signal-chain.yaml` uses the `device:` field — no `pedal:` field references remain
  3. All in-progress changes (mc6.yaml location, Mood preset values, scene revisions) are committed to git with clean working tree
  4. `rig validate` exits with zero errors against the local rig-cli install
**Plans**: 1 (01-PLAN.md — Migrate & Validate Full Migration)

## Progress

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 1. Migrate & Validate | v1.2 | 0/1 | Planned | - |
