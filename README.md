![[espyrain_controller_head.png]]
# ESPyRain Sprinkler Controller for Home Assistant

An ESP32-based sprinkler controller with Home Assistant integration, designed to keep scheduled irrigation running autonomously even if HA or Wi-Fi is temporarily unavailable..

ESPyRain is built for users who want a powerful, transparent, highly configurable irrigation system without depending on a cloud service or a closed commercial controller.

There is a wide selection of ESP based sprinkler systems out there. More common controllers like the ESPHome Sprinkler Controller and Irrigation Unlimited are both great and reliable sprinkler systems but they all require to recompile and reflash the ESP32 for any change to the system. 

The aim was to built a system which doesn't require reflashing or reloading yaml files to do a minor change. The user should have the flexibility to set and control everything through HA so that family member can change a schedule start time or a station runtime without having to edit and reload HA yaml files or recompile and reflash the ESP.

ESPyRain Controller allows users to be in full control of their watering requirements through HA without having to reload HA or reflash the ESP after the initial setup.

As previously mentioned another important factor was that the ESP32 controller continuous  scheduled watering even if HA or Wi-Fi is down temporarily. ESPyRain stores all schedules directly on the ESP device to allow fully autonomous watering.


![Sprinkler Controller](./screenshots/controller_head.png)

## What is ESPyRain Controller

ESPyRain provides a full-featured sprinkler controller built with:

- `ESPHome` on an `ESP32` for local irrigation execution
- `Home Assistant` for configuration, dashboard control, notifications, and higher-level logic

The key design goal is reliability:

- schedules run locally on the controller
- overlapping runs are queued instead of being skipped
- manual runs and scheduled runs can coexist
- the queue runs on first-in-first-out bases
- Home Assistant enhances the system, but is not required for scheduled watering once setup is complete

## Key Features

### Configuration

Easy configuration through HA dashboard. You can configure all aspects directly through HA. No YAML editing needed, no YAML reloading needed, no further ESP flashing needed just to change a GPIO pin or update a runtime. 

ESPyRain doesn't use Programs like regular controllers, instead it uses Schedules where the user can select stations and start times.

ESPyRain configuration options from HA without restarting HA or reflashing the ESP32:

- Set the amount of Stations/Zones (1 to 16) (hardware permitting)
- Set the amount of Schedules you need to run (1 to 8)
- Master Valve / Pump GPIO pin
- Station/Zone GPIO pins
- Station/Zone names
- Enable/Disable Stations/Zones
- Station runtimes
- Select Coupled Stations to work together
- Name your Schedules
- Enable/Disable Schedules
- Set Schedule Start time
- Seasonal Adjust% per schedule
- Schedule Day Mode, Odd/Even, Every 2 to 7 Days, Selected Days, Weekends, Weekdays
- Soak Cycle
- Soak Delay between Cycles
- Rain Delay
- Winter Mode
- Auto Winter Mode
- Notifications On/Off
- Bypass Interval Check

Note: Using day mode "Every 2x Days" has the advantage that your sprinklers will not water two days in a row or miss watering days at the end of a month like it always happens with "Odd/Even". 

![[every2days.png]]
### Core Irrigation Control

- Bore Pump / Master Valve
- currently up to `16` stations
- currently up to `8` schedules
- Seasonal adjust runtimes 50 to 150%
- Manual station runs
- Manual runtime selection
- Queue-based execution
- Overlapping runs are added to the queue without interrupting the current run
- Support for scheduled runs and manual runs in the same system

![Controller Config](./screenshots/system_config.png)
### Advanced Irrigation Logic

- Pause/Resume any time
- Soak cycles 2x to 5x
- Configurable soak rest time
- Seasonal adjust per schedule
- Daily, selected-day, and interval-based schedules
- Coupled stations / linked stations
- Rain delay
- Winter mode
- Optional automatic winter mode using start/end dates

![Controller Config card](./screenshots/controller_config_rain_delay_and_winter_mode.png)

