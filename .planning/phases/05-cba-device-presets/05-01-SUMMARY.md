# Plan 05-01: CBA Device Presets — Summary

**Status:** Complete
**Completed:** 2026-06-18
**Commits:** 3

## What Was Built

Added three new active preset entries to `rig.yaml` for the three Chase Bliss Audio pedals: `rock-drive` (Brothers, preset 5), `envelope-filter` (Wombtone, preset 6), and `chorus-shimmer` (Mood, preset 7). All parameter values match the D-01 through D-11 decisions locked in 05-CONTEXT.md. `rig validate` passes with exit 0 and no errors after all insertions.

## Tasks Completed

- [x] T1: Add Brothers rock-drive preset
- [x] T2: Add Wombtone envelope-filter preset
- [x] T3: Add Mood chorus-shimmer preset
- [x] T4: Validate all three presets

## UAT Results

- [x] BROTHERS-01: rock-drive visible with volume_1: 90 and dip_hi_gain_2: 127
- [x] WOMBTONE-01: envelope-filter visible with rate: 0 and feed: 100
- [x] MOOD-01: chorus-shimmer visible with wet_channel: 1 and time: 35
- [x] rig validate passes — exit 0, no errors

## Commits

| Task | Commit  | Description                                         |
|------|---------|-----------------------------------------------------|
| T1   | d389dc5 | feat(05-01): add rock-drive preset to brothers      |
| T2   | 585d9c0 | feat(05-01): add envelope-filter preset to billy-strings-wombtone |
| T3   | d86ea8e | feat(05-01): add chorus-shimmer preset to mood      |

## Key Files Modified

- `rig.yaml`

## Notes

No deviations from the plan. All three presets inserted at the exact locations specified in the plan (after the last active preset and before the first commented-out preset in each device section). `gain_1_type: 2` in rock-drive uses value `2` which means "boost" in Brothers AM encoding (as documented in the plan's dipswitch note: 0=boost, 1=od, 2=dist — confirmed by reference to existing `am-crunch` preset pattern). These preset IDs are now stable for Phase 7 rock scene dependencies.
