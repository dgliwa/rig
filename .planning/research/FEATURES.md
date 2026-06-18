# Feature Landscape: 90s Rock / Weezer Preset Set

**Domain:** Guitar rig preset configuration (Chase Bliss + HX Stomp)
**Researched:** 2026-06-18
**Confidence:** HIGH — all hardware is well-documented; Weezer/Cuomo tone is thoroughly
analyzed in gear journalism; parameter ranges drawn from CBA manuals and HX Stomp model
library; existing rig.yaml provides validated parameter conventions.

---

## Feature Overview

Six discrete features across four devices, wired into one new MC6 scene. Each feature
is either a new YAML preset block (Brothers, Wombtone, Mood) or a new HX Stomp `.hlx`
patch. The scene is the final deliverable that wires them together.

---

## Table Stakes

Features the "rock" scene requires to feel complete. Missing any one = the scene
cannot ship.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Brothers AM Ch-B: kicking distortion preset | The rock scene needs a standalone distortion voice; a clean boost alone will not define the crunch character | Low-Medium | DIST mode (gain_2_type: 2) + HI GAIN 2 dip; MASTER dip for level stability |
| Brothers AM Ch-A: light transparent boost (rock variant) | Volume push before the amp without added grit; used to push the Mesa's input for solo lift | Low | Existing `transparent-lift` (volume_1: 83) may suffice or need volume bump to 89–96 for Mesa headroom context |
| HX Stomp: Mesa Boogie Mark IV amp model + 4x12 cab | The amp is the foundation; everything else sits on top of it | Medium | New `.hlx` patch; "Cali IV Lead" is the Mark IV model in HX Stomp firmware; see detail section |
| HX Stomp: delay block | 90s alt-rock delay is a signature part of the texture; dry amp sounds naked | Low | Digital delay block ~250–350 ms, low mix (~20–25%), feedback ~25–35% |
| HX Stomp: reverb block | Room/hall glue; high-gain patches without reverb feel brittle and stiff | Low | Small room or hall at low mix (~15–20%); not an ambient reverb |
| New MC6 scene: "rock" | MC6 switch recall wiring all four devices to their rock presets simultaneously | Low | New `scene` block in rig.yaml; last step after all presets exist |

---

## Differentiators

Features that elevate beyond a usable rock tone toward the specific Weezer sonic
character. Not required for a working scene but strongly recommended.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Mood MkII: chorus-y preset (CE-1/CE-2 style) | Weezer's "Buddy Holly", "Undone", and Blue Album rhythm guitars use a lush, slightly detuned chorus; without it the patch sounds like generic 90s rock, not specifically Weezer | Medium | Mood's delay channel (wet_channel: 1) at very short TIME + moderate WET_MODIFY creates a chorus-like shimmer; see detail section |
| HX Stomp: optional footswitch-assignable chorus block | Second chorus source in-box; lets Mood chorus be independent; toggleable without a preset change | Low | "CE-1 Chorus Vib" or "Poly Chorus" block, footswitch-assigned; counts as one block in the 6-block budget |
| Wombtone MkII: high-resonance "cocked wah" phaser preset | Fixed phaser position creates a nasal, mid-boosted character similar to a cocked wah pedal — adds that Weezer-ish vocal quality to the rock scene | Low-Medium | High feed (resonance), moderate-to-zero rate, deep depth — creates a fixed resonant peak; see detail and constraint note |

---

## Anti-Features

