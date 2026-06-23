# Home Assistant Blueprints

Personal collection of Home Assistant automation blueprints. Built for my own setup, but feel free to use them if they fit yours.

> **Note:** UI labels and field descriptions inside the blueprints are written in Polish. This README and the blueprint identifiers are in English. If you'd like them in your own language, feel free to fork the repo and translate.

---

## Blueprints

### Fan Cooling – Presence & Sleep

Controls a fan (or any switch) based on indoor temperature, occupancy, and a sleep mode flag.

The fan turns on when indoor temperature reaches the configured threshold, someone is home, and sleep mode is off. It automatically shuts off after a configurable window past sunset, and stays off overnight.

#### Manual override

When the fan is turned on manually (via UI or physical switch) while the automation would keep it off, the blueprint detects this automatically:

- **First manual press** — fan stays on for a configurable timeout (default 10 min), then turns off.
- **Second manual press** (within the reset window) — fan stays on indefinitely.
- **Sleep mode** always cancels the manual override, regardless of how many times the fan was pressed.
- After the reset window (default 1 h), the counter resets and the automation resumes normal UV/temp control.

Two helper entities are required — create them once in **Settings → Devices & Services → Helpers**:

| Helper | Type | Settings |
|--------|------|----------|
| `input_boolean.fan_manual_override` | Toggle | Name: "Fan Manual Override" |
| `input_number.fan_manual_press_count` | Number | Name: "Fan Manual Press Count", min 0, max 10, step 1 |

#### Inputs

| Section | Input | Description |
|---------|-------|-------------|
| Entities | Temperature sensor | Indoor temperature sensor (°C, device_class: temperature) |
| Entities | Occupancy / Presence | `input_boolean` or `binary_sensor` that is `on` when home |
| Entities | Sleep mode | `input_boolean` or `binary_sensor` that is `on` during sleep |
| Entities | Fan switch | Switch entity controlling the fan's power |
| Thresholds | Temperature threshold | Temperature to activate the fan (default 23 °C) |
| Schedule | Active window after sunset | Hours past sunset the automation stays active (default 2 h) |
| Manual override | Manual override flag | `input_boolean.fan_manual_override` helper |
| Manual override | Manual press counter | `input_number.fan_manual_press_count` helper |
| Manual override | First press timeout | Minutes before fan turns off after first manual press (default 10) |
| Manual override | Counter reset window | Hours before the press counter resets (default 1 h) |

#### Requirements

- Home Assistant **2024.6+** (blueprint `section` inputs)
- Two helper entities for manual override tracking (see Manual override section above)

#### Import

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2FNatanE6%2FBluprints%2Fmain%2Ffan_cooling_presence.yaml)

Or manually: **Settings → Automations → Blueprints → Import Blueprint** and paste:

```
https://raw.githubusercontent.com/NatanE6/Bluprints/main/fan_cooling_presence.yaml
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
