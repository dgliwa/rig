# Phase 5: CBA Device Presets - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-06-18
**Phase:** 5-CBA Device Presets
**Areas discussed:** Brothers rock-drive gain level, Wombtone envelope-filter wet blend, Mood chorus-shimmer density

---

## Brothers rock-drive gain level

| Option | Description | Selected |
|--------|-------------|----------|
| Weezer-level crunch | gain_2 ~60-70: pushed distortion, crunchy and defined | |
| Kicking rock DIST | gain_2 ~80-90: aggressive, meaty distortion | |
| Free-form | Ch1 = Buddy Holly rhythm level; Ch2 = Say It Ain't So solo | ✓ |

**User's choice:** Ch1 is Buddy Holly level (moderate boost push), Ch2 is Say It Ain't So solo territory (high-gain DIST)

---

## Brothers Ch2 Hi-Gain Dipswitch

| Option | Description | Selected |
|--------|-------------|----------|
| Yes — engage dip_hi_gain_2 | Pushes Ch2 into proper solo-sustain territory | ✓ |
| No — just high gain_2 value | Tighter, less compressed, no dipswitch | |
| You decide | Planner picks based on reference | |

**User's choice:** Engage dip_hi_gain_2: 127

---

## Wombtone envelope-filter wet blend

| Option | Description | Selected |
|--------|-------------|----------|
| Full effect — heavily filtered | High mix (90-100+), high depth. Vowel character dominates. | |
| Blended — filtered but still present | Moderate mix (~60-80), moderate depth. Phaser adds character. | |
| Free-form | Option 1 (full depth) but with moderate mix | ✓ |

**User's choice:** High depth for full phaser effect, but moderate mix (~60-80) so dry signal remains present.

---

## Mood chorus-shimmer density

| Option | Description | Selected |
|--------|-------------|----------|
| Subtle shimmer — just adds life | Low mix (~40-55), hi-fi clock | |
| Present chorus texture | Moderate mix (~65-80), clock ~50-70. Audible chorus. | ✓ |
| You decide | Planner picks for 90s rock | |

**User's choice:** Present chorus texture — audible, adds depth. Reference: Smashing Pumpkins "Today" rhythm.

---

## Mood chorus-shimmer routing

| Option | Description | Selected |
|--------|-------------|----------|
| Live signal (routing: 0 = in) | Shimmer on direct guitar signal | |
| Loop output only (routing: 2 = loop) | Shimmer on loop buffer only | |
| Both in & loop | User requested both | ✓ |

**User's choice:** Both in & loop — use routing: 1 (out) to cover full output.

---

## Claude's Discretion

- Exact gain_1 value for Brothers Ch1 (Buddy Holly rhythm level — planner picks ~35-50 range)
- Exact gain_2 value for Brothers Ch2 (Say It Ain't So solo — planner picks ~85-95 range)
- Exact volume/tone values for both Brothers channels
- Wombtone exact mix value (~60-80 range, moderate)
- Wombtone depth exact value (near-max for full cocked-wah effect)
- Wombtone form parameter
- Mood exact time/wet_modify/clock values within the present-texture target range
- Preset slot numbers for all three new presets

## Deferred Ideas

None — discussion stayed within phase scope.
