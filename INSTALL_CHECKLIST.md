# ESPyRain Controller Install Checklist
This guide assumes you already have Home Assistant and ESPHome installed, and that you can access the Home Assistant file system through SSH, Samba, Studio Code Server, or the File Editor add-on.

## 1. Copy the project files into Home Assistant
Download the ZIP from:
- `https://github.com/juhurus/ESPyRain_Controller`

Unpack it, then treat your Home Assistant config folder as the root, usually:
- `/config/`

Note that there are two different `secrets.yaml` files:
- one for Home Assistant
- one for ESPHome

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
        `-- your_property_overhead.jpg
```

Copy from the downloaded ZIP like this:
- `ESPyRain_Controller-main/package_sources/` -> `/config/package_sources/`
- `ESPyRain_Controller-main/packages/` -> `/config/packages/`
- `ESPyRain_Controller-main/ESPyRain-controller/` -> `/config/esphome/ESPyRain-controller/`
- `images/www/espyrain/` -> `/config/www/espyrain/`

Optional, if you want to use the supplied ESPyRain theme together with the full HA dashboard rather than the simple dashboard:
- `ESPyRain_Controller-main/themes/espyrain_theme.yaml` -> `/config/themes/espyrain_theme.yaml`

## 2. Create a temporary ESPHome device
There are different ways to do this depending on your HA and ESPHome version. The method below works reliably:
- Click **New Device** in the ESPHome dashboard
- Click **Continue**
- Choose **New Device Setup**
- Name it `Temp` for now. We will delete it later.
- Enter your Wi-Fi SSID
- Enter your Wi-Fi password
- Click **Next**
- Select the board type you are going to use
- Click **Skip**
- ESPHome will create `temp.yaml`
- Copy `/config/esphome/ESPyRain-controller/espyrain_controller_wrapper.yaml` into `/config/esphome/`

## 3. Edit site-specific ESPHome settings
Review and update `/config/esphome/espyrain_controller_wrapper.yaml` before compiling.

At minimum, check:
- board type
- framework type
- `user_timezone`

Hint:
- take the ESP board details from the `temp.yaml` you created earlier

Examples:

```yaml
esp32:
  board: esp32-s3-devkitc-1
  framework:
    type: esp-idf
```

```yaml
substitutions:
  user_timezone: America/Denver
```

### Optional static IP configuration
By default ESPyRain uses DHCP.

If you require a static IP address, uncomment the appropriate lines in `/config/esphome/espyrain_controller_wrapper.yaml` and edit the network settings to suit your network.

Example:

```yaml
wifi:
  manual_ip:
    static_ip: 192.168.1.88
    gateway: 192.168.1.1
    subnet: 255.255.255.0
    dns1: 192.168.1.50
    dns2: 8.8.8.8
```

## 4. Add the required ESPHome secrets
Add these lines to `/config/esphome/secrets.yaml` and edit them with your own details.

Hint:
- use the API key from the `temp.yaml` you created earlier

```yaml
wifi_ssid: "your_wifi_ssid"
wifi_password: "your_wifi_password"

espyrain_ota_password: "your_espyrain_ota_password"
espyrain_api_key: "your_espyrain_api_key"

espyrain_ap_ssid: "espyrain_ap_ssid"
espyrain_ap_password: "your_espyrain_ap_password"
```

## 5. Compile and flash the ESPHome device
There are several ways to do this. The method below is known to work well:
1. Open the ESPHome dashboard in Home Assistant.
2. Click the three-dot menu on the `espyrain_controller` card.
3. Click **Validate**.
4. If validation fails, fix the issue first. If validation succeeds, continue.
5. Click **Install**.
6. Select **Plug into this computer**.
7. Wait until the file is ready for download.
8. Download the file.
9. Choose **Factory format for use with ESPHome Web**.
10. Open **ESPHome Web**.
11. Click **Connect**.
12. Select your device port.
13. Click **Install**.
14. Choose the file you just downloaded.
15. Click **Install** again.
16. When flashing is complete, close the ESPHome Web tab.
17. Reboot the ESP by unplugging and reconnecting the USB cable.
18. Go back to the ESPHome dashboard.
19. `espyrain_controller` should now show **Online**.

Next you need to add ESPyRain as a newly discovered device in Home Assistant:
- Go to **Settings -> Devices & Services**
- You should see `ESPyRain Controller (espyrain-controller)` under discovered devices
- Click **Add** to create all the ESPyRain entities

