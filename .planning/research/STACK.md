# Stack Research

**Domain:** Guitar rig preset configuration — 90s rock (Weezer-style) tone
**Researched:** 2026-06-18
**Confidence:** HIGH (catalog data pulled directly from rig-chasebliss source; HLX block data read from existing preset files)

---

## What This Document Covers

Specific parameter values, CC numbers, and configuration patterns for each new
preset in v1.3. The "stack" here is sonic: the parameter choices that produce
90s rock tone on each physical device.

---

## Recommended Stack

### Core Technologies

| Technology | Version/State | Purpose | Why Recommended |
|------------|--------------|---------|-----------------|
| Brothers AM | MIDI ch 4, catalog verified | Transparent boost + kicking distortion | Two independent channels let you keep one live; Ch1=BOOST + Ch2=DIST is the canonical dual-gain stack pattern already in the commented-out `bad-bros` preset |
| Wombtone MkII | MIDI ch 3, catalog verified | Envelope filter / auto-wah | The `form` knob sweeps the waveform from sine (phaser) through random; high-depth + slow-rate + high-feed = auto-wah without a dedicated pedal |
| Mood MkII | MIDI ch 2, catalog verified | Chorus bed for 90s shimmer | `wet_channel=delay` with short time + `routing=in` creates a chorus-like slapback; short delay at sub-20ms blends with dry for pitch-thickening chorus effect |
| HX Stomp | MIDI ch 1, preset_number TBD | Mesa/Boogie amp + 4x12 cab + FX | `HD2_AmpCaliIVLead` is already in use in `Boogie PB.hlx`; reuse that block with adjusted gain/mid/presence for Weezer-range saturation |

---

## Device-by-Device Parameter Knowledge

### Brothers AM — Transparent Boost Preset (Channel 1 only)

**Goal:** Push the front end of the amp louder without adding grit. The existing
`transparent-lift` preset (preset_number 4) already accomplishes this. Reuse
it in the rock scene as a solo-lift option.

**Why it works for 90s rock:** Weezer's Blue Album uses a relatively clean amp
pushed hard by volume and picking attack, not heavy dirt. A BOOST-mode clean
lift into the Mesa gives you that characteristic loud-clean-into-saturation
interaction.

**Existing preset values (reuse verbatim):**

| Parameter | Value | CC | Rationale |
|-----------|-------|----|-----------|
| `gain_1_type` | 2 (boost) | CC 23 | BOOST mode: cleanest, loudest, minimal coloration |
| `gain_1` | 13 | CC 16 | ~8 o'clock — zero grit, pure signal lift |
| `volume_1` | 83 | CC 18 | ~1-2 o'clock — audible lift above unity |
| `tone_1` | 64 | CC 19 | noon — neutral |
| `presence_1` | 0 | CC 29 | fully down — avoid ice-pick on an already-bright amp |
| `treble_boost` | 1 (off) | CC 22 | Pure KOT voice, no Rangemaster coloration |
| `channel_2_bypass` | 0 | CC 103 | Ch2 bypassed |

**No new preset needed — reuse `transparent-lift` in the rock scene.**

---

### Brothers AM — Kicking Distortion Preset (Channel 2, DIST mode)

**Goal:** Aggressive, saturated distortion for the v1.3 rock scene. Weezer-style
means scooped mid-range with bright treble, not muddy. Ch2=DIST with moderate
gain and presence pulled back.

**Why DIST over OD for this context:** `gain_2_type=2` (dist) engages hard
clipping for the square-wave saturation that defines classic rock distortion.
OD mode (value 1) is smoother and more compressed; BOOST (value 0) is clean.
For Weezer/Blue Album tone you want the defined attack of hard clipping.

**Recommended new preset — `weezer-drive`:**

