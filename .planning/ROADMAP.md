# Roadmap: Derek's Guitar Rig

## Milestones

- ✅ **v1.1 Architecture Migration** — Phase 1 (shipped 2026-06-08)
- ✅ **v1.2 Flesh Out the Rig** — Phases 2-4 (shipped 2026-06-09)
- 🚧 **v1.3 90s Rock Sound** — Phases 5-7 (in progress)

## Phases

<details>
<summary>✅ v1.1 Architecture Migration — SHIPPED 2026-06-08</summary>

- [x] Phase 1: Migrate & Validate (1/1 plans) — completed 2026-06-07

See: `.planning/milestones/v1.1-ROADMAP.md`

</details>

<details>
<summary>✅ v1.2 Flesh Out the Rig — SHIPPED 2026-06-09</summary>

- [x] Phase 2: Schema Migration (1/1 plans) — completed 2026-06-08
- [x] Phase 3: CBA Devices & Presets (1/1 plans) — completed 2026-06-09
- [x] Phase 4: Real Scenes (1/1 plans) — completed 2026-06-09

See: `.planning/milestones/v1.2-ROADMAP.md`

</details>

### 🚧 v1.3 90s Rock Sound (In Progress)

**Milestone Goal:** Wire all four devices with Weezer-style 90s rock presets and a new HX Stomp Mesa Boogie scene accessible via MC6 Bank 1 Switch B.

- [ ] **Phase 5: CBA Device Presets** - Add rock-drive, chorus-shimmer, and envelope-filter presets for Brothers, Mood, and Wombtone
- [ ] **Phase 6: HX Stomp Mesa-Rock Entry** - Add mesa-rock preset entry in rig.yaml pointing to user-supplied .hlx patch
- [ ] **Phase 7: Rock Scene & MC6 Wiring** - Create rock scene wiring all devices and map it to MC6 Bank 1 Switch B

## Phase Details

### Phase 5: CBA Device Presets
**Goal**: All three CBA pedals have rock-ready presets defined in rig.yaml
**Depends on**: Phase 4
**Requirements**: BROTHERS-01, MOOD-01, WOMBTONE-01
**Success Criteria** (what must be TRUE):
  1. User can see a `rock-drive` Brothers preset with Ch1 transparent boost (louder than transparent-lift) and Ch2 kicking DIST distortion
  2. User can see a `chorus-shimmer` Mood preset configured for short-delay chorus-like shimmer on the wet channel
  3. User can see an `envelope-filter` Wombtone preset configured for cocked-wah phaser character (high resonance, deep slow sweep)
  4. `rig validate` passes with all three new CBA presets in place
**Plans**: TBD

### Phase 6: HX Stomp Mesa-Rock Entry
**Goal**: rig.yaml contains a mesa-rock preset entry for the HX Stomp device
**Depends on**: Phase 4
**Requirements**: HX-01
**Success Criteria** (what must be TRUE):
  1. User can see a `mesa-rock` preset entry under the hx-stomp device in rig.yaml with a valid `hlx_file` path pointing to the user-provided `.hlx` file
  2. `rig validate` accepts the mesa-rock entry without errors (file-existence check is advisory, not blocking)
**Plans**: TBD

### Phase 7: Rock Scene & MC6 Wiring
**Goal**: Users can load the rock scene from MC6 Bank 1 Switch B, activating all four devices with their rock presets
**Depends on**: Phase 5, Phase 6
**Requirements**: SCENE-01, SCENE-02, SCENE-03
**Success Criteria** (what must be TRUE):
  1. A `rock` scene exists in mc6.config.scenes that references rock presets for brothers, wombtone, hx-stomp, and mood
  2. MC6 Bank 1 Switch B is mapped to the `rock` scene in rig.yaml
  3. `rig validate` passes reporting all devices, presets, and scenes with no cross-reference errors
**Plans**: TBD

## Progress

| Phase | Milestone | Plans Complete | Status | Completed |
|-------|-----------|----------------|--------|-----------|
| 1. Migrate & Validate | v1.1 | 1/1 | Complete | 2026-06-07 |
| 2. Schema Migration | v1.2 | 1/1 | Complete | 2026-06-08 |
| 3. CBA Devices & Presets | v1.2 | 1/1 | Complete | 2026-06-09 |
| 4. Real Scenes | v1.2 | 1/1 | Complete | 2026-06-09 |
| 5. CBA Device Presets | v1.3 | 0/1 | Not started | - |
| 6. HX Stomp Mesa-Rock Entry | v1.3 | 0/1 | Not started | - |
| 7. Rock Scene & MC6 Wiring | v1.3 | 0/1 | Not started | - |
