# Home Assistant Blueprints

Personal collection of Home Assistant automation blueprints. Built for my own setup, but feel free to use them if they fit yours.

> **Note:** UI labels and field descriptions inside the blueprints are written in Polish. This README and the blueprint identifiers are in English. If you'd like them in your own language, feel free to fork the repo and translate.

---

## Blueprints

### Fan Cooling – UV Index, Presence & Sleep

Controls a fan (or any switch) based on outdoor UV index, temperature, occupancy, and a sleep mode flag. Designed for climates where a fan provides enough comfort cooling during hot, sunny days.

The fan turns on when it's hot enough for the current UV level, someone is home, and sleep mode is off. It automatically shuts off after a configurable window past sunset, and stays off overnight.

#### How thresholds work

Three temperature limits are defined — one per UV intensity zone. Higher UV means the room heats up faster, so the threshold drops.

| UV level | Default UV range | Default temperature threshold |
|----------|-----------------|-------------------------------|
| Low      | UV ≤ 2          | 23 °C                         |
| Moderate | UV 3–5          | 20 °C                         |
| High     | UV > 5          | 19 °C                         |

All three thresholds and both zone boundaries are configurable in the UI, so you can tune the behaviour for your climate without touching any YAML.

#### Inputs

| Section | Input | Description |
|---------|-------|-------------|
| Entities | Weather entity | Outdoor weather source — must expose a `uv_index` attribute |
| Entities | Occupancy / Presence | `input_boolean` or `binary_sensor` that is `on` when home |
| Entities | Sleep mode | `input_boolean` or `binary_sensor` that is `on` during sleep |
| Entities | Fan switch | Switch entity controlling the fan's power |
| Thresholds | Threshold – low UV | Temperature to activate fan at low UV (default 23 °C) |
| Thresholds | Threshold – moderate UV | Temperature to activate fan at moderate UV (default 20 °C) |
| Thresholds | Threshold – high UV | Temperature to activate fan at high UV (default 19 °C) |
| Thresholds | Low/moderate UV boundary | UV index separating low from moderate (default 2) |
| Thresholds | Moderate/high UV boundary | UV index separating moderate from high (default 5) |
| Schedule | Active window after sunset | Hours past sunset the automation stays active (default 2 h) |

#### Requirements

- Home Assistant **2024.6+** (blueprint `section` inputs)
- A weather entity that provides `uv_index` as an attribute (e.g. Met.no, Open-Meteo, Buienradar). Verify with Developer Tools → Template: `{{ state_attr('weather.YOUR_ENTITY', 'uv_index') }}`

#### Import

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2FNatanE6%2FBluprints%2Fmain%2Ffan_cooling_uv_presence.yaml)

Or manually: **Settings → Automations → Blueprints → Import Blueprint** and paste:

```
https://raw.githubusercontent.com/NatanE6/Bluprints/main/fan_cooling_uv_presence.yaml
```

---

### Aqara H2 EU WS-K07D (Zigbee2MQTT)

A blueprint for the **Aqara H2 EU WS-K07D** wall switch, driven via Zigbee2MQTT MQTT messages.

The switch has 4 physical actions: top single press, bottom single press, bottom double press, and bottom hold. Each action is independently configurable with one of 6 modes — all set directly in the Home Assistant UI without touching any YAML.

#### Supported modes (per button)

| Mode | What it does |
|------|-------------|
| **Disabled** | No action |
| **Toggle** | Toggle selected lights on/off |
| **CCT Color** | Set color temperature (warm / neutral / cool / custom K) |
| **RGB Color** | Set RGB color |
| **Brightness Cycle** | Cycle through brightness levels: → 60% → 100% → 20% → … |
| **Custom action** | Run any arbitrary HA action (raw action selector) |

#### Was-on restore

CCT, RGB, and Brightness Cycle modes implement the **was-on restore pattern**: lights that were *off* before the button press are turned on briefly to apply the new color or brightness setting, then automatically turned off again after 1 second. Lights that were already on stay on with the new setting applied.

This means you can set the scene color before turning the lights on — the switch remembers which lights should stay off.

#### CCT presets

| Preset | Temperature |
|--------|------------|
| Warm | Dynamically reads `max_mireds` from your bulbs |
| Neutral | 4000 K |
| Cool | 5800 K |
| Custom | Slider input (2000–6500 K) |

#### Requirements

- Home Assistant **2024.6+** (blueprint `section` inputs)
- **Zigbee2MQTT** with the device paired and publishing to an MQTT topic

#### Import

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2FNatanE6%2FBluprints%2Fmain%2FAqara_H2_EU_WS-K07D.yaml)

Or manually: **Settings → Automations → Blueprints → Import Blueprint** and paste:

```
https://raw.githubusercontent.com/NatanE6/Bluprints/main/Aqara_H2_EU_WS-K07D.yaml
```

---

## License

Released under **The Unlicense** — do whatever you want with it.

For the full text, see [LICENSE](./LICENSE).
