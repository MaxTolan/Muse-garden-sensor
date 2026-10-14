# Muse Garden Sensor

Two right-sized solar sensor builds for a Houston garden. No industrial hardware,
no four-figure power plant: affordable sensors, one protected 18650 per node, and
aggressive deep sleep instead of a large panel and battery bank.

Status: planning only. Nothing has been purchased. Prices are estimates observed
on October 14, 2026, before tax and shipping.

## Build 1: ToughC5 multi-zone station (~$272, under the $300 cap)

The M5Stack ToughC5 (ESP32-C5) stays as the field gateway and local display, but
the sensor stack moves to low-cost analog, I2C, and 1-Wire parts:

| Part | Qty | Est. |
|---|---|---|
| M5Stack ToughC5 (gateway, display, enclosure) | 1 | $49.90 |
| DFRobot SEN0193 capacitive soil moisture | 3 | $17.70 |
| Adafruit ADS1115 16-bit I2C ADC | 1 | $14.95 |
| Adafruit SHT40 air temp + humidity (in radiation shield) | 1 | $5.95 |
| Adafruit VEML7700 ambient light | 1 | $4.95 |
| Adafruit DS18B20 waterproof soil temperature | 1 | $9.95 |
| Tipping-bucket rain gauge (0.2794 mm/tip) | 1 | $14.95 |
| La Crosse 925-1418 radiation/rain shield | 1 | $20.99 |
| Adafruit bq24074 solar charger with load sharing | 1 | $14.95 |
| 6 V 2 W ETFE solar panel | 1 | $26.19 |
| Protected 18650, 3.7 V 2200 mAh | 1 | $9.95 |
| Outdoor IP65 enclosure | 1 | $12.00 |
| PG7 cable glands, 5-pack | 1 | $10.00 |
| HY2.0-4P cable pack | 1 | $7.00 |
| RJ11 breakout board | 1 | $10.00 |
| Silicone hookup wire | 1 | $10.00 |
| Standoff kit | 1 | $8.00 |
| Mounting hardware | 1 | $12.00 |
| TVS/ESD protection parts | 1 | $5.00 |
| UV protection | 1 | $8.00 |
| **Total** | | **~$272** |

Wiring: three moisture probes feed ADS1115 channels A0-A2 (power the probes only
while sampling); SHT40 and VEML7700 share the 3.3 V I2C bus; DS18B20 on one GPIO
with its 4.7k pull-up; rain gauge on a GPIO with pull-up and debounce. Wake every
15 minutes, sample everything in under 60 seconds, transmit one compact record,
and sleep with the display off. A continuously-on ToughC5 draws far more than the
small panel can supply, so sleep is non-negotiable.

## Build 2: 27-gallon sub-irrigated planter node (~$111)

A sub-irrigated planter answers one practical question first: does the reservoir
need water? So the key sensor is a float switch at the refill level, with soil
moisture as backup (capillary beds can read moist above an emptying reservoir).

| Part | Qty | Est. |
|---|---|---|
| Seeed XIAO ESP32-C3 (~44 uA deep sleep) | 1 | $4.90 |
| Reed float switch, reservoir level | 1 | ~$15.00 * |
| DFRobot SEN0193 capacitive soil moisture | 1 | $5.90 |
| Adafruit bq24074 solar charger | 1 | $14.95 |
| 6 V 2 W ETFE solar panel | 1 | $26.19 |
| Protected 18650, 3.7 V 2200 mAh | 1 | $9.95 |
| Small outdoor enclosure | 1 | $10.00 |
| Cable glands | 1 | $4.00 |
| Load-switch MOSFET and resistors | 1 | $4.00 |
| Wire and connectors | 1 | $8.00 |
| Planter mounting | 1 | $8.00 |
| **Total** | | **~$111** |
| Optional DS18B20 soil temperature | 1 | $9.95 |

\* The float switch is the least certain line: no dependable single-unit US
retail price was verified. With the optional temperature probe, about $121.

Wake every 30 minutes, read float and soil, and transmit only on a meaningful
state change or scheduled heartbeat. Alert once when the reservoir drops to the
refill level, suppress repeats until it returns to full, and require two
consecutive readings before alerting.

## Power notes (both builds)

- Panel -> bq24074 charger -> protected 18650 -> load. Never feed the charger's
  <=4.4 V load output into a USB-C port; verify every battery polarity before
  connecting.
- Houston planning illustration: 2.37 W panel x 4 peak-sun-hours x 65% derate is
  roughly 6.2 Wh/day against an 8.1 Wh cell. Real sleep current, Wi-Fi
  association, and transmissions add load, so measure the finished node and
  lengthen the wake interval before upsizing anything.
- Commissioning: bench-test the power chain, calibrate each moisture probe in
  air, dry media, and wet media, soak-test outdoors 48-72 hours through midday
  heat and overnight dew, and confirm the float reports both states reliably.

## Docs

A detailed build document with full wiring tables, the power math,
commissioning gates, and decision notes is saved with this project as the
reference file.
