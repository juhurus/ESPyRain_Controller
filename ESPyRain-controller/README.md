# ESPyRain ESPHome Package Layout

This folder contains the ESPHome package files for:
- ESPyRain Controller for Home Assistant

## Source of truth
Edit the ESPHome files here:
- `ESPyRain-controller/`

## Main ESPHome device file
The main ESPHome device YAML is:
- `ESPyRain-controller/espyrain_controller_wrapper.yaml`

This is the file you typically copy into:
- `/config/esphome/espyrain_controller_wrapper.yaml`

## Package entry point
The ESPHome package entry point is:
- `ESPyRain-controller/package.yaml`

That package pulls in the rest of the ESPyRain controller YAML files in this folder.

## Important files
- `base.yaml`
  - shared ESPHome base configuration
- `entities_system.yaml`
  - controller system entities
- `entities_schedules.yaml`
  - schedule entities
- `entities_stations.yaml`
  - station entities
- `globals_system.yaml`
  - system globals
- `globals_schedules.yaml`
  - schedule globals
- `globals_stations.yaml`
  - station globals
- `schedule_interval.yaml`
  - schedule trigger logic
- `sensors.yaml`
  - ESP-side sensors and diagnostics
- `VERSION`
  - current ESPyRain version used by the config

## Site-specific setup
Before compiling, review and adjust the settings in:
- `ESPyRain-controller/espyrain_controller_wrapper.yaml`

At minimum, check:
- board type
- framework type
- timezone
- Wi-Fi / API / OTA secrets
- optional static IP settings if needed

## Important note
The `VERSION` file must remain in this folder because the ESPHome configuration reads the version from it.
