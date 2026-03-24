# HA Package Source Layout

This folder is the package source for:
- ESPyRain Controller for Home Assistant

## Source of truth
Edit here:
- `package_sources/espyrain_for_home_assistant/`

Do not edit `HA_files/` as the primary source.

## Home Assistant package entrypoint
The actual HA package file loaded by Home Assistant is:
- `packages/espyrain_for_home_assistant.yaml`

That file includes this source folder.

## Notifications
- No notifier secret is required.
- If `input_text.espyrain_notify_service` is blank, ESPyRain falls back to Home Assistant persistent notifications.
- You can optionally enter one or more comma-separated `notify.*` services in `input_text.espyrain_notify_service`.

## Sync to legacy layout
If you still need `HA_files/` for compatibility, regenerate it from this source:

```powershell
tools\sync_ha_package_to_legacy.ps1
```

## Notes
- This package does not include dashboard assets.
- Dashboard YAML remains separate under `HA_dashboard/`.
