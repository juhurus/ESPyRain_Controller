# ESPHome Sprinkler Controller for Home Assistant

## Install Checklist

### 1. Preferred HA layout
Use the package layout as the primary source:
- packages/esphome_sprinkler_controller_for_home_assistant.yaml
- package_sources/esphome_sprinkler_controller_for_home_assistant/

HA must include packages in configuration.yaml:

`yaml
homeassistant:
  packages: !include_dir_named packages
`

Legacy compatibility files still exist under HA_files/, but they are no longer the source of truth.

Included HA domains in the package:
- input_boolean
- input_datetime
- input_number
- input_select
- input_text
- utomation
- script
- sensor
- 	emplate

### 2. Required secrets
Add these to your HA `secrets.yaml`:

```yaml
sprinkler_telegram_notifier: notify.your_telegram_notifier_entity
```

Add these to your ESPHome secrets file:

```yaml
wifi_ssid: your_wifi_name
wifi_password: your_wifi_password
sprinkler_api_key: your_api_encryption_key
sprinkler_ota_password: your_ota_password
sprinkler_ap_password: your_fallback_ap_password
```

Notes:
- `sprinkler_telegram_notifier` is a Home Assistant notifier entity ID, not a raw Telegram chat ID.
- Notifications can still be disabled in HA with `input_boolean.sprinkler_notifications`.

### 3. ESPHome package defaults
Package-safe defaults now use DHCP.

Shared file:
- `ESP_sprinkler-controller/base.yaml`

If you want static IP networking, create a local override using:
- `ESP_sprinkler-controller/network_static_ip.example.yaml`

Example:

```yaml
wifi:
  manual_ip: !include ESP_sprinkler-controller/network_static_ip.local.yaml
```

Do not ship static IP values inside the shared package.

### 4. Values that should be local overrides, not secrets
These are site-specific and should be edited locally per install:
- ESP `device_name`
- ESP `friendly_name`
- ESP board/hardware target
- station GPIO mapping
- number of stations if hardware differs
- station names
- schedule names
- coupling settings if hardware layout differs

### 5. Dashboard assets and custom cards
Active dashboard file:
- `HA_dashboard/HA dashboard.yaml`

Required custom cards / plugins:
- `button-card`
- `card-mod`
- `fold-entity-row`
- `large-number-input-card`
- `mini-graph-card`
- `template-entity-row`
- `time-picker-card`

Required dashboard image assets:
- `/local/background.jpg`
- `/local/sprinklers/backyard_overhead_vert.jpg`

If those files are not present, either:
- add matching files under HA `www/`, or
- update `HA_dashboard/HA dashboard.yaml` to your local image paths

For dependency details, see:
- `HA_dashboard/README.md`

### 6. HA reload/restart order
After copying files:
1. restart Home Assistant once for new helpers
2. reload templates
3. reload automations
4. reload scripts
5. reload dashboard / refresh browser

### 7. ESP build/redeploy
After copying ESP files:
1. create a local wrapper YAML if needed
2. verify secrets exist
3. compile
4. flash
5. confirm entities appear in HA

### 8. Legacy export sync
If you still need `HA_files/` for compatibility, regenerate it from the package source:

```powershell
tools\sync_ha_package_to_legacy.ps1
```

### 9. Packaging status
Already package-safe:
- Telegram notifier moved to HA secrets
- ESP Wi-Fi/API/OTA credentials moved to secrets
- ESP network defaults now use DHCP
- HA package entrypoint created under `packages/`
- HA source moved to `package_sources/`

Still intentionally local/site-specific:
- dashboard image files
- hardware pin mapping
- device/friendly naming
- local network static IP choice

