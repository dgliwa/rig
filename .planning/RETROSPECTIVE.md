# Retrospective: Derek's Guitar Rig

## Milestone: v1.1 — Architecture Migration

**Shipped:** 2026-06-08
**Phases:** 1 | **Plans:** 1 | **Timeline:** ~1 day (2026-06-07)

### What Was Built

- Renamed `pedals/` to `devices/` (canonical rig-cli directory name)
- Updated `signal-chain.yaml` to use `device:` field instead of legacy `pedal:` alias
- Committed all in-progress YAML edits: mc6.yaml added, Mood MkII DIP parameters corrected, all four scene descriptions and preset references revised

### What Worked

- Single-phase, single-plan structure was right for this scope — no coordination overhead
- Treating "commit in-progress work first" as an explicit task (MIGS-03) prevented losing the DIP parameter fixes in the migration noise

### What Was Inefficient

- Planning artifacts (SUMMARY.md, STATE.md progress %) were never updated after execution — had to reconstruct from git log at milestone close
- The milestone was effectively "done" at commit `6ea6402 milestone 1.1` but closing wasn't formalized

### Patterns Established

- Milestone scope should match rig-cli version scope — versions in sync across repos is a good default
- The GSD phases directory needs SUMMARY.md created at execution time, not retroactively

### Key Lessons

- **Verify `rig validate` loads what you think it loads** — passing with 0 errors is not the same as validating real content. The schema had moved past directory-based loading before the migration was finished.
- **Check rig-cli source before assuming a migration is complete** — the actual schema the CLI reads is the ground truth, not the file structure
- The deferred gap (0 devices/0 scenes in validate) is the right call to note explicitly rather than treating MIGS-04 as fully satisfied

### Cost Observations

- Sessions: 1 planning session + 1 execution session
- All work committed in ~4 commits over ~1 hour on 2026-06-07
- No rework required

---

## Milestone: v1.3 — 90s Rock Sound

**Shipped:** 2026-06-18
**Phases:** 3 (Phases 5-7) | **Plans:** 3

### What Was Built

- Added `rock-drive` Brothers AM dual-channel preset (Ch1: transparent boost, Ch2: kicking DIST distortion using 0=boost/1=od/2=dist encoding)
- Added `chorus-shimmer` Mood MkII preset (wet_channel: 1 delay path for chorus-like shimmer)
- Added `envelope-filter` Wombtone MkII preset (rate=0 cocked-wah, high feed/resonance)
- Added `mesa-rock` HX Stomp preset entry in rig.yaml (hlx file is user-created in HX Edit)
- Created `rock` scene wiring all four devices; mapped MC6 Bank 1 Switch B to activate it
- `rig validate` passes: "5 pedals, 2 scenes — All cross-references valid"

### What Worked

- Single-phase single-plan structure continued to be right for this scope — each phase had exactly one deliverable, no coordination overhead
- Grepping for exact indentation before edits prevented errors (Switch B indentation was different from memory — grep caught it)
- Researching CBA MIDI encoding upfront (dipswitch 0/127, gain_N_type 0-indexed) prevented multiple rounds of correction

### What Was Inefficient

- REQUIREMENTS.md traceability was not updated as phases completed — arrived at milestone close with HX-01 and all three SCENE requirements still showing "Pending" despite being done
- No v1.3 milestone audit run — skipped due to clear phase completion evidence in git

### Patterns Established

- When adding a new scene element, grep for the existing scene's indentation before writing — YAML indentation errors are silent until `rig validate`
- CBA device MIDI encoding constants worth documenting at requirement-gathering time, not mid-execution

### Key Lessons

- **Update traceability table immediately after each phase completes** — stale "Pending" rows in REQUIREMENTS.md waste time at milestone close reconstructing what was actually done
- **Wombtone has no envelope follower** — true auto-wah requires an external expression pedal + MC6 CC map; document this constraint before scoping related requirements

### Cost Observations

- Sessions: ~1 planning + 1 execution session per phase (3 phases)
- Execution time: ~20 min per phase, ~1 hour total
- No rework required on any phase

---

## Cross-Milestone Trends

| Milestone | Phases | Plans | Days | Notes |
|-----------|--------|-------|------|-------|
| v1.1 Architecture Migration | 1 | 1 | 1 | Clean execution; schema gap discovered at close |
| v1.2 Flesh Out the Rig | 3 | 3 | 2 | Retrospective not captured at close |
| v1.3 90s Rock Sound | 3 | 3 | 18 | All phases complete; traceability table stale at close |
