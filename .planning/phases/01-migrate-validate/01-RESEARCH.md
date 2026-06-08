# Research: Phase 1 — Migrate & Validate

**Generated:** 2026-06-07
**Phase:** 1 — Migrate & Validate

---

## Summary

This phase migrates a rig config repo from rig-cli's old structure to its current canonical structure. The migration is mechanical (rename directory, change field names, fix preset parameters) but requires careful ordering. One blocking validation issue was discovered: the in-progress `mood.yaml` changes use simplified DIP parameter names that don't match the rig-chasebliss catalog.

---

## rig-cli Architecture (from source)

**Two installations exist on this machine:**

| Binary | Version | Source |
|--------|---------|--------|
| `/Users/derekgliwa/.local/bin/rig` | Old (uv-tools) | Uses `pedals/` dir + root `mc6.yaml` — NOT the target |
| `rig` (mise shim) | Dev/editable | `/Users/derekgliwa/dev/rig-cli` — THIS is the authoritative install |

The REQUIREMENTS say "current local rig-cli install" — that means the **editable dev version** (`/Users/derekgliwa/dev/rig-cli`), not the uv-tools release.

**Canonical paths in dev rig-cli (`packages/rig/src/rig/config/loader.py`):**
```python
devices_dir = _resolve(root, "devices")
if not devices_dir.is_dir():
    devices_dir = _resolve(root, "pedals")  # fallback — remove in future
```
→ `devices/` is the canonical dir; `pedals/` still works but is legacy.

**Signal chain field (`packages/rig/src/rig/models/signal_chain.py`):**
```python
device_ref: str = Field(validation_alias=AliasChoices("device", "pedal", "pedal_ref"))
```
→ Both `device:` and `pedal:` are accepted; `device:` is the canonical form.

**mc6 device:** In the new architecture, `mc6.yaml` lives in `devices/` as a `type: controller` device. The old version expected `mc6.yaml` at the repo root — that's already removed.

---

## Current Working Tree State

```
D  mc6.yaml              # deleted from root (already moved to pedals/)
M  pedals/mood.yaml      # preset IDs + parameters revised
M  scenes/clean.yaml     # updated presets + description
M  scenes/crunch.yaml    # updated presets + description
M  scenes/lead.yaml      # updated presets + description
M  scenes/wacky.yaml     # updated presets + description
?? pedals/mc6.yaml       # new controller device file (untracked)
```

`signal-chain.yaml` currently uses `pedal:` field for all 3 entries.

---

## Blocking Issue: Mood Preset DIP Parameter Names

The new `pedals/mood.yaml` uses simplified DIP names that **do not exist** in the `rig-chasebliss` catalog:

| Parameter in mood.yaml | Correct catalog name | DIP bank |
|------------------------|---------------------|----------|
| `dip_spread` | `dip_r_spread` | Right |
| `dip_trails` | `dip_r_trails` | Right |
| `dip_smooth` | `dip_r_smooth` | Right |
| `dip_bounce` | `dip_l_bounce` | Left |
| `dip_sweep` | `dip_l_sweep` | Left |

The catalog (`rig-chasebliss/src/rig_chasebliss/catalog.py`) uses `dip_l_*` (left bank) and `dip_r_*` (right bank) naming. `rig validate` runs `_validate_references` which checks `set(preset.parameters) - control_names` — unknown params raise a `ValidationError`.

**Full valid control names for Mood MkII:** time, mix, length, wet_modify, clock, loop_modify, ramp_speed, wet_channel, routing, loop, wet_clock, loop_clock_division, freeze, tap_tempo, true_bypass, wet_bypass, micro_bypass, loop_overdub, dip_l_bounce, dip_l_clock, dip_l_length, dip_l_loop_modify, dip_l_polarity, dip_l_sweep, dip_l_time, dip_l_wet_modify, dip_r_classic, dip_r_dry_kill, dip_r_latch, dip_r_miso, dip_r_no_dub, dip_r_smooth, dip_r_spread, dip_r_trails.

---

## Migration Sequence (Correct Order)

### Step 1: Fix mood.yaml DIP parameters (prerequisite for MIGS-03)
Rename simplified DIP names → catalog names in `pedals/mood.yaml`:
- `dip_spread` → `dip_r_spread`
- `dip_trails` → `dip_r_trails`
- `dip_smooth` → `dip_r_smooth`
- `dip_bounce` → `dip_l_bounce`
- `dip_sweep` → `dip_l_sweep`

### Step 2: Commit all in-progress changes (MIGS-03)
Stage + commit in logical groups:
1. `pedals/mc6.yaml` + deleted `mc6.yaml` (controller device move)
2. `pedals/mood.yaml` (preset revisions with correct param names)
3. `scenes/*.yaml` (scene revisions)

### Step 3: Rename `pedals/` → `devices/` (MIGS-01)
Use `git mv pedals devices` — preserves git history for each file.

### Step 4: Update `signal-chain.yaml` (MIGS-02)
Replace all `pedal:` field references with `device:` in the 3 chain entries.

### Step 5: Commit migration changes
Single commit: directory rename + signal-chain update.

### Step 6: Run `rig validate` (MIGS-04)
Use the mise shim: `rig validate` (not `/Users/derekgliwa/.local/bin/rig`).
Expected: zero errors.

---

## Risks / Gotchas

1. **Wrong rig binary**: `/Users/derekgliwa/.local/bin/rig` is the old uv-tools install with a different schema — it will always fail against the new structure. Must use the mise-shimmed `rig` (editable dev install).

2. **`git mv pedals devices`** must be done as a single atomic operation to preserve history. Do not `rm -rf pedals && mkdir devices && cp`.

3. **scenes reference device IDs** (e.g., `hx-stomp`, `mood`) — these are the `id:` fields from the device YAML files, not filenames. The rename to `devices/` doesn't change device IDs, so scene references remain valid.

4. **`signal-chain.yaml` has 3 entries** all using `pedal:` — all 3 need updating to `device:`. The controller (mc6) is at position 3.

5. **No `devices/` directory exists yet** — the rename creates it. Don't pre-create it.

---

## Verification Commands

```bash
# Confirm no pedal: references remain
grep -r "pedal:" signal-chain.yaml

# Confirm devices/ exists and pedals/ is gone
ls -la

# Confirm mc6 controller is in devices/
ls devices/

# Run validation (use mise rig, not uv rig)
rig validate

# Confirm clean working tree
git status
```
