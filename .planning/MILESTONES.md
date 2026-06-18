# Milestones

## v1.3 90s Rock Sound (Shipped: 2026-06-18)

**Phases completed:** 3 phases (5–7), 3 plans

**Key accomplishments:**

- Added `rock-drive` Brothers AM dual-channel preset: Ch1 transparent boost (louder than transparent-lift), Ch2 kicking DIST distortion; `rig validate` passes with all three new CBA presets in place.
- Added `chorus-shimmer` Mood MkII and `envelope-filter` Wombtone MkII presets; mesa-rock HX Stomp preset entry pointing to user-supplied .hlx patch.
- Created `rock` scene in mc6.config.scenes wiring all four devices (brothers/wombtone/hx-stomp/mood) and mapped MC6 Bank 1 Switch B to activate it — two live scenes (clean + rock) confirmed with `rig validate`.

**Deferred:** WOMBTONE-02 (true auto-wah — hardware limitation, Wombtone has no envelope follower)

---

## v1.2 Flesh Out the Rig (Shipped: 2026-06-10)

**Phases completed:** 3 phases, 3 plans, 5 tasks

**Key accomplishments:**

- Billy Strings Wombtone and Brothers AM devices added to rig.yaml with real presets; Mood MkII controls synced to catalog (45 controls) and all 4 presets annotated with sonic intent.
- 4 production-ready scenes fully wired with all devices; all Mood MkII presets rewritten from official doc recipes (Tier 1: ①a, ②, ④; Tier 2: ⑦).

---

## v1.1 Architecture Migration (Shipped: 2026-06-08)

**Phases completed:** 1 phases, 1 plans, 0 tasks

**Key accomplishments:**

- Renamed `pedals/` to `devices/`, updated `signal-chain.yaml` to canonical `device:` field, committed all in-progress YAML revisions, and confirmed `rig validate` passes.

---
