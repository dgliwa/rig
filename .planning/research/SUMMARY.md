# Project Research Summary

**Project:** Guitar Rig v1.3 — 90s Rock Sound
**Domain:** rig.yaml config-only additions (Chase Bliss + HX Stomp)
**Researched:** 2026-06-18
**Confidence:** HIGH

## Executive Summary

This milestone is a pure configuration exercise: add four new device presets and one new scene to an existing, validated rig.yaml. No new tooling, no rig-cli source changes, no new devices. The target sound is Weezer-style 90s rock — Brothers AM handling dual-channel drive (Ch1 transparent boost + Ch2 kicking distortion), HX Stomp providing the Mesa Boogie amp voice, Mood MkII approximating chorus shimmer, and Wombtone MkII in a static cocked-wah phaser position. A new MC6 scene ties all four together under Bank 1 Switch B.

The recommended build order is strict: device presets first, validated individually, then the rock scene, then the MC6 bank switch. This order is not stylistic — it is enforced by `rig validate`'s cross-reference checks. The HX Stomp preset specifically requires its `.hlx` file to exist in `hlx/` before the yaml entry is added, or `rig apply` will silently fail (validate passes, apply skips the patch load).

The primary risk is the gap between `rig validate` and `rig apply --setup`: five categories of errors (unknown CBA parameter names, out-of-range values, duplicate preset numbers, missing hlx_file paths, and mismatched MC6 scene name references) pass validation silently but fail or misbehave at apply time. The mitigation is to run `rig apply --dry-run` after adding each preset block, not just `rig validate`.

## Key Findings

### Recommended Stack

No new stack. All work is rig.yaml edits plus one new `.hlx` file created in HX Edit. The rig-cli v1.2 schema supports everything needed.

**Core technologies:**
- **rig.yaml (v1.2 schema):** Single source of truth for all device presets and scene wiring
- **rig-cli:** Validates and applies config via MIDI; `rig validate` + `rig apply --dry-run` are the test harness
- **HX Edit:** Creates `hlx/Mesa Rock.hlx` (or reuses an existing patch); the .hlx file is referenced but not read by rig-cli at apply time

**Critical parameter reference:**
- Brothers AM `gain_N_type`: 0=boost, 1=od, 2=dist (0-indexed; never write 3+)
- Brothers AM `channel_N_bypass: 0` means active (not bypassed); omission is not the same as off
- Wombtone MkII `rate: 0` freezes phase position (static cocked-wah); high `feed` (96+) adds resonance
- Mood MkII `wet_channel`: 0=reverb, 1=delay, 2=slip; chorus-like texture uses `wet_channel: 1` with short time and even mix
- CBA dipswitches use `0` (off) / `127` (on) — never `true`/`false`/`1`

### Expected Features

**Must have (table stakes):**
- **Brothers rock-boost preset** — dual-channel: Ch1 transparent boost + Ch2 kicking distortion (`gain_2_type: 2`, gain ~76-96, `dip_master: 1`)
- **HX Stomp Mesa rock patch** — Mesa Boogie amp model + 4x12 cab; new `.hlx` file in `hlx/`
- **Rock scene** — MC6 Bank 1 Switch B wiring all four device presets together

**Should have (differentiators):**
- **Mood MkII weezer-chorus preset** — delay-mode (`wet_channel: 1`) with short time and ~50-64 mix for shimmer
- **Wombtone wah-phaser preset** — `rate: 0`, `feed: 96+`, deep `depth` for frozen cocked-wah mid-peak

**Defer (v2+):**
- Chorus variants (deeper/shallower) — tune after hearing in context with the full signal chain

### Architecture Approach

All changes slot into the existing rig.yaml structure. Each device gets one new `- id:` entry in its `presets:` list; the MC6 `config.scenes` gets a new `rock:` block referencing those preset IDs; and Bank 1 `switches` gets a `B: scene: rock` entry. The `.hlx` directory already contains precedent files (`Boogie PB.hlx`, `Mesa Lead D.hlx`).

**Major components:**
1. **Device preset blocks (x4)** — declare parameters for one sound per device; live under each device's `presets:` list
2. **Scene block** — wires one preset per device into a named snapshot under `mc6.config.scenes`
3. **MC6 bank switch** — maps footswitch B to the rock scene under `mc6.config.banks[0].switches`
4. **`.hlx` file** — the actual HX Stomp patch; created in HX Edit, saved to `hlx/`; must exist before the rig.yaml entry is written

### Critical Pitfalls

1. **Scene added before presets exist** — `rig validate` raises `MissingReferenceError`. Fix: always add device presets first, run `rig validate`, then add the scene.
2. **`channel_2_bypass` omitted on Brothers dual-channel preset** — passes validate, silently leaves Ch2 in whatever hardware state it was last in. Fix: explicitly set `channel_1_bypass: 0` and `channel_2_bypass: 0` on any Brothers preset using both channels.
3. **`rig validate` does not catch CBA parameter errors** — unknown param names and out-of-range values only surface during `rig apply --setup`. Fix: run `rig apply --dry-run` after each new preset addition; cross-reference param names against `catalog.py`.
4. **MC6 scene name case mismatch** — `scene: Rock` vs `scene: rock` silently programs an empty switch. Fix: use identical lowercase strings everywhere.
5. **`hlx_file` path wrong (case/spaces)** — validate passes, apply silently skips. Fix: run `ls hlx/` and copy the exact filename before writing the rig.yaml entry.