### Reliability and Autonomy

- Scheduled watering continues on the ESP32 even if Home Assistant is unavailable
- Scheduled watering continues if Wi-Fi is unavailable
- State restore across controller restarts
- Heartbeat/disconnect awareness between HA and the controller
- Controller-side queue execution
- No cloud dependency

![[controller_offline.png]]
### Home Assistant Integration

- Home Assistant package-based installation
- Lovelace Dashboard included
- HA Companion app control through Home Assistant
- Telegram / notification support
- Forecast and next-run sensors
- Debug sensors and troubleshooting helpers
- Manual run image-map/dashboard support
- Runtime stats through custom cards. 

### Hardware Integration

- Pump / master valve switching
- Pump / valve delay handling
- Solenoid valve switching
- Suitable for systems that need staged startup or safe changeover timing

## Why ESPyRain Project was designed 

Many irrigation solutions are either:

- easy to use but closed and limited
- powerful but dependent on cloud services
- integrated into Home Assistant but difficult to use
- integrated into HA but not designed for independent operation

This project aims to combine the best parts of both worlds:

- local, reliable execution on the controller
- rich control and visibility through Home Assistant
- easy to use, operate and maintain through HA 
- transparent logic that can be understood, modified, and extended

## How It Works

The architecture is split between two layers:

### ESP32 / ESPHome Layer

The ESP32 is the actual sprinkler controller. It is responsible for:

- storing schedule execution state
- running stations
- managing the queue
- handling soak cycles
- preserving autonomous scheduled operation

This is what allows the system to keep watering on schedule even if Home Assistant or Wi-Fi is offline.

![Sprinkler Controller](./screenshots/upcoming_watering.png)

### Home Assistant Layer

Home Assistant provides:

- configuration helpers
- dashboards
- manual controls
- notifications
- forecast and summary sensors
- optional higher-level automation

Home Assistant is the management and visibility layer, but not the only thing keeping irrigation alive.

## Practical Features That Matter in Daily Use

### Real Pause Function

This system includes a real pause function for active watering.

Examples where this is useful:

- sprinkler maintenance, pause the system while you replace or change a sprinkler head
- your kids are playing on the lawn
- you have visitors over for a BBQ in your backyard
- the lawn maintenance person arrives
- a car is parked on the lawn
- you are in the middle of gardening
- you need to quickly stop watering without losing the run state

The goal is to pause cleanly and resume properly, instead of cancelling a run. This could potentially save you water if you don't need to re-run a schedule from the start. 

### Queueing Instead of Cancelling or Skipping

If one schedule is already active and another schedule becomes due, the new run is added to the end of the queue. This could happen when you have two schedules programmed to run one after another but due to seasonal adjust they may now overlap. ESPyRain handles this issue by queueing the next run on a first-in-first-out basis. 

This is an important reliability feature. The system is designed so irrigation runs are not silently cancelled or skipped just because something else is already watering.

### Coupled Stations

If multiple valves need to operate together, they can be linked.

Selecting or starting any station in a linked group can resolve to the whole group, so coupled irrigation zones behave consistently in both manual and scheduled operation.

### Seasonal Adjust

Each schedule supports individual seasonal adjustment between 50% to 150%.

This is useful on its own, but it also makes weather-based runtime adjustment easy to add for users who have reliable local weather or rainfall data in Home Assistant.

