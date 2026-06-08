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

## Cross-Milestone Trends

| Milestone | Phases | Plans | Days | Notes |
|-----------|--------|-------|------|-------|
| v1.1 Architecture Migration | 1 | 1 | 1 | Clean execution; schema gap discovered at close |