Features to explicitly avoid.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| Brothers Ch-B at maximum gain with HI GAIN on | At DIST + full gain + HI GAIN 2, Ch-B becomes a fuzz wall that overwhelms the Mesa model — you lose amp character, only fuzz remains | Set gain_2 to 60–75 (noon to ~1 o'clock); the amp model provides additional saturation |
| HX Stomp Mesa amp with presence above 65 | HX Stomp's Cali IV Lead has a bright-cap simulation; presence maxed + Mesa mid-scoop = harsh fizz, especially with a Greenback/V30 cab sim | Keep presence at 45–55; use cab model high-cut for top-end shaping |
| Mood chorus with routing: 0 (in mode) at high mix | In "in" routing at high mix the chorus overpowers the dry signal; high-gain attack becomes mushy and indistinct | Use routing: 0 with mix held to 50–65, or routing: 2 (loop) if chorus should blend with a loop |
| Brothers Ch-A and Ch-B both active by default in the rock scene | Ch-A boost pushing into Ch-B distortion stacks two drive stages into the already-saturated Mesa; input clips before the amp's own gain stage | Default Ch-A to bypass in the rock scene; assign a separate footswitch to engage the boost for solos |
| Dual modulation: Mood chorus AND Wombtone rate sweep simultaneously | Two heavy modulation sources creates a swirling, phase-cancellation mess — not musical | Let the player toggle one at a time; Wombtone preset = static position, Mood preset = the active modulation |

---

## Feature Detail: Brothers AM Channel A — Transparent Boost (rock variant)

**Sonic target:** Volume push of ~4–6 dB with zero tonal character change. Used to
push the Mesa's input for solo lift without engaging Ch-B or altering the amp voice.
Rivers Cuomo runs a clean boost before the Mesa specifically to push the amp harder on
leads without changing the drive character.

**Parameters:**
- `gain_1_type: 2` — boost mode; highest headroom, cleanest gain mode in Brothers
- `gain_1: 13–20` — 8–9 o'clock; barely above minimum; avoids crossover distortion
- `volume_1: 89–96` — ~2 o'clock; noticeable volume lift calibrated for Mesa headroom
- `tone_1: 64` — noon; neutral
- `presence_1: 0` — off; no treble lift; amp presence handles top end
- `treble_boost: 1` — off; Rangemaster-style treble boost makes Rectifier/Mark IV harsh
- `channel_2_bypass: 0` — Ch-B bypassed; this is a boost-only preset

**Relationship to existing `transparent-lift`:** The existing preset (volume_1: 83) was
tuned for the clean Fender scene. For the Mesa context, volume_1 should be raised to
89–96 to compensate for the Mesa's higher headroom ceiling. This can either be a
separate preset (preset_number: 5) or the existing preset can be updated. A separate
preset is preferable so the clean scene is not affected.

**Complexity:** Low. Parameter adjustment only; no new modes or dip switches.

---

## Feature Detail: Brothers AM Channel B — Kicking Distortion

**Sonic target:** Thick, aggressive, compressed 90s rock crunch. Not a fuzz, not a
transparent overdrive — a hard-clipping distortion that adds body and sustain. Think
Weezer Pinkerton-era rhythm tone or Smashing Pumpkins "Cherub Rock". The Brothers'
DIST mode is a hard-clipper (asymmetric clipping); HI GAIN 2 engages a +25% gain
circuit that pushes it into tight, compressed saturation without becoming a fuzz.

**Parameters:**
- `gain_2_type: 2` — dist mode; hard clipping; required for the kicking character
- `gain_2: 64–70` — noon to ~12:30; moderate distortion; let the Mesa amp add the rest
- `volume_2: 76–83` — ~1 o'clock; close to unity; amp governs loudness
- `tone_2: 57–64` — slightly dark to neutral; DIST mode gets bright fast; dial back
- `presence_2: 0` — off; no top-end addition on an already bright distortion
- `dip_hi_gain_2: 1` — HI GAIN on; the +25% gain is what makes it "kicking"; without
  it DIST mode is anemic and lacks the compressed character
- `treble_boost: 1` — off; treble boost before DIST creates ice-pick harshness
- `dip_master: 1` — MASTER on; VOL2 governs output; prevents level spike when Ch-A is
  toggled in during solos
- Ch-A: this preset leaves Ch-A at minimum/bypass; Ch-A boost is toggled independently
  via the rock scene's optional boost footswitch

**What distinguishes this from "Bad Bros" (commented preset):** Bad Bros runs both HI
GAIN dips on, gain_1 at 96, and treble_boost at half_sun — a maximum gnarly stack. This
rock distortion preset deliberately moderates gain_2 to 64–70 and disables the Ch-A
push and treble boost. The goal is an amp-friendly distortion that sits under the Mesa
model, not a fuzz that replaces it.

**Complexity:** Low-Medium. Requires understanding the dip_master interaction (MASTER
must be on for level stability when Ch-A is added later); otherwise straightforward.

---

## Feature Detail: Mood MkII — Chorus-y Preset (CE-1/CE-2 style)

**Sonic target:** Lush, slightly detuned chorus reminiscent of the Roland CE-1/CE-2 or
Boss CH-1. Weezer's Blue Album uses various chorus units throughout (CE-1 style on
"Buddy Holly", "Undone"). The key sonic character: slow, wide pitch modulation with
enough depth to make chords shimmer but not so much that they sound seasick.

