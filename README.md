# ESPHome — AT-02 BBQ Thermometer # ha-bbq-thermometer-AT-02-esphome

ESPHome configuration for the **AT-02 BBQ Grill Thermometer** with probe 
disconnect detection for Home Assistant.

Based on the excellent reverse engineering work by the Home Assistant community —
special thanks to [syepes](https://community.home-assistant.io/u/syepes),
[DigiH](https://community.home-assistant.io/u/DigiH),
[LangeJan](https://community.home-assistant.io/u/LangeJan),
[yurassic](https://community.home-assistant.io/u/yurassic) and
[Adma2761](https://community.home-assistant.io/u/Adma2761) for figuring out
the BLE protocol.
See the original thread:
[BBQ Grill Thermometer - AT-02](https://community.home-assistant.io/t/bbq-grill-thermometer-at-02/584078).

Modified by [wpbezemer](https://github.com/wpbezemer)
to add per-probe disconnect detection, designed to work with
[ha-bbq-card](https://github.com/wpbezemer/ha-bbq-card).
