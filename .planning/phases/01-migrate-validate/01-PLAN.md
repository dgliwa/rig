---
id: 01-01
phase: 1
wave: 1
title: "Migrate & Validate — Full Migration"
goal: "Config repo uses rig-cli canonical structure and rig validate passes"
requirements: [MIGS-01, MIGS-02, MIGS-03, MIGS-04]
depends_on: []
must_haves:
  truths:
    - "devices/ directory exists; no pedals/ directory"
    - "signal-chain.yaml uses device: for all entries"
    - "rig validate exits 0"
    - "git working tree is clean"
  anti_goals:
    - "Do not modify hx-stomp.yaml — it is already correct"
    - "Do not use /Users/derekgliwa/.local/bin/rig for validation"
    - "Do not pre-create devices/ before the git mv"
verification:
  - "rig validate exits 0 with no errors"
  - "git status shows clean working tree"
  - "ls shows devices/ not pedals/"
  - "grep 'pedal:' signal-chain.yaml returns no matches"
---

## Tasks

### Group 1 — Pre-commit fix: Mood DIP parameter names

**Task 1.1 — Fix DIP parameter names in pedals/mood.yaml**

The current `pedals/mood.yaml` uses simplified DIP names. The rig-chasebliss catalog
requires the full prefixed names. Apply these exact renames across all presets:

| Wrong name    | Correct name     | Appears in presets               |
|---------------|------------------|----------------------------------|
| `dip_spread`  | `dip_r_spread`   | sigur-ros-drone                  |
| `dip_trails`  | `dip_r_trails`   | sigur-ros-drone, ambient-pad-machine, broken-tape-memories, second-guitarist |
| `dip_smooth`  | `dip_r_smooth`   | sigur-ros-drone, second-guitarist |
| `dip_bounce`  | `dip_l_bounce`   | broken-tape-memories             |
| `dip_sweep`   | `dip_l_sweep`    | broken-tape-memories             |

File: `pedals/mood.yaml`

Edit the file in-place. Do not change any numeric values, do not add or remove parameters,
do not reformat comments. Only rename the five DIP keys listed above.

Verify: `grep -E 'dip_(spread|trails|smooth|bounce|sweep):' pedals/mood.yaml` returns no
matches (all occurrences have been prefixed with `r_` or `l_`).

---

### Group 2 — Commit in-progress work (3 logical commits)

**Task 2.1 — Commit the Mood MkII preset and DIP fix**

Stage and commit `pedals/mood.yaml` (the newly-fixed preset file, previously untracked
as far as git is concerned because the root `mc6.yaml` delete is unrelated).

```
git add pedals/mood.yaml
git commit -m "feat(mood): revise presets and fix DIP parameter names"
```

Commit covers: updated preset IDs and parameters (preset_numbers 3–6), corrected DIP
control names to match rig-chasebliss catalog.

**Task 2.2 — Commit the MC6 device file**

Stage and commit the new untracked controller file:

```
git add pedals/mc6.yaml
git commit -m "feat(mc6): add MC6 controller device file"
```

This file defines `id: mc6`, `type: controller`, midi channel 1, and Bank 1 switch
mappings (A→clean, B→crunch, C→lead, D→wacky).

**Task 2.3 — Commit the scene revisions**

Stage and commit the four modified scene files:

```
git add scenes/clean.yaml scenes/crunch.yaml scenes/lead.yaml scenes/wacky.yaml
git commit -m "feat(scenes): update presets and descriptions for all four scenes"
```

Scenes updated: clean (second-guitarist mood preset), crunch (ambient-pad-machine),
lead (broken-tape-memories), wacky (sigur-ros-drone). Descriptions also revised.

At this point `git status` should show only `D mc6.yaml` (the deleted root-level file)
as the remaining unstaged change. The scenes and pedals/ contents are clean.

---

### Group 3 — Migration: directory rename + field rename

**Task 3.1 — Rename pedals/ to devices/ using git mv**

```
git mv pedals devices
```

This renames the directory while preserving full git history for each file inside it.
Do not `mkdir devices/` first — git mv handles the directory creation.

After this command:
- `devices/hx-stomp.yaml` exists
- `devices/mood.yaml` exists
- `devices/mc6.yaml` exists
- `pedals/` directory is gone

The deleted root-level `mc6.yaml` (from the `D mc6.yaml` status entry) is unrelated to
this rename — it was a separate file at the repo root. That deletion will be included in
the migration commit (Task 3.3).

**Task 3.2 — Update signal-chain.yaml: replace pedal: with device:**

File: `signal-chain.yaml`

Replace all three `pedal:` field names with `device:`. The values (hx-stomp, mood, mc6)
and all other fields (position, loop) remain unchanged.

Before:
```yaml
chain:
  - pedal: hx-stomp
    position: 1
    loop: front
  - pedal: mood
    position: 2
    loop: front
  - pedal: mc6
    position: 3
    loop: none
```

After:
```yaml
chain:
  - device: hx-stomp
    position: 1
    loop: front
  - device: mood
    position: 2
    loop: front
  - device: mc6
    position: 3
    loop: none
```

Verify: `grep -c 'pedal:' signal-chain.yaml` returns `0`.

---

### Group 4 — Commit migration changes

**Task 4.1 — Stage and commit all migration changes**

```
git add -A
git commit -m "feat(migrate): rename pedals/ to devices/, update signal-chain to device: field"
```

This single commit captures:
- The `git mv pedals devices` rename (history-preserving)
- The `signal-chain.yaml` field rename (`pedal:` → `device:`)
- The deletion of the root-level `mc6.yaml` (was already staged as `D`)

After this commit, `git status` must show a clean working tree with no staged or
unstaged changes.

---

### Group 5 — Validate

**Task 5.1 — Run rig validate**

```
rig validate
```

Use the mise shim (`rig`) which resolves to the editable dev install at
`/Users/derekgliwa/dev/rig-cli`. Do NOT invoke
`/Users/derekgliwa/.local/bin/rig` — that is an old uv-tools version with a
different schema and will produce false results.

Expected outcome: command exits with code 0 and no ValidationError output.

If validation fails:
- ValidationError mentioning a DIP parameter name → recheck Task 1.1; a name was missed.
- ValidationError mentioning `pedal:` → recheck Task 3.2; a reference was missed.
- Any other error → read the full error message before making changes.

Final state checks (run all four):
```
rig validate                                          # exits 0
git status                                            # clean working tree
ls                                                    # shows devices/, no pedals/
grep -v '^#' signal-chain.yaml | grep -c 'pedal:'    # returns 0
```