| Parameter | Value | CC | Rationale |
|-----------|-------|----|-----------|
| `gain_2_type` | 2 (dist) | CC 21 | Hard clipping — defined attack, less compression than OD |
| `gain_2` | 76 | CC 14 | ~1 o'clock — pushed but not fizzy; Weezer sits below full |
| `volume_2` | 76 | CC 15 | ~1 o'clock — matches unity with amp |
| `tone_2` | 70 | CC 17 | Slightly bright — 90s rock favors treble presence |
| `presence_2` | 38 | CC 27 | 10 o'clock down — pulls back harsh upper-mid content that fizzes |
| `treble_boost` | 1 (off) | CC 22 | Off — DIST mode already has enough top end |
| `gain_1_type` | 2 (boost) | CC 23 | Ch1 set to BOOST so it stacks cleanly if both engaged |
| `gain_1` | 13 | CC 16 | Low — Ch1 as minimal push only |
| `volume_1` | 64 | CC 18 | Unity |
| `tone_1` | 64 | CC 19 | Neutral |
| `presence_1` | 0 | CC 29 | Down |
| `dip_master` | 0 | CC 77 | Off — keep Ch2 VOL governing output independently |

**Toggle value reference (from rig-chasebliss catalog.py, verified):**

```
gain_2_type (CC 21): 0=boost, 1=od, 2=dist       positions=[boost, od, dist]
gain_1_type (CC 23): 0=dist, 1=od, 2=boost        positions=[dist, od, boost]
treble_boost (CC 22): 0=full_sun, 1=off, 2=half_sun
```

**Critical gotcha:** `gain_1_type` and `gain_2_type` have opposite position orders.
Ch2 CC 21: boost=0, od=1, dist=2. Ch1 CC 23: dist=0, od=1, boost=2. Setting
both to value 2 gives Ch2=dist but Ch1=boost. The existing commented presets
handle this correctly; new presets must respect it.

---

### Wombtone MkII — Envelope Filter / Auto-Wah Preset

**Complete control catalog (all 12 controls, from catalog.py):**

| Parameter | CC | Range | Default | Notes |
|-----------|-----|-------|---------|-------|
| `feed` | 14 | 0-127 | 64 | Feedback/resonance — controls the Q of the filter notch |
| `volume` | 15 | 0-127 | 64 | Output level |
| `mix` | 16 | 0-127 | 64 | Wet/dry blend |
| `rate` | 17 | 0-127 | 64 | LFO speed |
| `depth` | 18 | 0-127 | 64 | Sweep depth |
| `form` | 19 | 0-127 | 64 | Waveform shape: low=square, mid=sine, high=random/sample-hold |
| `ramp` | 20 | 0-127 | 64 | Ramp modulation of rate |
| `clock_division` | 21 | 0-5 | 3 (quarter) | whole/half/quarter_triplet/quarter/eighth/sixteenth |
| `midi_clock_ignore` | 51 | switch | — | Ignore incoming MIDI clock |
| `tap_tempo` | 93 | switch | — | Tap tempo |
| `expression_over_midi` | 100 | switch | — | Expression pedal via MIDI |
| `bypass` | 102 | switch | — | Bypass toggle |

**The Wombtone is fundamentally a phaser** — it has no dedicated envelope mode.
The auto-wah effect is achieved by setting `rate` very slow (near 0) and `depth`
high so the sweep is wide and slow, then playing dynamically to interact with
the phase sweep position. High `feed` creates a strong resonant peak at the
notch frequency, which is the characteristic wah vowel.

**Recommended new preset — `envelope-filter`:**

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| `feed` | 90 | High resonance/Q — gives the wah-like peak at filter cutoff |
| `volume` | 70 | Slightly above unity — phasers can lose apparent loudness |
| `mix` | 102 | Heavy wet blend — auto-wah needs to be forward in the mix |
| `rate` | 12 | Very slow LFO — sweep moves slowly, guitar attack interacts with position |
| `depth` | 108 | Wide sweep — big filter travel for pronounceable wah character |
| `form` | 96 | Upper sine/random zone — less regular than pure sine, adds irregularity for envelope feel |
| `ramp` | 0 | No ramp modulation |
| `clock_division` | 2 (quarter_triplet) | Loose tempo sync |

