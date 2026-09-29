# ESPyRain Controller Install Checklist
This guide assumes you already have Home Assistant and ESPHome installed, and that you can access the Home Assistant file system through SSH, Samba, Studio Code Server, or the File Editor add-on.

## 1. Copy the project files into Home Assistant
Download the ZIP from:
- [ESPyRain_Controller on GitHub](https://github.com/juhurus/ESPyRain_Controller)

Unpack it, then treat your Home Assistant config folder as the root, usually:
- `/config/`

Note that there might be two different `secrets.yaml` files:
- one for Home Assistant
- one for ESPHome

Copy from the downloaded ZIP like this:
- `ESPyRain_Controller-main/package_sources/` -> `/config/package_sources/`
- `ESPyRain_Controller-main/packages/` -> `/config/packages/`
- `ESPyRain_Controller-main/ESPyRain-controller/` -> `/config/esphome/ESPyRain-controller/`
- `images/www/espyrain/` -> `/config/www/espyrain/`

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
|   |-- espyrain_controller_wrapper.yaml
|   `-- ESPyRain-controller/
|       |-- VERSION
|       |-- base.yaml
|       |-- package.yaml
|       |-- entities_schedules.yaml
|       |-- entities_stations.yaml
|       |-- entities_system.yaml
|       |-- globals_schedules.yaml
|       |-- globals_stations.yaml
|       |-- globals_system.yaml
|       |-- schedule_interval.yaml
|       |-- secrets_example.yaml
|       `-- ...other ESPyRain controller YAML files
|-- themes/
|   `-- espyrain_theme.yaml
`-- www/
    `-- espyrain/
        |-- espyrain_background.jpg
        `-- your_property_overhead.jpg
```


Optional, if you want to use the supplied ESPyRain theme together with the full HA dashboard rather than the simple dashboard:
- `ESPyRain_Controller-main/themes/espyrain_theme.yaml` -> `/config/themes/espyrain_theme.yaml`


## 2. Home Assistant configuration
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


## 3. Create a new ESPHome device
There are different ways to do this depending on your HA and ESPHome version. This method below works for me:
- open "ESPHome Device Builder"
- add new device
- create new project
- select your ESP board
- name device e.g. "ESPyRain Controller"
- go through the tabs and setup ESPHome Core, Platform, Logger, API, OTA, WiFI and Captive Portal

- add configuration -> Packages - and paste this yaml:
```yaml
packages:
  integrations_auto_run_block: !include ESPyRain-  controller/integrations_auto_run_block.yaml
```
 
 
- add configuration -> Substitutions - and paste this yaml:
```yaml
substitutions:
  user_timezone: America/Denver
```

- adjust the user_timezone to your location

### Your config should look similar to this example:
*** Note: By default ESPyRain uses DHCP. The example below uses a static IP address.
```yaml
# Board: ESP32-S3 DevKitC-1 (Espressif)
# Definition: definitions/boards/esp32-s3-devkitc-1/manifest.yaml

esphome:
  name: espyrain89
  friendly_name: ESPyRain89
esp32:
  variant: ESP32S3
  flash_size: 16MB
  framework:
    type: esp-idf

logger:
  level: INFO

api:
  encryption:
    key: !secret espyrain_api_key
ota:
  - platform: esphome
    encryption:

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  ap:
    ssid: ESPyRain temp Fallback Hotspot
    password: "kjshdfkhKALlkUdk"

  manual_ip:
    gateway: 192.168.1.1
    static_ip: 192.168.1.89
    subnet: 255.255.255.0
    dns2: 1.1.1.1
    dns1: 192.168.1.50
  fast_connect: true
  power_save_mode: NONE
captive_portal:

packages:
  espyrain_controller: !include ESPyRain-controller/package.yaml
  integrations_auto_run_block: !include ESPyRain-controller/integrations_auto_run_block.yaml

substitutions:
  user_timezone: America/Denver
  
```


## 4. Compile and flash the ESPHome device
Once you happy with the setup in your config:
- click INSTALL
- Plug in your device using USB and follow the on screen instructions
- This should upload the firmware to your ESP and it will take a few minutes
- After the first USB upload subsequent uploads can be done over-the-air (OTA)

1. Reboot the ESP by unplugging and reconnecting the USB cable.
2. Go back to the ESPHome Device Builder dashboard.
3. `ESPyRain Controller` should now show **Online**.

Next you need to add ESPyRain as a newly discovered device in Home Assistant:
- Go to **Settings -> Devices & Services**
- You should see `ESPyRain Controller (espyrain-controller)` under discovered devices
- Click **Add** to create all the ESPyRain entities

Confirm that entities appear in **Settings -> Tools -> States**, for example:
- `sensor.espyrain_controller_status`
- `button.espyrain_controller_pause`
- `number.espyrain_controller_number_of_stations`
- to see all related entities, type `espyrain` into **Filter entities**

Please note:
- `button.*` entities may show `unknown` in HA. This is normal for stateless action buttons.

From then on you can select **wireless** to update and reflash the device remotely instead of connecting it by USB.


## 5. Restart Home Assistant and validate the config
After copying the files as explained above:
1. Check the YAML / configuration in Home Assistant.
2. Restart Home Assistant.
3. Check HA log to confirm there are no package or secret errors.
4. Confirm that the new helpers exist, for example:
   - `input_text.espyrain_station_1_name`
   - `input_boolean.espyrain_notifications`
   - `input_text.espyrain_notify_service`


## 6. Choose a dashboard path
You have two dashboard options.


### Option A: Simple dashboard
Use this first if you want the easiest install path and not having to install HACS custom cards, but note that this is only a very basic dashboard. You can use this as a starting point to create your own custom dashboard.

File:
- [`HA_dashboards/ESPyRain_dashboard_simple.yaml`](HA_dashboards/ESPyRain_dashboard_simple.yaml)

This dashboard:
- uses only built-in Home Assistant cards
- does not require HACS
- does not require custom cards
- does not require image assets

To install:
- Add a new dashboard from scratch and paste the content from `ESPyRain_dashboard_simple.yaml` into the raw configuration editor, replacing everything in that new dashboard.

### Option B: Full dashboard
Use this if you want the richer ESPyRain dashboard with all feature.

File:
- [`HA_dashboards/ESPyRain_dashboard_full.yaml`](HA_dashboards/ESPyRain_dashboard_full.yaml)

This dashboard:
- uses custom cards
- uses image assets under `/config/www/espyrain/`
- is much more polished, but has more setup steps

Required HACS custom cards for the full dashboard:
- `button-card`
- `card-mod`
- `fold-entity-row`
- `large-number-input-card`
- `mini-graph-card`
- `template-entity-row`
- `time-picker-card`

**Install all of the above custom cards through HACS**

For more detail, see:
- [`HA_dashboards/README.md`](HA_dashboards/README.md)


## 7. Import or create the dashboard
Once Home Assistant and the ESP are both up:
1. Add the simple dashboard first, or the full dashboard if you already installed its dependencies.
2. Refresh the browser once after importing the dashboard.
3. If you are using the full dashboard, verify that the image paths load correctly.


## 8. First-time controller setup
Once the ESPyRain entities appear in Home Assistant:
1. Find the **System Configuration** card and set the number of stations(valves) you have.
2. Set the number of schedules you need.
3. Set rain delay to `0` for now.
4. Turn winter mode off unless you actually want it on.

Next configure the stations:
- enable the stations you currently use
- name each of the station as you wish- 
- set runtime in minutes
- assign the GPIO pin for each valve
- set coupled stations if needed
- enable the master valve / pump if used
- select the master valve / pump GPIO pin
- set the master valve delay in milliseconds


> **What does Delay(ms) start timer do?**
> 
> Allows to set a positive or negative value in milli seconds for the master valve to open before or after the start of the sprinkler valves
> 
> - use a negative value when you want the master valve to open after the station valves.
> - use a positive value when you want the master valve to open bevor the station valves. 



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


## 9. Optional soil moisture skip setup
This feature is optional and is ignored unless you enable it.

What it does:
- checks the average of the sensors in group.espyrain_soil_moisture_sensors
- ignores sensors that are currently unknown or unavailable
- skips automatic scheduled runs when the average is above the configured threshold
- does not block manual runs
- does not stop a schedule that is already running

Setup steps:
1. Create a Home Assistant group named group.espyrain_soil_moisture_sensors.
2. Add one or more soil moisture sensors to that group.
3. Reload Home Assistant groups or restart Home Assistant if needed.
4. Open the ESPyRain dashboard Config tab.
5. Turn on Soil Moisture Skip.
6. Set Soil Moisture Skip Threshold to the moisture percentage above which automatic runs should be skipped.
7. Verify these public entities exist and update correctly:
   - sensor.espyrain_soil_moisture_value
   - binary_sensor.espyrain_soil_moisture_block_active
   - binary_sensor.espyrain_auto_run_block_active
   - sensor.espyrain_auto_run_block_reason

Example group YAML:

~~~yaml
group:
  espyrain_soil_moisture_sensors:
    name: ESPyRain Soil Moisture Sensors
    entities:
      - sensor.hobeian_zg_303z_humidity
      - sensor.hobeian_zg_303z_humidity_2
      - sensor.hobeian_zg_303z_humidity_3
      - sensor.hobeian_zg_303z_humidity_4
~~~


## 10. Notification check
Turn ESPyRain notifications on if you want to be notified by the many events that trigger messages. Notifications are especially useful during setup because they show what is supposed to run and when.

Notifications can also report:
- automatic resume after a pause timeout
- queue limit reached when a new run cannot be added

1. Leave **Notification Service(s)** blank to get persistent notifications in HA.
2. If you want push notifications to your phone or another supported service, enter one or more comma-separated `notify.*` targets that already work in Home Assistant.

Examples:
- `notify.mobile_app_my_phone`
- `notify.telegram_bot_xxx_xxx, notify.mobile_app_my_phone`




