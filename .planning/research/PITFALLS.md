# Domain Pitfalls: v1.3 90s Rock Preset Addition

**Domain:** rig.yaml config-only additions to an existing validated rig
**Researched:** 2026-06-18
**Confidence:** HIGH — all pitfalls derived from direct source inspection of rig-cli, rig_chasebliss/catalog.py, and the current rig.yaml

---

## Critical Pitfalls

Mistakes that break `rig validate` or cause silent apply failures.

---

### Pitfall 1: Scene References a Preset That Doesn't Exist Yet

**What goes wrong:** You add a `rock` scene to `mc6.config.scenes` before the presets it references (e.g. `brothers: kicking-distortion`) are uncommented/added to the device's `presets:` list. `_validate_references` in `loader.py` raises `MissingReferenceError` and validate fails with: `Scene 'rock': device 'brothers' has no preset 'kicking-distortion'`.

**Why it happens:** Scenes and presets are edited in two separate locations in rig.yaml (device preset list vs. mc6 config block). It's easy to add the scene first and forget to uncomment the preset — especially since all v1.2 presets are commented out.

**Consequences:** `rig validate` exits 1. The scene slot assignment in `mc6.config.banks` also fails because the scene doesn't load.

**Prevention:** Always add the preset block first, confirm it parses (run `rig validate`), then add the scene reference. Don't add the mc6 bank switch entry until both are done.

**Detection:** `rig validate` error: `Scene 'X': device 'Y' has no preset 'Z'`. Also check that the preset id string in the scene dict exactly matches the `id:` field in the device's preset block — ids are case-sensitive.

---

### Pitfall 2: Brothers AM `channel_2_bypass` Key Missing When Ch2 is Active

