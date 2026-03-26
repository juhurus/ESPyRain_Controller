# Dashboard Options

Starter dashboard with only built-in Home Assistant cards:
- `HA_dashboard/ESPyRain_dashboard_simple.yaml`

## Starter dashboard notes
- No HACS or custom cards required.
- No image assets required.
- Good for first install, testing, and minimal setups.

Starter dashboard with only built-in Home Assistant cards:
- `HA_dashboard/ESPyRain_dashboard_simple.yaml`

Full custom-card dashboard:
- `HA_dashboard/ESPyRain_dashboard_full.yaml`
## Full dashboard dependencies
This dashboard currently uses these custom cards:
- `button-card`
- `card-mod`
- `fold-entity-row`
- `large-number-input-card` (search for `large-number-input)
- `mini-graph-card`
- `template-entity-row`
- `time-picker-card`

The full dashboard also references built-in wrapper types that appear as custom-prefixed entries in YAML:
- `custom:hui-conditional-card`
- `custom:hui-markdown-card`

Those are HA frontend wrappers, not external HACS dependencies.

## Required image assets for the full dashboard
The full dashboard currently expects these files under HA `www/espyrain/`:
- `/local/espyrain/espyrain_background.jpg`
- `/local/espyrain/your_property_overhead.jpg`

```
`-- www/
    `-- espyrain/
        |-- espyrain_background.jpg
        `-- your_property_overhead.jpg
```

For best result use the supplied ESPyRain Theme. Make sure you copied ESPyRain Theme into your themes folder and restarted HA.

If those assets are missing, either:
- add files with those exact paths, or
- edit `HA_dashboard/ESPyRain_dashboard_full.yaml` to point to your own images


