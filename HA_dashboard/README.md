# Dashboard Options

Full custom-card dashboard:
- `HA_dashboard/HA_dashboard.yaml`

Starter dashboard with only built-in Home Assistant cards:
- `HA_dashboard/HA_dashboard_starter.yaml`

## Full dashboard dependencies
This dashboard currently uses these custom cards:
- `button-card`
- `card-mod`
- `fold-entity-row`
- `large-number-input-card`
- `mini-graph-card`
- `template-entity-row`
- `time-picker-card`

The full dashboard also references built-in wrapper types that appear as custom-prefixed entries in YAML:
- `custom:hui-conditional-card`
- `custom:hui-markdown-card`

Those are HA frontend wrappers, not external HACS dependencies.

## Required image assets for the full dashboard
The full dashboard currently expects these files under HA `www/`:
- `/local/background.jpg`
- `/local/sprinklers/backyard_overhead_vert.jpg`

If those assets are missing, either:
- add files with those exact paths, or
- edit `HA_dashboard/HA_dashboard.yaml` to point to your own images

## Starter dashboard notes
- No HACS or custom cards required.
- No image assets required.
- Good for first install, testing, and minimal setups.
- Once users want the richer UI, they can switch to the full dashboard later.
