# Dashboard Dependencies

Active dashboard file:
- `HA_dashboard/HA dashboard.yaml`

## Required custom cards / plugins
This dashboard currently uses these custom cards:
- `button-card`
- `card-mod`
- `fold-entity-row`
- `large-number-input-card`
- `mini-graph-card`
- `template-entity-row`
- `time-picker-card`

The dashboard also references built-in wrapper types that appear as custom-prefixed entries in YAML:
- `custom:hui-conditional-card`
- `custom:hui-markdown-card`

Those are HA frontend wrappers, not external HACS dependencies.

## Required image assets
The dashboard currently expects these files under HA `www/`:
- `/local/background.jpg`
- `/local/sprinklers/backyard_overhead_vert.jpg`

If those assets are missing, either:
- add files with those exact paths, or
- edit `HA_dashboard/HA dashboard.yaml` to point to your own images

## Notes
- The manual-run map currently depends on the overhead image path above.
- `card-mod` is heavily used for styling and border animations.
- If a required custom card is missing, parts of the dashboard will fail to render.
