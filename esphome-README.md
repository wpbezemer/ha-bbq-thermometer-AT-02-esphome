# ESPHome — AT-02 BBQ Thermometer

ESPHome configuration for the **AT-02 BBQ Grill Thermometer** with Home Assistant integration.

Supports 4 probes + 1 ambient sensor. Each probe has a status text sensor that shows `"Disconnected"` when the probe is unplugged or the thermometer is out of BLE range — designed to work with [ha-bbq-card](https://github.com/wpbezemer/ha-bbq-card).

---

## Credits

This configuration is based on the work of several community members who reverse-engineered the AT-02 BLE protocol:

| Contributor | Contribution |
|---|---|
| [syepes (Sebastian YEPES)](https://community.home-assistant.io/u/syepes) | Initial reverse engineering and BLE investigation |
| [DigiH](https://community.home-assistant.io/u/DigiH) | Temperature decoding algorithm (big-endian int16 / 10) |
| [LangeJan (Jan)](https://community.home-assistant.io/u/LangeJan) | Full packet structure and checksum analysis |
| [yurassic](https://community.home-assistant.io/u/yurassic) | BLE authentication sequence (CEE1 write codes) |
| [Adma2761](https://community.home-assistant.io/u/Adma2761) | First working ESPHome YAML implementation |

Original thread: [BBQ Grill Thermometer - AT-02 — Home Assistant Community](https://community.home-assistant.io/t/bbq-grill-thermometer-at-02/584078)

**Modifications by [wpbezemer](https://github.com/wpbezemer) (with help from Claude / Anthropic):**
- Added `binary_sensor` for overall BLE connection state
- Added `text_sensor` per probe showing `"Disconnected"` when probe is unplugged (`0x7FFF` in packet) or BLE is disconnected
- On BLE disconnect all sensors immediately publish `NAN` and `"Disconnected"`
- Designed to work with [ha-bbq-card](https://github.com/wpbezemer/ha-bbq-card) which shows "Offline" when the text sensor contains a non-numeric value

---

## Hardware

- **AT-02 BBQ Grill Thermometer** (available on AliExpress)
- Any **ESP32** board with Bluetooth (e.g. ESP32 DevKit)

---

## Installation

### 1. Find your AT-02 MAC address

Use a BLE Scanner app on your phone and look for `AT-02`. Or on Linux:

```bash
sudo hcitool lescan
```

### 2. Configure secrets

Copy `secrets.yaml.template` to `secrets.yaml` and fill in your values:

```yaml
wifi_ssid: "Your WiFi SSID"
wifi_password: "Your WiFi Password"
ap_password: "Your Fallback AP Password"
encryption: "your-esphome-api-key"
ota_bbq_password: "your-ota-password"
ble_bbq_mac: "E6:2D:7C:52:22:B3"   # replace with your MAC
```

> ⚠️ Never commit `secrets.yaml` to GitHub — it is in `.gitignore`

### 3. Flash to ESP32

```bash
esphome run bbq-thermometer-at02.yaml
```

---

## Sensors in Home Assistant

| Entity | Type | Description |
|---|---|---|
| `sensor.probe_1_temperature` | Number | Probe 1 in °C (`NAN` when disconnected) |
| `sensor.probe_2_temperature` | Number | Probe 2 in °C |
| `sensor.probe_3_temperature` | Number | Probe 3 in °C |
| `sensor.probe_4_temperature` | Number | Probe 4 in °C |
| `sensor.ambient_temperature` | Number | Ambient/dome in °C |
| `text_sensor.probe_1_status` | Text | `"Disconnected"` or temperature value |
| `text_sensor.probe_2_status` | Text | `"Disconnected"` or temperature value |
| `text_sensor.probe_3_status` | Text | `"Disconnected"` or temperature value |
| `text_sensor.probe_4_status` | Text | `"Disconnected"` or temperature value |
| `text_sensor.ambient_status` | Text | `"Disconnected"` or temperature value |
| `binary_sensor.bbq_thermometer_connected` | Boolean | Overall BLE connection state |

---

## Using with ha-bbq-card

Use the **status text sensors** as `temp_entity` in the BBQ Card. The card shows "Offline" when the value is not a number:

```yaml
type: custom:bbq-card
name: Probe 1
size: small
show_preset: true
temp_entity: text_sensor.probe_1_status
min_entity: input_number.probe1_min_temp
max_entity: input_number.probe1_max_temp
onoff_entity: input_boolean.probe1_monitoring
snooze_entity: input_boolean.probe1_snooze
abs_min: 0
abs_max: 100
step: 1
```

---

## License

MIT — see [LICENSE](LICENSE) for details.