**How Mood MkII achieves chorus:** Mood's WET channel set to delay (wet_channel: 1)
with a very short TIME value (5–20 ms range) creates a slapback shimmer that crosses
into chorus territory. The CLOCK parameter affects the sample rate and therefore the
pitch-modulation character of the delay. Higher CLOCK values = cleaner/brighter chorus;
lower = warmer, more bucket-brigade analog feel. WET_MODIFY at moderate-high values
adds resonance/feedback depth.

**Parameters:**
- `wet_channel: 1` — delay channel; required for chorus-type effect; reverb will not do this
- `time: 15–25` — very short delay time; chorus range (~5–20 ms effective); this is the
  most critical parameter to dial in by ear
- `mix: 55–70` — ~noon to just past; Weezer chorus sits forward but does not drown dry signal
- `wet_modify: 70–85` — moderate-high feedback adds resonance and depth to the shimmer
- `clock: 80–100` — higher sample rate = cleaner chorus character; lower = vintage BBD feel
- `loop_modify: 64` — noon; normal playback; no reverse or stretch
- `routing: 0` — in mode; wet processes live signal (chorus must track live playing)
- `loop: 1` — tape; warm, slightly pitch-unstable character that enhances the chorus effect
- `dip_r_trails: 127` — TRAILS on; chorus fades smoothly on bypass
- `ramp_speed: 0` — no ramping; static modulation rate
- `length: 50` — noon; neutral; looper not the focus here

**Important caveat:** Mood MkII does NOT have a dedicated LFO-modulated chorus engine.
The chorus effect emerges from the interaction of short delay, high feedback, and the
sample rate/clock interaction. The result is more of a "shimmer-slapback" than a
traditional CE-2 pitch sweep. It will be lush and harmonically interesting but will
not sweep the same way a bucket-brigade chorus does. Calibrate TIME carefully — below
~10 ms tends toward flanger-like comb filtering; above ~30 ms tips into slapback echo.

**Complexity:** Medium. TIME and WET_MODIFY calibration requires hands-on iteration.

---

## Feature Detail: Wombtone MkII — "Cocked Wah" Phaser Preset

**Sonic target:** A fixed, high-resonance phaser position that creates a nasal,
mid-boosted tonal peak — the "cocked wah" effect. Used to add vocal, mid-forward
character to the rock scene. Not a dynamic auto-wah; a static voicing choice. Think
of it as a fixed EQ peak shaped like a phaser sweep frozen at its most resonant point.

**Critical hardware constraint:** The Wombtone MkII is a phaser, not an envelope filter.
It has no envelope follower circuit and no MIDI parameter that maps to envelope
sensitivity or pick-attack tracking. The CBA MIDI spec for Wombtone exposes: rate,
depth, form, feed (feedback), volume, mix, ramp speed, and clock division — no
envelope-related parameters. A dynamically-reactive auto-wah that responds to pick
dynamics is NOT achievable as a standalone MIDI preset.

**For a true reactive auto-wah:** Requires an expression pedal connected to Wombtone's
TRS jack, with MC6 sending real-time CC messages mapped to the RATE parameter. This is
a Phase 2 enhancement (hardware + MC6 mapping task), not a Phase 1 preset task.

**Recommended approach — static cocked-wah phaser:**

- `feed: 100–110` — high feedback creates the resonant peak that reads as a filter
  vowel rather than a phase shift
- `volume: 70` — unity
- `mix: 89–102` — heavy wet blend; the phaser must dominate to create the wah character
- `rate: 10–20` — very slow or near-zero sweep; fast rate sounds like a phaser, not a wah
- `depth: 95–110` — deep modulation sweep range, even if it moves slowly
- `form: 30–45` — asymmetric waveform shape; creates the "yawning" peak shape vs
  a symmetric sine that reads as a smoother phase shift
- `ramp: 0` — no ramping; static modulation
- `clock_division: 2` — quarter note; base tempo reference

**Why this still works for the rock scene:** A high-resonance phaser at very slow rate
creates a fixed spectral notch-and-peak pattern that emphasizes specific midrange
frequencies. Depending on where the sweep is positioned, it sounds like a wah-guitar
voice frozen open — which is a classic 90s alt-rock production move (Velvet Underground
style, or the "cocked wah" heard on many Smashing Pumpkins tracks). It is less
dynamically interesting than a true auto-wah but adds genuine tonal character.

