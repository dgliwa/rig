# Phase 6: HX Stomp Mesa-Rock Entry - Context

**Gathered:** 2026-06-18
**Status:** Ready for planning

<domain>
## Phase Boundary

Add a single `mesa-rock` preset entry to the `hx-stomp` device's `presets:` list in `rig.yaml`. The entry must be active (uncommented), reference `hlx/MesaRock.hlx`, and pass `rig validate` (file-existence check is advisory, not blocking).

Creating the `.hlx` patch file itself is out of scope — that is done manually in HX Edit by the user.

</domain>

<decisions>
## Implementation Decisions

### Preset Slot Number
- **D-01:** Use `preset_number: 37`. The commented `marshall` entry currently occupies this slot annotation — `mesa-rock` takes slot 37 instead. The other commented entries (38, 39) are unaffected.

### HX File Path
- **D-02:** `hlx_file: hlx/MesaRock.hlx` — matches what the user will name the file in HX Edit. No spaces in the filename.

### Entry Status
- **D-03:** Entry is **active (uncommented)** in `rig.yaml`. Since `rig validate` treats the file-existence check as advisory, the entry can be live even before `MesaRock.hlx` is created in HX Edit.

### Notes Field
- Claude's discretion: short description capturing the Mesa Boogie amp character (e.g., "Mesa Boogie amp model, high-gain rock tone"). No strong preference expressed.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Config Schema
- `rig.yaml` — Primary config file; add the new preset under `hx-stomp.presets`. Study the existing `clean` entry (lines ~171–175) for the exact field pattern: `id`, `name`, `preset_number`, `hlx_file`, `notes`.

### Requirements
- `.planning/REQUIREMENTS.md` — HX-01 is the acceptance criterion for this phase: rig.yaml contains a `mesa-rock` HX Stomp preset entry pointing to a user-provided `.hlx` file.
- `.planning/ROADMAP.md` §Phase 6 — Success criteria: (1) mesa-rock preset entry under hx-stomp, (2) valid `hlx_file` path, (3) `rig validate` accepts without errors.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `hx-stomp.presets[clean]` in `rig.yaml` (~lines 171–175) — Direct template for the new entry. Same fields: `id`, `name`, `preset_number`, `hlx_file`, `notes`.

### Established Patterns
- HX preset entry structure: `id`, `name`, `preset_number`, `hlx_file`, `notes`
- Commented amp presets (marshall=37, boogie-pb=38, doogie-pb=39) can be left as-is or removed; `mesa-rock` simply takes slot 37 as an active entry.

### Integration Points
- Phase 7 `rock` scene will reference `hx-stomp: mesa-rock` by ID — the `id:` value must be exactly `mesa-rock` for that cross-reference to resolve.
- `rig validate` checks that preset IDs referenced in scenes exist — the name must match exactly.

</code_context>

<specifics>
## Specific Ideas

- User confirmed `hlx/MesaRock.hlx` as the exact filename (no spaces) — this differs from the existing `hlx/Clean.hlx` pattern (which uses spaces in commented entries like `hlx/Marshall D.hlx`). Use `MesaRock.hlx` exactly.

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope.

</deferred>

---

*Phase: 6-HX Stomp Mesa-Rock Entry*
*Context gathered: 2026-06-18*
