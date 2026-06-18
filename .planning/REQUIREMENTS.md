# Requirements: Derek's Guitar Rig

**Defined:** 2026-06-18
**Core Value:** A single `rig validate` confirms the config repo is consistent and ready to apply

## v1.3 Requirements

### Brothers AM

- [x] **BROTHERS-01**: User can engage a `rock-drive` Brothers preset that activates Ch1 transparent boost (louder than `transparent-lift`) and Ch2 kicking DIST distortion as a single dual-channel preset

### Mood MkII

- [x] **MOOD-01**: User can engage a `chorus-shimmer` Mood preset that adds a chorus-like shimmer (short-delay path) to the rock signal chain

### Wombtone MkII

- [x] **WOMBTONE-01**: User can engage an `envelope-filter` Wombtone preset that delivers a cocked-wah phaser character (high resonance, deep slow sweep)

### HX Stomp

- [ ] **HX-01**: rig.yaml contains a `mesa-rock` HX Stomp preset entry pointing to a user-provided `.hlx` file (file is created manually by user in HX Edit; this requirement covers the rig.yaml entry only)

### Rock Scene

- [ ] **SCENE-01**: A `rock` scene exists in mc6.config.scenes wiring all four devices (brothers, wombtone, hx-stomp, mood) to their rock presets
- [ ] **SCENE-02**: MC6 Bank 1 Switch B is mapped to the `rock` scene
- [ ] **SCENE-03**: `rig validate` passes with all new presets and the rock scene in place

## Future Requirements

### Wombtone Auto-Wah (True)

- **WOMBTONE-02**: User can trigger a true envelope-filter auto-wah that tracks pick attack dynamically (requires expression pedal + MC6 CC mapping — deferred; Wombtone has no onboard envelope follower)

## Out of Scope

| Feature | Reason |
|---------|--------|
| Creating the HX Stomp `.hlx` patch file | User configures in HX Edit directly; out of scope for config-only rig repo |
| Brothers Ch1 boost as a standalone scene preset | Dual-channel rock-drive covers this; separate boost preset is redundant for v1.3 |
| High-gain fuzz / maxed Brothers | Research flagged this as anti-feature (overwhelms Mesa amp model) |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| BROTHERS-01 | Phase 5 | Complete |
| MOOD-01 | Phase 5 | Complete |
| WOMBTONE-01 | Phase 5 | Complete |
| HX-01 | Phase 6 | Pending |
| SCENE-01 | Phase 7 | Pending |
| SCENE-02 | Phase 7 | Pending |
| SCENE-03 | Phase 7 | Pending |

**Coverage:**

- v1.3 requirements: 7 total
- Mapped to phases: 7
- Unmapped: 0

---
*Requirements defined: 2026-06-18*
*Last updated: 2026-06-18 — traceability mapped to phases 5-7*