**Why this works for 90s rock:** Weezer and contemporaries used auto-wah/envelope
filter for funk-tinged rhythm parts. The Wombtone cannot truly track pick attack
(that requires a dedicated envelope follower circuit), but slow + deep + high-
resonance approximates the spectral shape. The high `mix` (102/127) ensures the
swept tone dominates.

**Note on clock_division mapping (from catalog.py):**
positions=[whole, half, quarter_triplet, quarter, eighth, sixteenth]
- 0 = whole, 1 = half, 2 = quarter_triplet, 3 = quarter, 4 = eighth, 5 = sixteenth

---

### Mood MkII — Chorus Preset

**Mood MkII does not have a dedicated chorus algorithm.** The three `wet_channel`
modes are: reverb (0), delay (1), slip/granular (2). A very short delay with
moderate feedback and mix creates a convincing analog chorus effect — this is
how chorus pedals work internally (short modulated delay line). The Mood's
`clock` knob controlling sample rate acts as an indirect modulation source.

**Recommended new preset — `chorus-shimmer`:**

| Parameter | Value | CC | Rationale |
|-----------|-------|----|-----------|
| `wet_channel` | 1 (delay) | CC 21 | Delay mode — short time = chorus-like doubling |
| `time` | 22 | CC 14 | Short delay — ~10-12ms territory creates chorus-like pitch thickening |
| `mix` | 64 | CC 15 | Noon — balanced wet/dry for classic chorus blend |
| `wet_modify` | 50 | CC 17 | Moderate feedback — adds chorus depth without distinct slapback |
| `clock` | 90 | CC 18 | High clock rate (clean/hi-fi) — keep delay artifact-free |
| `routing` | 0 (in) | CC 22 | Process live signal directly |
| `loop_modify` | 64 | CC 19 | Neutral |
| `length` | 64 | CC 16 | Neutral |
| `ramp_speed` | 0 | CC 20 | No ramping |
| `loop` | 1 (tape) | CC 23 | Tape loop for warmth if looper engaged |
| `dip_r_trails` | 127 | CC 75 | TRAILS on — chorus fades cleanly on bypass |

**Why `time=22` for chorus:** At lower values the delay is short enough (sub-20ms)
that the brain fuses the delayed signal with the dry, producing perceived pitch
thickening rather than a distinct echo. Combined with `wet_modify=50` feedback,
this creates the doubled shimmer effect characteristic of 90s chorus guitar.

**routing toggle mapping (from catalog.py):**
positions=[in, loop_and_in, loop]
- 0 = in (process live signal), 1 = loop_and_in, 2 = loop only

**loop toggle mapping (from catalog.py):**
positions=[env, tape, stretch]
- 0 = env, 1 = tape, 2 = stretch

---

### HX Stomp — Mesa Boogie + 4x12 Cab Patch

**Existing reference: `Boogie PB.hlx`** (preset_number 38 in commented-out config)

Block chain in `Boogie PB.hlx`:
```
inputA -> block0(HD2_EQGraphic10Band) -> block1(HD2_AmpCaliIVLead) -> block2(FX Loop, off)
       -> block3(HD2_TremoloOpticalTrem, off) -> block4(HD2_DelayTransistorTape, off)
       -> block5(VIC_ReverbDynRoom) -> block6(HD2_CabMicIr_2x12BlueBell) -> outputA
```

**The existing `Boogie PB.hlx` uses a 2x12 BlueBell cab, not a 4x12.**
For 90s rock/Weezer tone the 4x12 is more appropriate. `Mesa Lead D.hlx`
confirms `HD2_CabMicIr_4x12CaliV30` is available and already dialed in for
Mesa-style tone. The V30-loaded 4x12 adds low-mid weight and the characteristic
Mesa snarl vs the BlueBell's cleaner, brighter character.