**Complexity:** Low-Medium for static preset. High if true auto-wah (expression pedal
configuration) is desired.

---

## Feature Detail: HX Stomp — Mesa Boogie Amp Patch with FX

**Sonic target:** The archetypal 90s alt-rock amp. Tight, slightly scooped low-mids,
searing saturation, and a compressed feel. Rivers Cuomo's Mesa Boogie Mark IV was the
primary amp on Weezer's Blue Album. The Mark IV is tighter and more dynamic than a
Dual Rectifier, which suits Weezer's rhythmically precise guitar parts better than the
looser, heavier Rectifier.

**HX Stomp amp model:**
- "Cali IV Lead" = Mesa Boogie Mark IV Lead channel — correct model for Weezer Blue Album
- "Placater Dirty" = Mesa Boogie Dual Rectifier — heavier, looser, better for Pinkerton
  or Smashing Pumpkins territory
- Recommendation: "Cali IV Lead" as primary; "Placater Dirty" as an alternative variant
  if a heavier sound is needed later

**Amp parameters (Cali IV Lead — approximate targets):**
- Drive: 55–65 — moderate; Brothers distortion stacks with it; don't max
- Bass: 45–55 — slightly scooped; characteristic Mesa low-mid contour
- Mid: 38–48 — scooped; Weezer Blue Album has a scooped-mid character
- Treble: 60–70 — bright but not harsh
- Presence: 45–55 — moderate; see anti-feature above
- Master: 70–80 — higher master = tighter, more controlled response
- Ch Vol: 70–80 — calibrate to match other scenes

**Cabinet model:** "4x12 XXL V30" (Celestion Vintage 30 simulation) is the standard
pairing for a Mesa Mark IV. V30 cabs are brighter, with a scooped midrange that matches
the amp's own character. "4x12 Greenback 25" is warmer and sags more — usable as a
variant but less Weezer-accurate.

**FX chain blocks (within 6-block HX Stomp limit):**
1. Amp + cab block — "Cali IV Lead" into "4x12 XXL V30" (counts as 1 or 2 blocks
   depending on whether split or combined; use single block to save DSP)
2. Digital delay — "Transistor Tape" or "Simple Delay", ~250–350 ms, feedback ~25–35%,
   mix ~20–25%; sits under the tone as texture, not an overt effect
3. Room reverb — "Ganymede" or "Room" reverb algorithm, mix ~15–20%, decay ~1.5–2.5s;
   glue reverb that makes the amp feel like it's in a room
4. Optional chorus block — "CE-1 Chorus Vib" or "Poly Chorus", footswitch-assignable,
   rate ~0.5–1 Hz, depth ~30–40%, mix ~20–30%; toggle without changing presets

Block budget: amp+cab (1–2) + delay (1) + reverb (1) + optional chorus (1) = 4–5 blocks.
Fits within the 6-block limit with room for one more block if needed.

**Signal chain within patch:** Input → (optional chorus — toggle) → Amp → Cab →
Delay → Reverb → Output. This mirrors standard 90s rack topology.

**`.hlx` file note:** The HX Stomp patch is a JSON file (`.hlx` format) edited in
HX Edit software or hand-crafted. The rig.yaml entry will reference `hlx_file: hlx/Rock.hlx`
(or similar). This makes the HX Stomp feature a two-part task: create the `.hlx` file
AND add the YAML stanza. The `.hlx` file is opaque JSON — it cannot be meaningfully
parameterized in YAML without the full HX Edit spec.

**Complexity:** Medium. Requires building a new patch in HX Edit (or locating an
existing Boogie patch to adapt). Calibration against the Brothers distortion pedal is
critical to avoid double-gain buildup.

---

## Feature Detail: New MC6 "Rock" Scene

**Sonic target:** Single-button scene recall that simultaneously recalls all four device
presets to their rock configurations. MC6 sends MIDI PC messages to each device.

**Scene wiring:**
- `hx-stomp`: new rock patch (preset_number: 37)
- `brothers`: new kicking-distortion preset (preset_number: 5)
- `billy-strings-wombtone`: new cocked-wah phaser preset (preset_number: 4)
- `mood`: new chorus-y preset (preset_number: 4)

**MC6 switch assignment:** Bank 1 Switch B is the natural candidate — it matches the
commented-out position in the current rig.yaml and keeps A=clean, B=rock logical.

**Dependencies:** All four device presets must exist and be validated in rig.yaml before
the scene can be wired. The scene block is always the final deliverable.