**What goes wrong:** The existing `transparent-lift` preset uses `channel_2_bypass: 0` to explicitly bypass Ch2. The new kicking-distortion preset uses both channels. If you omit `channel_2_bypass` entirely, `validate_cc_params` does NOT error (it's optional), but the pedal will retain whatever Ch2 bypass state it was last in — meaning Ch2 may be silently bypassed on hardware even though the YAML looks correct.

**Why it happens:** `channel_2_bypass` is not required. Pydantic `DigitalPreset` treats `parameters` as `dict[str, float | str | bool]` — missing keys are simply absent, not defaulted to anything.

**Consequences:** Silent apply failure — the rock scene loads, validate passes, but Ch2 does nothing at the gig.

**Prevention:** Explicitly set `channel_2_bypass: 0` (bypass off = Ch2 active) on any Brothers AM preset that uses Ch2. Explicitly set `channel_1_bypass: 0` on any preset that uses Ch1. Omission != off.

**Detection:** Preset passes validate but pedal behaves wrong. Check: does the preset have an explicit `channel_2_bypass` param? For kicking-distortion (dual-channel), both bypass params should be present and set to 0.

---

### Pitfall 3: Brothers AM Toggle Parameters Use Integer Indices, Not Strings

**What goes wrong:** `gain_1_type` and `gain_2_type` are TOGGLE controls with `positions=["boost", "od", "dist"]`. The catalog accepts either the string name (`"boost"`) or an integer index (`2` for `"dist"`). Existing presets use integers (e.g. `gain_1_type: 2`). If you mix styles — or use an integer that exceeds the positions list length (0, 1, 2 are valid; 3+ is not) — `validate_cc_params` raises: `'gain_1_type': 3 is out of range (0-2, as position index)`.

**Why it happens:** The YAML looks numeric. It's tempting to write `gain_2_type: 3` thinking "mode 3" — but the list is 0-indexed and has only 3 entries.

**Consequences:** `rig validate` fails with a position index out-of-range error.

**Prevention:** For Brothers AM `gain_N_type`: valid values are `0` (boost), `1` (od), `2` (dist). For kicking-distortion targeting 90s rock, use `gain_2_type: 2` (dist) or `gain_2_type: 1` (od). Never use 3+.

**Detection:** `rig validate` error: `'gain_2_type': X is out of range (0-2, as position index)`.

---

### Pitfall 4: Unknown Parameter Names Break Validation

**What goes wrong:** `validate_cc_params` in `rig_chasebliss/device.py` iterates every key in `parameters` and errors on any name not found in the device's catalog controls. If you write `dip_bypass: 1` instead of `channel_2_bypass: 0`, or `hi_gain_1: 1` instead of `dip_hi_gain_1: 1`, validation fails: `'hi_gain_1' is not a valid parameter for this device`.

**Why it happens:** The commented-out v1.2 presets (e.g. `bad-bros`) use `dip_hi_gain_1` and `dip_hi_gain_2` as the correct names. Copy-pasting from memory or shortening names is the error path.

**Consequences:** `rig validate` fails with an explicit unknown-parameter error — but only during `rig apply --setup`, not during `rig validate` alone. Wait — actually, `_detect_cba_setup_for_device` is called from `setup()`, not from `load_rig()`. `rig validate` only calls `load_rig()` which does NOT call `validate_cc_params`. **This means invalid parameter names pass `rig validate` but fail at `rig apply` time.**

**Prevention:** Cross-reference every parameter name against `BROTHERS_AM_CONTROLS` in `catalog.py`. Valid dip/toggle names for Brothers AM: `gain_2_type`, `treble_boost`, `dip_hi_gain_1`, `dip_hi_gain_2`, `dip_master`, `channel_1_bypass`, `channel_2_bypass`. Do not invent names.

**Detection:** Only surfaces at `rig apply` setup phase, not `rig validate`. The error message is explicit: `CBA preset validation failed for device 'brothers': 'X' is not a valid parameter for this device`.

---

### Pitfall 5: HX Stomp Preset Requires `hlx_file` — Missing File Doesn't Break Validate But Breaks Apply

**What goes wrong:** `HXStompPreset` has a required `hlx_file: str` field. If you add the Mesa Boogie rock preset to rig.yaml but point `hlx_file` to a path that doesn't exist on disk, `rig validate` still passes (it only checks YAML structure and cross-references, not file existence). But `rig apply` or any step that reads the hlx_file will fail silently or with a file-not-found error.

**Why it happens:** `HXStompPreset(**p)` in `device.py` does not check if the hlx_file path exists. The loader makes no filesystem checks on hlx_file paths.

**Consequences:** Validate passes, apply silently skips or crashes. The Mesa Boogie scene is wired but the patch never loads to the hardware.

**Prevention:** Confirm the `.hlx` file exists in `hlx/` before adding the preset. The `hlx/` directory already has `Boogie PB.hlx` and `Mesa Lead D.hlx` — use one of those exact filenames (including spaces and case). Write `hlx_file: hlx/Boogie PB.hlx` not `hlx_file: hlx/boogie_pb.hlx`.

**Detection:** `rig validate` gives no warning. Test by checking `ls hlx/` and verifying the exact filename used in `hlx_file` exists there.

---

### Pitfall 6: MC6 Scene Bank Switch References a Scene Name That Doesn't Match Exactly

**What goes wrong:** `mc6.config.banks[].switches.X.scene: rock` must match the exact key in `mc6.config.scenes:`. If the scene is defined as `rock:` but the bank switch says `scene: Rock` or `scene: rock-scene`, the `MC6Device.apply()` loop calls `rig.scenes.get(scene_name)` which returns `None`. No PC messages are programmed into that switch slot — the switch is cleared but left empty.

**Why it happens:** `apply()` does not raise on a missing scene name — it just silently skips PC generation for that switch. `rig validate`'s `_validate_references` does NOT check bank switch scene names against the scene dict, so this never surfaces as a validate error.

**Consequences:** Validate passes. The MC6 switch gets named "rock" but does nothing when pressed.

**Prevention:** Scene key in `mc6.config.scenes` and the `scene:` value in the bank switch must be identical strings. Use lowercase-hyphen convention consistently (e.g. `rock` everywhere).

**Detection:** After `rig apply`, press the switch — if no PCs fire, the scene name is mismatched. Add verbose logging (`rig apply -vv`) to see which scene_obj lookups return None.

---

## Moderate Pitfalls

---

### Pitfall 7: Preset Number Collision Between New and Existing Presets

**What goes wrong:** Brothers AM already has `transparent-lift` at `preset_number: 4`. If the new kicking-distortion preset is also assigned `preset_number: 4`, both CBA presets will overwrite the same hardware slot. The last one applied "wins." The other preset is silently gone.

**Why it happens:** `rig validate` and `_validate_references` do not check for duplicate `preset_number` values within a device. Pydantic's `DigitalPreset` model has no uniqueness constraint.

**Prevention:** Assign new presets to unused numbers. `transparent-lift` uses slot 4. Use slot 5 for kicking-distortion, slot 6 for envelope filter, etc. Commented-out v1.2 presets occupy slots 3-6 on Wombtone — check those too before assigning.

**Detection:** After apply, recall preset slot N — if it contains the wrong sound, there was a collision.

---

### Pitfall 8: Wombtone MkII Does Not Have Chorus — It's a Phaser/Vibrato

**What goes wrong:** The Wombtone MkII is a phaser/vibrato. Its wet_channel values are not "chorus/vibrato/tremolo" — the catalog defines it differently (see the actual catalog controls). Attempting to write a "chorus preset" for Wombtone by setting parameters that don't exist in `WOMBTONE_MKII_CONTROLS` will fail at apply time with unknown-parameter errors.

**Why it happens:** The feature description says "Mood MkII: chorus preset" but the Mood MkII is a delay/reverb/looper. The Wombtone is a modulation pedal. Chorus-like textures come from the Mood MkII (using its reverb/slip modes for shimmer), not from Wombtone.

**Prevention:** The Mood MkII chorus preset should use `wet_channel: 0` (reverb) or `wet_channel: 1` (delay) with appropriate `mix` and `wet_modify` settings. For Wombtone, target the phaser wet modes actually defined in `WOMBTONE_MKII_CONTROLS`. Verify parameter names against catalog before writing.

**Detection:** `rig apply` fails with `CBA preset validation failed` if using wrong param names for Wombtone.

---

### Pitfall 9: Mood MkII `wet_channel` Toggle Accepts 0/1/2 or Strings — Mixed Usage Causes Confusion

**What goes wrong:** `wet_channel` in `MOOD_MKII_CONTROLS` is a TOGGLE with `positions=["reverb", "delay", "slip"]`. Existing presets use integer indices (`wet_channel: 0` for reverb, `wet_channel: 1` for delay, `wet_channel: 2` for slip). If you write `wet_channel: "reverb"` in one preset and `wet_channel: 0` in another, both are valid but the inconsistency creates confusion when reading the file. More critically, using `wet_channel: 3` causes an out-of-range error at apply time.

**Prevention:** Pick one style (integers match existing presets) and stay consistent. Valid values: `0` = reverb, `1` = delay, `2` = slip. For a chorus-like texture, use `wet_channel: 1` (delay) with high `mix` and moderate `wet_modify` feedback, or `wet_channel: 0` (reverb) with heavy diffusion.

---

### Pitfall 10: `loop_clock_division` vs `wet_clock` — Wrong Parameter Name for Clock Subdivision

**What goes wrong:** Mood MkII has two separate clock division controls: `loop_clock_division` (for the looper section) and `wet_clock` (for the wet/reverb/delay section). They accept different position sets. Writing a chorus preset and specifying `clock_division: 5` instead of `loop_clock_division: 5` will fail at apply time — `clock_division` is not in `MOOD_MKII_CONTROLS`. The correct names are `loop_clock_division` and `wet_clock`.

**Prevention:** Use `loop_clock_division` for the loop side, `wet_clock` for the wet signal clock. Existing `ambient-reverse-tape` preset correctly uses `loop_clock_division: 5` — follow that pattern.

---

### Pitfall 11: YAML Indentation Error When Uncommenting Preset Blocks

**What goes wrong:** The commented-out presets in rig.yaml use `#` at the start of each line with variable indentation (some lines have extra spaces before `#`). When uncommenting a block by removing `#` characters, the resulting indentation may be off by one or two spaces, causing PyYAML to raise a `ParseError`. This breaks `rig validate` immediately with `Invalid YAML in rig.yaml`.

**Why it happens:** YAML is indentation-sensitive. The `#` comment prefix does not preserve the original leading spaces when re-inserted.

**Prevention:** After uncommenting, run `rig validate` immediately to catch parse errors before adding more changes. Use an editor with YAML syntax highlighting. When in doubt, the pattern is: preset list items start with 6 spaces (`      - id:`), sub-keys have 8 spaces.

**Detection:** `rig validate` error: `Invalid YAML in rig.yaml: ...` with a line number. Fix indentation at that line.

---

### Pitfall 12: Wombtone `dip_*` Parameter Values Must Be 0 or 127 — Not True/False/1

**What goes wrong:** Dipswitch parameters in `WOMBTONE_MKII_CONTROLS` (and all CBA pedals) use CC values where the hardware interprets anything above a threshold as "on." The catalog defines dips with `min=0, max=127`. The existing `slow-vibe` preset does not use any dips. If you add dip params for the envelope filter preset and write `dip_something: true` or `dip_something: 1`, `validate_cc_params` will accept `1` as in-range (0-127), but `true` will fail with `'X': 'True' is not a valid numeric value` at apply time.

**Prevention:** Use `0` for off, `127` for on — matching the pattern in the commented-out v1.2 presets (`dip_r_trails: 127`). Never use `true`/`false` for CBA dipswitches.

---

## Minor Pitfalls

---

### Pitfall 13: MC6 `banks` Switch Labels Must Match `SWITCH_INDEX` Keys

**What goes wrong:** `SWITCH_INDEX = {"A": 0, "B": 1, "C": 2, "D": 3, "E": 4, "F": 5}`. If you define a bank switch with a label outside this set (e.g. `G:` or `a:` lowercase), `MC6Device.apply()` logs a warning and skips it: `Unknown MC6 switch label 'G'`. No error, no validate failure — the switch is silently unprogrammed.

**Prevention:** Only use labels A-F. The existing config uses `A: scene: clean`. Add the rock scene under a new letter: `B: scene: rock`.

---

### Pitfall 14: `hlx_file` Path Is Relative to CWD, Not to rig.yaml Location

**What goes wrong:** `HXStompPreset` stores `hlx_file` as a plain string. Nothing in the current codebase resolves it relative to the yaml file. If `rig apply` is run from a directory other than the repo root, the hlx_file path will not resolve correctly. This is a latent issue — `rig validate` doesn't check paths, and `rig apply` currently doesn't read hlx_files at runtime either — but any future hlx-read feature will break if paths aren't repo-root-relative.

**Prevention:** Always use repo-root-relative paths: `hlx_file: hlx/Boogie PB.hlx`. Never use absolute paths or `./` prefixes.

---

### Pitfall 15: Adding a New Device ID That Duplicates an Existing ID

**What goes wrong:** `load_rig()` builds `devices` as a Python dict keyed on `device.id`. If two devices share the same `id`, the second silently overwrites the first. No error is raised. The signal chain still lists both, but `devices[id]` only contains the last one parsed.

**Why it matters here:** Not applicable to v1.3 (no new devices), but relevant if someone adds a second Brothers AM entry by accident.

**Prevention:** Not applicable — but note it as a silent failure mode in the system.

---

## Validate vs Apply: What Each Command Catches

This is the most important gap in the system for this milestone.

| Check | `rig validate` | `rig apply --setup` |
|-------|---------------|---------------------|
| YAML parse errors | YES — immediate | YES |
| Unknown device config type | YES | YES |
| Scene references unknown device | YES | YES |
| Scene references unknown preset id | YES | YES |
| Unknown parameter names (CBA) | **NO** | YES |
| Out-of-range parameter values (CBA) | **NO** | YES |
| Duplicate preset_number within device | **NO** | NO (silent overwrite) |
| `hlx_file` path existence | **NO** | NO (not read at apply time) |
| MC6 bank switch references undefined scene | **NO** | NO (silent skip) |
| `dip_*: true` instead of `dip_*: 127` | **NO** | YES (type error) |

**Key insight:** `rig validate` is necessary but not sufficient. The CBA parameter validation only runs during `rig apply --setup`. After adding new CBA presets, a dry-run apply (`rig apply --dry-run`) or careful manual audit is the only way to catch parameter-level errors before a live session.

---

## Phase-Specific Warnings

| Phase Topic | Likely Pitfall | Mitigation |
|-------------|---------------|------------|
| Brothers AM kicking-distortion preset | Unknown param names, channel_2_bypass missing, dip values using `true` instead of `127` | Add preset, run apply --dry-run, verify all param names against catalog |
| Brothers AM transparent boost (already exists) | Preset_number collision if new preset accidentally reuses 4 | Assign new preset a different slot (5+) |
| Mood MkII chorus preset | Wrong wet_channel value (>2), `clock_division` vs `loop_clock_division` confusion | Use integer indices; wet_channel max is 2; check existing ambient-reverse-tape for naming patterns |
| Wombtone envelope filter preset | Dip params using boolean instead of 127/0; wrong parameter names; "chorus" params don't exist on Wombtone | Audit WOMBTONE_MKII_CONTROLS in catalog.py before writing any params |
| HX Stomp Mesa Boogie patch | hlx_file pointing to nonexistent file (filename case/spaces); validate passes but apply silently fails | Verify exact filename in `hlx/` dir before adding preset |
| Rock scene (mc6.config.scenes) | Adding scene before adding presets; id mismatch between scene preset map and device preset block | Add presets first, validate, then add scene |
| MC6 bank switch assignment | Scene name case mismatch (silent skip); switch label outside A-F | Use exact matching scene key; use uppercase A-F only |

---

*Pitfalls research for: v1.3 90s Rock Sound — Derek's Guitar Rig*
*Source confidence: HIGH — derived from direct rig-cli source inspection (loader.py, device.py, catalog.py, sysex.py) and current rig.yaml.*