Rather than building one fixed weather model into the controller, this project lets advanced users drive `Seasonal Adjust' through HA automations if they want that behavior.

## Pump / Master Valve Support

This project supports pump or master valve switching, including delay handling.

This is useful for installations where:

- a pump must start before station valves open
- a pump or master valve should remain active briefly during station changeover
- valve/pump timing needs to be coordinated safely

Note: pump / master valve behavior should always be validated for each hardware installation before relying on it in production.

![[master_valve_bore_pump_card.png]]
## Control Options

You can control the system through:

- the Home Assistant dashboard
- the Home Assistant companion app
- Built-in webserver (for when HA is down)
- Home Assistant automations
- ESPHome button/services exposed to Home Assistant

Manual watering can be started either from standard controls or from a mapped image-based station selection card.

![Sprinkler Controller](./screenshots/mobile_manual_station_s8_active_image.jpg)

## Who This Project Is For

This project is a good fit if you:

- want a local-first irrigation controller
- already use Home Assistant and ESPHome
- are comfortable editing YAML
- want more flexibility than a fixed commercial controller usually provides
- care about autonomy, visibility, and reliability


## Requirements

### Software

- Home Assistant
- ESPHome
- Required Lovelace custom cards for the included dashboard

### Hardware

- ESP32 board
- Relay board or suitable sprinkler output hardware
- Optional pump or master valve output
- Optional Telegram notifier for notifications

### Expected User Skill Level

This project is best suited for users who are comfortable with:

- editing YAML
- Home Assistant configuration
- ESPHome configuration
- basic irrigation wiring concepts

It is packaged to be easier to install, but it is still designed for advanced Home Assistant users rather than one-click beginners.

## Quick Start

Detailed setup steps are documented in:

- `INSTALL_CHECKLIST.md`

At a high level, installation looks like this:

1. Install the Home Assistant package
2. Add required secrets
3. Install required Lovelace custom cards
4. Prepare the ESPHome wrapper config
5. Configure Wi-Fi, API, OTA, and hardware-specific settings
6. Flash the ESP32
7. Import or add the dashboard
8. Configure stations, schedules, and optional features
9. Run first manual and scheduled tests

## First-Time Setup Checklist

After the package is installed, the first things most users will want to configure are:

- station names
- station enabled states
- GPIO / valve mapping
- schedule names
- schedule times and day modes
- soak cycle options
- soak rest time
- seasonal adjust values
- coupled stations if used
- notification settings
- winter mode dates if used
- dashboard image paths if using the manual run map

## Repository Layout

- `ESPyRain-controller/`
  - ESPHome package, wrapper examples, and controller firmware configuration
  
- `HA_dashboard/`
  - dashboard YAML and dashboard dependency notes

- `packages/`
  - Home Assistant package entrypoint

- `package_sources/`
  - Home Assistant package source files:
  - helpers
  - automations
  - scripts
  - sensors
  - templates

- `INSTALL_CHECKLIST.md`
  - detailed installation and setup checklist

- `VERSION`
  - canonical project version

## Dashboard and Companion App

The included dashboard is designed to expose the controller in a practical, operator-friendly way.

It includes:

- schedule configuration
- watering progress
- runtime stats
- manual run controls
- controller status and debug information
- optional image-based station start buttons

Because the system is exposed through Home Assistant, it can also be controlled through the Home Assistant companion app.

## Current Scope

This project already includes:

- autonomous scheduled irrigation execution on the ESP
- Home Assistant integration
- soak cycles
- queue-based overlap handling
- seasonal adjust
- rain delay
- winter mode
- notifications
- dashboard control
- manual image-map station control

## Notes on Weather-Based Watering

Weather-based runtime adjustment is not forced into the project as a built-in one-size-fits-all feature.

This is intentional.

Weather data quality varies a lot by location, and some users have much more reliable local weather information than others. For users with good local data in Home Assistant, runtime adjustment can be added very easily by automating schedule `Seasonal Adjust`.

This gives flexibility without forcing unreliable forecast assumptions on every installation.

## Roadmap / Future Ideas

Potential future improvements include:

- optional weather/rain-based skip logic
- further public package polish
- installation simplification
- additional diagnostics and onboarding improvements
- optional flow/leak monitoring if supporting hardware is added

## Status

This project is actively being refined and packaged for cleaner public installation.

The goal is a reliable, transparent, local-first irrigation controller that can be adapted to different properties and watering needs.

## License

To be added.
