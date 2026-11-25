# Frigate Light Blueprint

Home Assistant blueprint to control camera lights using Frigate detections, zone filters, and the sun's elevation.

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fcoruscant187%2Ffrigate-light-blueprint%2Fblob%2Fmain%2FFrigate_Camera_Light%2FStable.yaml)

## Features

- `detection` events by default, with optional `alert` events.
- Zone and object filters.
- Adjustable sun elevation threshold, brightness, and cooldown.
- Optional motion sensors.

## Requirements

- Home Assistant 2024.11 or later.
- Frigate connected to Home Assistant through MQTT.
- A dimmable camera light exposed as a Home Assistant light entity (such as through the [Dahua integration](https://github.com/rroller/dahua)).

## Setup

1. Import [Stable.yaml](Frigate_Camera_Light/Stable.yaml) using the button above, or paste its GitHub URL into **Settings → Automations & scenes → Blueprints → Import blueprint**.
2. Create an automation and select the Frigate camera and the light to control.
3. Enter at least one exact Frigate zone ID. Use zone IDs unique to that camera.
4. Select `sun.sun` or a numeric sun elevation sensor. Adjust the threshold and timings if needed.
5. Optionally select motion sensors and enable debug logging.

The camera name is read from its `camera_name` attribute. The default review topic is `frigate/reviews`; person counts use `frigate/<zone_id>/person`.

Disable competing device lighting rules. This automation exclusively controls the selected light. Its internal MQTT state is restored after reloads when available. If that state is unavailable while the light is on, it requests OFF instead of assuming a new lighting session. It does not change camera image settings or illumination modes.

## Behavior

A matching review starts a session. Person counts hold the light on while any selected zone is occupied. Once all zones report zero and optional motion sensors are inactive, the cooldown starts. A renewed occupancy request cancels pending switch-off. Motion sensors can also request light independently. All requests require the configured darkness condition.

After the maximum on-time, the light switches off and remains locked out until the occupancy request clears. New review updates cannot bypass that lockout. A stuck count can therefore block the next activation instead of leaving the light on.

## Credits

Camera mapping follows the approach used by [SgtBatten's Frigate Notifications](https://github.com/SgtBatten/HA_blueprints). Review messages follow the [Frigate MQTT documentation](https://docs.frigate.video/integrations/mqtt/).

## License

[MIT](LICENSE)
