# 🔥 ESPHome — AT-02 BBQ Thermometer

ESPHome configuration for the **AT-02 Bluetooth BBQ Grill Thermometer** with full probe disconnect detection and Home Assistant integration.

---

## Features

- Reads **4 meat probes** + **1 ambient temperature** over BLE
- Shows **"Disconnected"** when a probe is unplugged or the thermometer is off — no stale values
- Binary sensor for overall **BLE connection state**
- Designed to work with **[ha-bbq-card](https://github.com/wpbezemer/ha-bbq-card)**

---

## Requirements

- ESP32 board (e.g. D1 Mini ESP32) with Bluetooth
- [ESPHome](https://esphome.io) (tested with esp-idf framework)
- AT-02 BBQ Bluetooth Thermometer
- Home Assistant

---

## Installation

### 1. Find your thermometer's MAC address

Use a BLE Scanner app on your phone and look for a device named **AT-02**, or on Linux:

```bash
sudo hcitool lescan
```

### 2. Configure secrets

Copy `secrets.yaml.template` to `secrets.yaml` and fill in your values:

```yaml
wifi_ssid: "YourWiFiNetwork"
wifi_password: "YourWiFiPassword"
ap_password: "YourFallbackPassword"
encryption: "YourESPHomeAPIEncryptionKey"
ota_bbq_password: "YourOTAPassword"
ble_bbq_mac: "FE:2A:65:48:2F:04"   # ← replace with your AT-02 MAC
```

### 3. Flash the device

```bash
esphome run bbq-thermometer-at02.yaml
```

---

## Home Assistant Entities

After adding the device to Home Assistant, the following entities are available:

| Entity | Type | Description |
|---|---|---|
| `binary_sensor.bbq_thermometer_connected` | Binary sensor | BLE connection state |
| `sensor.probe_1_temperature` | Sensor (°C) | Probe 1 numeric value |
| `sensor.probe_2_temperature` | Sensor (°C) | Probe 2 numeric value |
| `sensor.probe_3_temperature` | Sensor (°C) | Probe 3 numeric value |
| `sensor.probe_4_temperature` | Sensor (°C) | Probe 4 numeric value |
| `sensor.ambient_temperature` | Sensor (°C) | Ambient/lid temperature |
| `sensor.probe_1_status` | Text sensor | "Disconnected" or temperature |
| `sensor.probe_2_status` | Text sensor | "Disconnected" or temperature |
| `sensor.probe_3_status` | Text sensor | "Disconnected" or temperature |
| `sensor.probe_4_status` | Text sensor | "Disconnected" or temperature |
| `sensor.ambient_status` | Text sensor | "Disconnected" or temperature |

> **Tip:** Use the `*_status` text sensors on your dashboard — they show "Disconnected" when a probe is unplugged instead of an outdated value. Use the numeric `*_temperature` sensors for graphs and automations.

---

## Disconnect Detection

The configuration handles three disconnect scenarios:

| Situation | Behaviour |
|---|---|
| Probe unplugged (0x7FFF in packet) | Shows "Disconnected" |
| Probe returns -0.1 °C or lower | Shows "Disconnected" |
| Thermometer turned off / BLE lost | All sensors show "Disconnected" |

---

## BLE Packet Structure

Credits to the Home Assistant community for reverse engineering the protocol:

```
Byte [0-1]  = 0x55 0xAA  (header)
Byte [4]    = 0xA1        (temperature message type)
Byte [5-6]  = Probe 1     (big-endian int16, divide by 10 = °C)
Byte [7-8]  = Probe 2
Byte [9-10] = Probe 3
Byte [11-12]= Probe 4
Byte [17-18]= Ambient
0x7FFF      = probe not connected
```

---

## Credits

Protocol research and decoding by the Home Assistant community:

- **[syepes](https://community.home-assistant.io/u/syepes)** — initial BLE reverse engineering
- **[DigiH](https://community.home-assistant.io/u/DigiH)** — temperature decoding algorithm
- **[LangeJan](https://community.home-assistant.io/u/LangeJan)** — packet structure & checksum analysis
- **[yurassic](https://community.home-assistant.io/u/yurassic)** — BLE authentication sequence
- **[Adma2761](https://community.home-assistant.io/u/Adma2761)** — first working ESPHome YAML

Original thread: [BBQ Grill Thermometer - AT-02](https://community.home-assistant.io/t/bbq-grill-thermometer-at-02/584078)

---

## License

MIT — see [LICENSE](LICENSE)
