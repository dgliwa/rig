# Requirements: Derek's Guitar Rig

**Defined:** 2026-06-08
**Core Value:** A single `rig validate` should confirm the config repo is consistent and ready to apply — no guessing, no manual cross-referencing.

## v1.2 Requirements

### Schema Migration

- [ ] **SCHEMA-01**: `rig.yaml` has a `devices:` list with all devices (hx-stomp, mood, billy-strings-wombtone, brothers, mc6) and presets defined inline
- [ ] **SCHEMA-02**: Scenes defined under MC6 device `config.scenes` in `rig.yaml` (not in separate scenes/*.yaml files)
- [ ] **SCHEMA-03**: Obsolete files removed: `signal-chain.yaml`, `devices/*.yaml`, `scenes/*.yaml`
- [ ] **SCHEMA-04**: `rig validate` reports actual device and scene counts (not "0 devices/0 scenes")

### CBA Devices & Presets

- [x] **PRESET-01**: Mood MkII presets reviewed and verified as real, playable configurations
- [x] **PRESET-02**: Billy Strings Wombtone added as a new CBA device with at least one tuned preset
- [x] **PRESET-03**: Brothers added as a new CBA device with at least one tuned preset (AM style)
- [x] **PRESET-04**: Preset parameters annotated with sonic intent where values are non-obvious

### Real Scenes

- [ ] **SCENE-01**: All scenes have accurate descriptions reflecting actual live use context
- [ ] **SCENE-02**: Scenes incorporate Wombtone and Brothers where musically appropriate
- [ ] **SCENE-03**: MC6 bank/switch assignments reflect intended performance workflow

## Future Requirements

### v1.3+

- **GEN-01**: `rig generate mc6` produces valid MC6 bank JSON from scene definitions
- **GEN-02**: Generated MC6 JSON can be loaded onto hardware without manual editing

## Out of Scope

| Feature | Reason |
|---------|--------|
| rig-cli source changes | Config-only repo — no Python modifications |
| HX Stomp preset content changes | HX preset content (.hlx files) stays as-is; schema migration only |
| New physical gear beyond Wombtone + Brothers | No other new devices being added |
| MC6 JSON generation | Deferred to v1.3+ |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| SCHEMA-01 | Phase 2 | Pending |
| SCHEMA-02 | Phase 2 | Pending |
| SCHEMA-03 | Phase 2 | Pending |
| SCHEMA-04 | Phase 2 | Pending |
| PRESET-01 | Phase 3 | Complete |
| PRESET-02 | Phase 3 | Complete |
| PRESET-03 | Phase 3 | Complete |
| PRESET-04 | Phase 3 | Complete |
| SCENE-01 | Phase 4 | Pending |
| SCENE-02 | Phase 4 | Pending |
| SCENE-03 | Phase 4 | Pending |

**Coverage:**

- v1.2 requirements: 11 total
- Mapped to phases: 11
- Unmapped: 0 ✓

---
*Requirements defined: 2026-06-08*
*Last updated: 2026-06-08 — phase assignments added*
