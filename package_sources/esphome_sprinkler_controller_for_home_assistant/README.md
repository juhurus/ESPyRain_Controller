# HA Package Source Layout

This folder is the package source for:
- ESPHome Sprinkler Controller for Home Assistant

## Source of truth
Edit here:
- `package_sources/esphome_sprinkler_controller_for_home_assistant/`

Do not edit `HA_files/` as the primary source.

## Home Assistant package entrypoint
The actual HA package file loaded by Home Assistant is:
- `packages/esphome_sprinkler_controller_for_home_assistant.yaml`

That file includes this source folder.

## Included HA domains
- `input_boolean`
- `input_datetime`
- `input_number`
- `input_select`
- `input_text`
- `automation`
- `script`
- `sensor`
- `template`

## Sync to legacy layout
If you still need `HA_files/` for compatibility, regenerate it from this source:

```powershell
tools\sync_ha_package_to_legacy.ps1
```

## Notes
- This package expects the notifier secret:
  - `sprinkler_telegram_notifier`
- This package does not include dashboard assets.
- Dashboard YAML remains separate under `HA_dashboard/`.