**Key amp model: `HD2_AmpCaliIVLead`** — the Mesa/Boogie Mark IV Lead channel.
Weezer Blue Album and Pinkerton were recorded through Mesa/Boogie Mark III and IV.
This is the correct model.

**Current `Boogie PB.hlx` amp parameters vs recommended for 90s rock:**

| Parameter | Current Value | Recommended for 90s Rock | Notes |
|-----------|--------------|--------------------------|-------|
| `LeadGain` | 0.30 | 0.45-0.55 | Increase for more sustain; current is too clean for rock |
| `LeadDrive` | 0.50 | 0.50 | Keep at noon — preamp drive stage |
| `Bass` | 0.09 | 0.25-0.35 | Very scooped currently; add body without muddiness |
| `Mid` | 0.40 | 0.45-0.55 | Current slightly scooped; Weezer tone is mid-present |
| `Treble` | 0.81 | 0.70-0.75 | Reduce slightly to prevent brightness clash with Brothers DIST |
| `Presence` | 0.34 | 0.40-0.50 | Increase — 90s rock had more upper-mid presence than current setting |
| `Master` | 0.50 | 0.60-0.70 | Push power section harder for natural power-tube saturation |
| `ChVol` | 0.88 | 0.85-0.90 | Output level, keep near current |

**Cab swap: `HD2_CabMicIr_2x12BlueBell` -> `HD2_CabMicIr_4x12CaliV30`**

`Mesa Lead D.hlx` cab parameters for reference:
```
LowCut: 19.9 Hz  (very low — keeps full body)
HighCut: 20100 Hz (open top end)
Mic: 10 (SM57 on-axis)
Position: 0.23 (close-miked toward center)
Distance: 1
Level: 0
```

**FX loop considerations:** The current signal chain (brothers -> wombtone ->
hx-stomp) means Brothers drives the HX Stomp input directly. This is correct
gain staging — no FX loop block changes needed. Leave the `HD2_FXLoopMono1`
block disabled in the new preset.

**New HLX preset:** Create a new file (e.g., `hlx/Rock Drive.hlx`) as a copy of
`Boogie PB.hlx` with the above amp adjustments and cab swap. Assign a new
preset_number (40 recommended, to avoid overwriting the deferred boogie-pb slot).

---

## Rock Scene Wiring

New scene in `mc6.config.scenes`:

```yaml
rock:
  description: "90s rock — Mesa Boogie drive with chorus and wah textures"
  presets:
    hx-stomp: rock-drive
    brothers: weezer-drive
    billy-strings-wombtone: envelope-filter
    mood: chorus-shimmer
```

MC6 Bank 1, Switch B -> scene: rock

---

## Supporting Libraries

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| rig-chasebliss | current (dev install) | MIDI CC dispatch for CBA devices | Already wired; no change needed |
| rig-hx | current (dev install) | PC message dispatch for HX Stomp | Already wired; no change needed |

---

## Development Tools

| Tool | Purpose | Notes |
|------|---------|-------|
| `rig validate` | Cross-reference check after each edit | Run after adding each preset; catches missing references before applying |
| HX Edit | Create/edit .hlx preset files | New rock patch must be exported as .hlx from HX Edit before referencing in rig.yaml |

---

## Alternatives Considered

| Category | Recommended | Alternative | When to Use Alternative |
|----------|-------------|-------------|------------------------|
| Brothers distortion mode | `gain_2_type=2` (dist) | `gain_2_type=1` (od) | OD if you want a smoother, more compressed character like Japandroids/Pixies style |
| Wombtone auto-wah approach | Slow phaser with high depth/resonance | External envelope filter pedal | Only if you add a dedicated EF pedal to the signal chain in a future milestone |
| Mood chorus method | Short delay (wet_channel=delay, time=22) | Reverb with classic voicing (dip_r_classic=127) | Reverb approach if you want more spatial dimension vs tight doubling |
| HX cab | HD2_CabMicIr_4x12CaliV30 | HD2_CabMicIr_2x12BlueBell (current) | 2x12 BlueBell if targeting cleaner/brighter Fender-adjacent tone rather than Mesa |
| New HLX preset number | 40 | Reactivate 38 (boogie-pb) | Reactivate 38 only if you decide the old boogie-pb variant is no longer needed |

