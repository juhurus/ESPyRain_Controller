# ESP Package Layout

This folder is the package-ready ESPHome bundle for:
- ESPHome Sprinkler Controller for Home Assistant

## Source of truth
Edit here:
- `ESP_sprinkler-controller/`

## Package entrypoint
- `ESP_sprinkler-controller/package.yaml`

## Example wrapper
- `ESP_sprinkler-controller/wrapper.example.yaml`

A local ESPHome device file can look like this:

```yaml
substitutions:
  device_name: sprinkler-controller
  friendly_name: "Sprinkler Controller"
  version: "2.0.2"

packages:
  sprinkler_controller: !include ESP_sprinkler-controller/package.yaml
```

## Optional local override
For static IP networking, add:

```yaml
wifi:
  manual_ip: !include ESP_sprinkler-controller/network_static_ip.local.yaml
```

Use `network_static_ip.example.yaml` as the template for that local file.
