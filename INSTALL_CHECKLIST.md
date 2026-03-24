# ESPyRain Controller Install Checklist
This guide assumes you already have Home Assistant and ESPHome installed, and that you can access your Home Assistant file system through SSH, Samba, Studio Code Server, or the File Editor add-on.
## 1. Copy the project files into Home Assistant
Download the ZIP from:
- `https://github.com/juhurus/ESPyRain_Controller`

Unpack it, then treat your Home Assistant config folder as the root, usually:
- `/config/`

Note that there are two secrets.yaml files one for HA and the other for ESPHome.

Your final layout should look like this:

```text
/config/
|-- configuration.yaml
|-- package_sources/
|   `-- espyrain_for_home_assistant/
|       |-- automations/
|       |-- scripts/
|       |-- sensors/
|       |-- templates/
|       |-- input_boolean.yaml
|       |-- input_datetime.yaml
|       |-- input_number.yaml
|       |-- input_select.yaml
|       |-- input_text.yaml
|       `-- README.md
|-- packages/
|   `-- espyrain_for_home_assistant.yaml
|-- esphome/
|   |-- espyrain_controller.yaml
|   |-- secrets.yaml
|   `-- ESPyRain-controller/
|       |-- VERSION
|       |-- base.yaml
|       |-- package.yaml
|       |-- entities_schedules.yaml
|       |-- entities_stations.yaml
|       |-- entities_system.yaml
|       |-- globals_schedules.yaml
|       |-- globals_stations.yaml
|       |-- network_static_ip.example.yaml
|       |-- schedule_interval.yaml
|       `-- ...other ESPyRain controller YAML files
|-- themes/
|   `-- espyrain_theme.yaml
`-- www/
    `-- espyrain/
        |-- espyrain_background.jpg
        `-- test_property_overhead.jpg
```

Copy from the downloaded ZIP like this:
- `ESPyRain_Controller-main/package_sources/` -> `/config/package_sources/`
- `ESPyRain_Controller-main/packages/` -> `/config/packages/`
- ESPyRain-controller/espyrain_controller.yaml` -> `/config/esphome/espyrain_controller.yaml`
- `ESPyRain_Controller-main/ESPyRain-controller/` -> `/config/esphome/ESPyRain-controller/`
- `ESPyRain_Controller-main/themes/espyrain_theme.yaml` -> `/config/themes/espyrain_theme.yaml`
- `images/www/espyrain/` -> `/config/www/espyrain/`

## 2. Create the ESPHome device entry
In the ESPHome dashboard in Home Assistant:
1. Click **New Device**.
2. Create a new device named `espyrain_controller`.
3. Choose your ESP board type.
4. Complete the first-use wizard and note the generated API encryption key.

For the very first flash, use USB or the ESPHome web flasher path. After that, ESPyRain can be updated normally through ESPHome.
## 3. Home Assistant configuration
In `configuration.yaml`, make sure Home Assistant includes packages:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

If you want to use the supplied theme, also add:

```yaml
frontend:
  themes: !include_dir_merge_named themes
```

## 3. Edit site-specific ESPHome settings
Review `/config/esphome/ESPyRain-controller/base.yaml` before compiling.

At minimum, check:
- board type
- framework type
- timezone

Examples:

```yaml
esp32:
  board: esp32-s3-devkitc-1
  framework:
    type: esp-idf
```

```yaml
time:
  - platform: sntp
    id: esp_time
    timezone: Australia/Perth
```

## 4. Required ESPHome secrets
Add these to `/config/esphome/secrets.yaml`:

```yaml
wifi_ssid: your_wifi_name
wifi_password: your_wifi_password
espyrain_api_key: your_api_encryption_key
espyrain_ota_password: your_ota_password
```

Notifications do not require any secret.

Default notification behavior:
- If `input_boolean.espyrain_notifications` is on and `input_text.espyrain_notify_service` is blank, ESPyRain creates a Home Assistant persistent notification.