Confirm that entities appear in **Developer Tools -> States**, for example:
- `sensor.espyrain_controller_status`
- `button.espyrain_controller_pause`
- `number.espyrain_controller_number_of_stations`
- to see all related entities, type `espyrain` into **Filter entities**

Please note:
- `button.*` entities may show `unknown` in HA. This is normal for stateless action buttons.

From now on you can usually select **wireless** to update and reflash the device remotely instead of connecting it by USB.

At this stage you can delete the temporary `Temp` device because it is no longer required.

## 6. Home Assistant configuration
In `configuration.yaml`, make sure Home Assistant includes packages:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

If you want to use the supplied `espyrain_theme`, also add:

```yaml
frontend:
  themes: !include_dir_merge_named themes
```

## 7. Restart Home Assistant and validate the config
After copying the files as explained above:
1. Check the YAML / configuration in Home Assistant.
2. Restart Home Assistant.
3. Confirm there are no package or secret errors.
4. Confirm that the new helpers exist, for example:
   - `input_text.espyrain_station_1_name`
   - `input_boolean.espyrain_notifications`
   - `input_text.espyrain_notify_service`

## 8. Choose a dashboard path
You have two dashboard options.

### Option A: Simple dashboard
Use this first if you want the easiest install path.

File:
- `HA_dashboards/ESPyRain_dashboard_simple.yaml`

This dashboard:
- uses only built-in Home Assistant cards
- does not require HACS
- does not require custom cards
- does not require image assets

To install:
- Add a new dashboard from scratch and paste the content from `ESPyRain_dashboard_simple.yaml` into the raw configuration editor, replacing everything in that new dashboard.

### Option B: Full dashboard
Use this if you want the richer ESPyRain dashboard.

File:
- `HA_dashboards/ESPyRain_dashboard_full.yaml`

This dashboard:
- uses custom cards
- uses image assets under `/config/www/espyrain/`
- is much more polished, but has more setup steps

Required custom cards for the full dashboard:
- `button-card`
- `card-mod`
- `fold-entity-row`
- `large-number-input-card`
- `mini-graph-card`
- `template-entity-row`
- `time-picker-card`

For more detail, see:
- `HA_dashboards/README.md`

## 9. Import or create the dashboard
Once Home Assistant and the ESP are both up:
1. Add the simple dashboard first, or the full dashboard if you already installed its dependencies.
2. Refresh the browser once after importing the dashboard.
3. If you are using the full dashboard, verify that the image paths load correctly.

## 10. First-time controller setup
Once the ESPyRain entities appear in Home Assistant:
1. Set the number of stations you need.
2. Set the number of schedules you need.
3. Set rain delay to `0`.
4. Turn winter mode off unless you actually want it on.

Then configure the stations:
- name each station as you wish
- enable the stations you currently use
- set runtime in minutes
- assign the GPIO pin for each valve
- set coupled stations if needed
- enable the master valve / pump if used
- choose the master valve / pump GPIO pin
- set the master valve delay in milliseconds

Where to find the coupled-station toggles:
- **Simple dashboard:** open the `Coupling` tab. Each station has its own card with `S1` to `S16` toggles.
- **Full dashboard:** open the `Stations` tab, then open the coupling section for the station you want to edit.

Before configuring schedules, test your stations manually:
- open the manual run controls
- choose a runtime
- choose the station
- start a manual run
- confirm the expected valve/output operates

Once your stations work correctly, configure the schedules:
- name each schedule
- enable the schedules you want to use
- set start times
- set seasonal adjust
- choose the run days / interval mode
- choose which stations each schedule runs
- set soak cycles if needed

Where to find the schedule station-selection toggles:
- **Simple dashboard:** open the `Schedule Stations` tab. Each schedule has its own card with `S1` to `S16` toggles.
- **Full dashboard:** open the `Schedules` tab, then open the station-selection section for the schedule you want to edit.

## 11. Notification check
Turn ESPyRain notifications on if you want to be notified by the many events that trigger messages. Notifications are especially useful during setup because they show what is supposed to run and when.

Notifications can also report:
- automatic resume after a pause timeout
- queue limit reached when a new run cannot be added

1. Leave **Notification Service(s)** blank to get persistent notifications in HA.
2. If you want push notifications to your phone or another supported service, enter one or more comma-separated `notify.*` targets that already work in Home Assistant.

Examples:
- `notify.mobile_app_my_phone`
- `notify.telegram_bot_xxx_xxx, notify.mobile_app_my_phone`