## Implications for Roadmap

Based on research, the architecture mandates a strict build order. All work is low-complexity YAML config, so phases can be small and validated individually.

### Phase 1: CBA Device Presets (Brothers + Wombtone + Mood)

**Rationale:** These three have no external file dependencies — add, validate, dry-run apply. They are the prerequisite inputs for the rock scene. Grouped together because all three use the same CBA parameter validation path and share the same pitfall pattern (unknown param names, dip values).

**Delivers:** `rock-boost` (Brothers), `wah-phaser` (Wombtone), `weezer-chorus` (Mood) preset entries in rig.yaml, each passing `rig validate` + `rig apply --dry-run`.

**Addresses:** Brothers rock-boost (table stakes), Wombtone wah-phaser (differentiator), Mood chorus (differentiator)

**Avoids:** Pitfall: missing channel_2_bypass; gain_type index out of range; unknown param names (only caught at apply); Wombtone has no chorus params; Mood wet_channel confusion; dip values as booleans.

### Phase 2: HX Stomp Mesa Rock Patch

**Rationale:** Requires creating the `.hlx` file in HX Edit first — a hardware/external-app step that must precede the rig.yaml entry. Isolated from the CBA devices (different validation path, different failure modes).

**Delivers:** `hlx/Mesa Rock.hlx` on disk + `mesa-rock` preset entry in rig.yaml for the hx-stomp device.

**Uses:** HX Edit app (external), existing `hlx/` directory convention

**Avoids:** hlx_file path existence trap (validate passes but apply fails); repo-root-relative path requirement.

### Phase 3: Rock Scene + MC6 Bank 1 Switch B

**Rationale:** Final assembly. Cannot be done until all four device presets exist and validate cleanly. This phase has the most silent-failure traps (scene name mismatches, switch label case).

**Delivers:** `rock:` scene in `mc6.config.scenes`, `B: scene: rock` in Bank 1 switches — making the rock sound recallable from a single footswitch press.

**Avoids:** Scene-before-presets ordering error; MC6 scene name case mismatch; switch label outside A-F.

### Phase Ordering Rationale

- Presets before scenes is enforced by `_validate_references` in loader.py — not optional.
- HX Stomp is isolated to its own phase because the `.hlx` file creation is a manual hardware step with different tooling from CBA text-only edits.
- Scene wiring is last because it depends on all four presets existing and validated.
- Running `rig validate` + `rig apply --dry-run` after each phase is the integration test harness. There is no other way to catch CBA parameter errors before hardware.

### Research Flags

Phases with standard patterns (skip research-phase):
- **Phase 1 (CBA Presets):** Well-documented; catalog.py is the authoritative source; PITFALLS.md covers all known failure modes.
- **Phase 2 (HX Stomp):** Standard pattern; existing `Boogie PB.hlx` in repo is the precedent; consider reusing it instead of creating a new patch.
- **Phase 3 (Scene + MC6):** Mechanical wiring step; ARCHITECTURE.md provides the exact yaml snippet needed.

No phases require `--research-phase` deeper research. The PITFALLS.md was generated from direct rig-cli source inspection — confidence is HIGH on all failure modes.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | No new stack; rig-cli source inspected directly |
| Features | HIGH | Grounded in existing rig.yaml patterns + Weezer tonal reference |
| Architecture | HIGH | Derived from live rig.yaml structure and PROJECT.md design intent |
| Pitfalls | HIGH | Sourced from rig-cli loader.py, device.py, catalog.py direct inspection |

**Overall confidence:** HIGH

### Gaps to Address

- **`gain_2_type` value for dist:** Index 2 = dist per catalog.py — but verify against the existing `bad-bros` commented preset in rig.yaml before writing. Low risk; easy to confirm.
- **HX Stomp `.hlx` reuse vs. new:** `hlx/Mesa Lead D.hlx` may already approximate the desired tone. Check before opening HX Edit — saves a hardware session.
- **Brothers `transparent-lift` preset_number collision:** The new `rock-boost` dual-channel preset must use a slot other than 4 (already taken). Assign slot 5+.

## Sources

### Primary (HIGH confidence)
- `rig.yaml` — current device schemas, existing preset patterns, MC6 bank structure
- rig-cli source (`loader.py`, `device.py`, `catalog.py`, `sysex.py`) — validation behavior, parameter names, apply vs validate gap
- `PROJECT.md` — MC6 Bank 1 intended layout (A=clean, B=crunch, C=lead, D=wacky)

### Secondary (MEDIUM confidence)
- Chase Bliss Audio Brothers AM manual — gain_type position values (corroborated by catalog.py)
- HX Edit — Mesa/Boogie amp model availability

### Tertiary (LOW confidence)
- Weezer (Blue Album, Pinkerton) tonal reference — Rivers Cuomo used Mesa/Boogie Dual Rectifier + CE-1 chorus; informs preset target sound but does not constrain YAML values

---
*Research completed: 2026-06-18*
*Ready for roadmap: yes*
