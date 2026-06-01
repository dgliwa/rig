# Derek's Guitar Rig

Infrastructure-as-Code configuration for my guitar rig.

## Prerequisites

```bash
# Install rig-cli globally (see rig-cli README for details)
uv tool install /path/to/rig-cli

# Or run directly without installing
uv run --directory /path/to/rig-cli rig validate
```

See [rig-cli](https://github.com/dgliwa/rig_cli) for setup and development instructions.

## Directory Layout

```
rig.yaml            # Top-level rig definition
signal-chain.yaml   # Ordered pedal signal chain
mc6.yaml            # Morningstar MC6 bank/scene mapping
pedals/             # Pedal definitions and presets
scenes/             # Scene definitions (complete rig states)
imports/            # Raw preset files for ingestion
generated/          # Generated artifacts (MC6 configs, docs)
.rig/               # Local state (auto-generated)
```

## Quick Start

```bash
rig validate
rig plan
rig apply
```

## Workflow

1. Edit YAML configs to define pedals, presets, and scenes
2. `rig validate` — check for errors
3. `rig plan` — preview what would change
4. `rig apply` — walk through each device, confirm changes
5. Commit everything to Git