**Complexity:** Low. Once presets exist, the scene is 5–6 YAML lines.

---

## Feature Prioritization Matrix

| Feature | User Value | Implementation Priority | Complexity | Risk |
|---------|------------|------------------------|------------|------|
| Brothers Ch-B kicking distortion | HIGH | P1 | Low-Medium | Low |
| HX Stomp Mesa Boogie amp patch | HIGH | P1 | Medium | Medium |
| New MC6 rock scene | HIGH | P1 (last) | Low | Low |
| Brothers Ch-A transparent boost (rock variant) | MEDIUM | P1 | Low | Low |
| Mood MkII chorus-y preset | MEDIUM | P2 | Medium | Medium |
| HX Stomp optional chorus block | MEDIUM | P2 | Low | Low |
| Wombtone cocked-wah phaser preset | MEDIUM | P2 | Low-Medium | Low |
| Wombtone true auto-wah (expression pedal) | LOW | P3 (future) | High | High |

**P1:** Must ship for the rock scene to be usable.
**P2:** Should ship; adds the Weezer-specific character above generic 90s rock.
**P3:** Future enhancement requiring expression pedal hardware.

---

## Feature Dependencies

```
[HX Stomp rock patch]         ─needs─> [new .hlx file in hlx/]
[Brothers distortion preset]  ─needs─> [preset_number: 5 free]
[Wombtone phaser preset]      ─needs─> [preset_number: 4 free]
[Mood chorus preset]          ─needs─> [preset_number: 4 free]

[MC6 rock scene]
    ─requires all four device presets above─>

[Wombtone true auto-wah]
    ─requires─> [expression pedal hardware]
    ─requires─> [MC6 CC mapping config]
    ─deferred─> Phase 2 / future
```

Sequential order: device presets first (can be developed in parallel across devices),
then MC6 rock scene last.

---

## Preset Number Assignments (Recommended)

| Device | Existing occupied slots | New preset | Recommended slot |
|--------|------------------------|------------|-----------------|
| Brothers AM | 4 (transparent-lift) | kicking-distortion | 5 |
| Wombtone MkII | 3 (slow-vibe) | cocked-wah phaser | 4 |
| Mood MkII | 3 (ambient-reverse-tape) | chorus-y | 4 |
| HX Stomp | 36 (clean) | rock/boogie | 37 |

CBA pedals support presets 1–16; slots 3 and 4 for Brothers/Wombtone/Mood preserve
room for future growth without colliding with existing presets.

---

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Brothers AM parameters | HIGH | DIST mode, HI GAIN dip, and MASTER dip behavior are established in the existing rig.yaml commented presets; gain_2_type values are validated |
| HX Stomp amp models | HIGH | "Cali IV Lead" (Mark IV) and "Placater Dirty" (Rectifier) are confirmed model names; 6-block limit is hardware spec |
| Mood MkII chorus approach | MEDIUM | Delay-as-chorus behavior is derived from Mood's architecture; exact TIME/WET_MODIFY values for the chorus sweet spot require hands-on calibration |
| Wombtone envelope filter constraint | HIGH | No envelope follower exists on Wombtone MkII; this is architectural, not a tuning question |
| Wombtone cocked-wah approach | MEDIUM | Parameter ranges are reasonable estimates; feed and form values require calibration |
| Weezer tone references | HIGH | Blue Album gear chain is well-documented; Mesa Mark IV, CE-1-style chorus, and clean-boost-into-amp are confirmed Cuomo techniques |

---

## Sources

- Existing `rig.yaml` commented presets — gain_type values, dip switch behavior, MASTER
  interaction all validated against Brothers AM manual conventions
- Chase Bliss Audio Brothers AM manual — DIST mode topology, HI GAIN circuit, MASTER dip
- Chase Bliss Audio Wombtone MkII manual — phaser architecture, MIDI CC parameter map,
  expression jack spec; confirms absence of envelope follower
- Chase Bliss Audio Mood MkII manual — wet channel modes (delay/reverb/slip), routing
  options, clock/sample rate behavior
- Line 6 HX Stomp model list — "Cali IV Lead" = Mark IV; "Placater Dirty" = Dual
  Rectifier; 6-block DSP limit
- Weezer Blue Album production documentation — Mesa Boogie Mark IV as primary amp;
  Rivers Cuomo rig interviews confirming CE-1-style chorus and clean boost usage
