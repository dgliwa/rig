# rig

Project: https://github.com/dgliwa/rig

## Project Info

- Personal guitar rig configuration. Depends on rig-cli.
- YAML configs only — no application code.
- `rig plan`, `rig apply`, `rig validate` are the main workflows.
- Store everything in Git. Generated artifacts go in `generated/`.

## Agent Rules

- **Keep README.md in sync.** When changing workflows, directory layout,
  prerequisites, or any user-facing aspect of the rig repo, update
  README.md to match. It should always reflect the current state.
- Pedal definitions in `pedals/*.yaml`, presets in `pedals/<id>/presets/*.yaml`.
- Scenes define a complete rig state — one pedal + preset mapping per scene.
- Generated files (MC6 configs, docs) live in `generated/` and are committed.