---

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| `dip_hi_gain_1/2` on Brothers DIST preset | +25% gain pushes into fizzy, indistinct territory; appropriate for metal not 90s rock | Standard gain range via `gain_2` at 76 |
| `treble_boost=0` (full_sun) | Rangemaster-style treble boost stacks harsh brightness on top of DIST mode — ice-pick | Keep `treble_boost=1` (off) |
| `dip_master=1` on single-channel DIST preset | MASTER mode locks VOL2 as global output controller; useful for stacking both channels but unnecessary complexity for single-channel | Keep dip_master=0 |
| `wet_channel=2` (slip/granular) on Mood for chorus | Granular slip mode produces re-pitched grain clouds, not chorus | `wet_channel=1` (delay) with time=22 |
| `HD2_AmpUSDeluxeVib` for rock patch | Fender-style amp; wrong character for Mesa-style rock saturation | `HD2_AmpCaliIVLead` |
| `channel_2_bypass: 0` in the new DIST preset | That disables Ch2 (the distortion); use only in boost-only presets | Omit the key or set `channel_1_bypass: 0` if you want Ch1 bypassed instead |

---

## Stack Patterns by Variant

**If you want the Weezer Blue Album clean-ish wall-of-sound:**
- Brothers: `transparent-lift` (Ch1 BOOST only)
- HX Stomp: rock-drive with Master at 0.65
- Mood: `chorus-shimmer` active
- Wombtone: bypassed or `slow-vibe` at low mix

**If you want Pinkerton-era harder drive:**
- Brothers: `weezer-drive` (Ch2 DIST active)
- HX Stomp: rock-drive with LeadGain at 0.55
- Wombtone: bypassed
- Mood: `chorus-shimmer` at low mix (time=22, mix=45)

**If you want the funk/wah texture:**
- Brothers: `transparent-lift` or bypassed
- Wombtone: `envelope-filter` active, play rhythm with pick attack variation
- HX Stomp: rock-drive at lower gain
- Mood: `chorus-shimmer` optional

---

## Version Compatibility

| Package / File | Version | Notes |
|----------------|---------|-------|
| rig-chasebliss catalog | Current (verified 2026-06-18) | BROTHERS_AM_CONTROLS, WOMBTONE_MKII_CONTROLS confirmed matching rig.yaml usage |
| HX Stomp firmware | v3.71 (from HLX build_sha) | HLX file format version 6; all referenced block models present in existing files |
| rig.yaml schema | v1.2 single-file | No structural changes needed; add presets, add scene, add MC6 switch |

---

## Sources

- `/Users/derekgliwa/dev/rig-cli/packages/rig-chasebliss/src/rig_chasebliss/catalog.py` — authoritative CC numbers, ranges, toggle positions for Brothers AM, Wombtone MkII, Mood MkII (HIGH confidence — canonical source of truth)
- `/Users/derekgliwa/dev/rig/rig.yaml` — existing preset values and inline annotations including all commented-out presets (HIGH confidence)
- `/Users/derekgliwa/dev/rig/hlx/Boogie PB.hlx` — existing Mesa Boogie patch block structure and all parameter values (HIGH confidence)
- `/Users/derekgliwa/dev/rig/hlx/Mesa Lead D.hlx` — confirmed `HD2_CabMicIr_4x12CaliV30` cab model availability and Mesa amp parameter ranges (HIGH confidence)
- Internal knowledge of Weezer Blue Album/Pinkerton recording tone (Mesa Boogie Mark III/IV, KOT overdrive, chorus guitar texture) (MEDIUM confidence — no external verification available in this session)

---

*Stack research for: 90s rock (Weezer-style) guitar presets — v1.3 milestone*
*Researched: 2026-06-18*