Optional notification targets:
- Enter one `notify.*` target in `input_text.espyrain_notify_service`
- Or enter multiple comma-separated targets

Examples:
- `notify.mobile_app_my_phone`
- `notify.telegram_bot_123456789_987654321, notify.mobile_app_my_phone`

## 5. Optional static IP configuration
By default ESPyRain uses DHCP.

If you want a static IP:
1. Copy or rename `ESPyRain-controller/network_static_ip.example.yaml` to `ESPyRain-controller/network_static_ip.yaml`
2. Edit it with your IP, gateway, subnet, and DNS values
3. Uncomment the `manual_ip` line in `ESPyRain-controller/base.yaml`

Expected result in `base.yaml`:

```yaml
wifi:
  manual_ip: !include network_static_ip.yaml
```


## 6. Choose a dashboard path
You have two dashboard options.

### Option A: Starter dashboard
Use this first if you want the easiest install path.

File:
- `HA_dashboard/HA_dashboard_starter.yaml`

This dashboard:
- uses only built-in Home Assistant cards
- does not require HACS
- does not require custom cards
- does not require image assets

### Option B: Full dashboard
Use this if you want the richer ESPyRain dashboard.

File:
- `HA_dashboard/HA_dashboard.yaml`

This dashboard:
- uses custom cards
- uses image assets under `/config/www/espyrain/`
- is more polished but has more setup steps

Required custom cards for the full dashboard:
- `button-card`
- `card-mod`
- `fold-entity-row`
- `large-number-input-card`
- `mini-graph-card`
- `template-entity-row`
- `time-picker-card`

For more detail, see:
- `HA_dashboard/README.md`

## 7. Restart Home Assistant and validate config
After copying the files:
1. Check YAML / configuration in Home Assistant
2. Restart Home Assistant
3. Confirm there are no package or secret errors
4. Confirm the new helpers exist, for example:
   - `input_text.espyrain_station_1_name`
   - `input_boolean.espyrain_notifications`
   - `input_text.espyrain_notify_service`

## 8. Compile and flash ESPyRain
In ESPHome:
1. Open `espyrain_controller.yaml`
2. Verify the package includes resolve correctly
3. Compile the firmware
4. Flash the ESP
5. Wait for the device to come online in Home Assistant

After the first successful flash, confirm entities appear, for example:
- `sensor.espyrain_controller_status`
- `button.espyrain_controller_pause`
- `number.espyrain_controller_number_of_stations`

## 9. Import or create the dashboard
Once Home Assistant and the ESP are both up:
1. Add the starter dashboard first, or the full dashboard if you already installed its dependencies
2. Refresh the browser once after importing the dashboard
3. If using the full dashboard, verify the image paths load correctly

## 10. First-time controller setup
Once the ESPyRain entities appear in Home Assistant:
1. Set the number of stations
2. Set the number of schedules
3. Set rain delay to `0`
4. Turn winter mode off unless you actually want it on

Then configure stations:
- name each station
- enable the stations you use
- set runtime in minutes
- assign the GPIO pin for each valve
- set coupled stations if needed
- enable master valve / pump if used
- choose the master valve / pump GPIO pin
- set the master valve delay

Before configuring schedules, test your stations manually:
- open the manual run controls
- choose a runtime
- choose a station
- start a manual run
- confirm the expected valve/output operates

Then configure schedules:
- name each schedule
- enable the schedules you want to use
- set start times
- set seasonal adjust
- choose the run days / interval mode
- choose which stations each schedule runs
- set soak cycles if needed

## 11. Notification check
Optional but recommended:
1. Leave `input_text.espyrain_notify_service` blank and confirm a persistent notification works
2. If you want push notifications, enter one or more `notify.*` targets
3. Test again

Examples:
- `notify.mobile_app_my_phone`
- `notify.telegram_bot_123456789_987654321, notify.mobile_app_my_phone`
