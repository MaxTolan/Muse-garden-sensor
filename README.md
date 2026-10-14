# Muse Garden Sensor

A ToughC5 garden sensor system designed to grow: start with the sub-$300 build
now, add soil chemistry months later without rewiring. Plus a cheaper standalone
node for one 27-gallon sub-irrigated planter.

Status: planning only. Nothing has been purchased. Prices are estimates observed
on October 14, 2026, before tax and shipping.

## Phase 1: expandable ToughC5 station (~$285)

The M5Stack ToughC5 (ESP32-C5) is the field gateway and local display, with
low-cost analog, I2C, and 1-Wire sensors — built with expansion in mind from day
one:

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
| 6 V 5 W solar panel (headroom for Phase 2) | 1 | $26.00 |
| Protected 18650, 3.7 V 2200 mAh | 1 | $9.95 |
| Outdoor IP65 enclosure | 1 | $12.00 |
| PG7 cable glands, 5-pack + 1 spare | 1 | $12.00 |
| HY2.0-4P cable pack | 1 | $7.00 |
| RJ11 breakout board | 1 | $10.00 |
| Labeled RS485 terminal block (A/B/GND, unpopulated) | 1 | $3.00 |
| Silicone hookup wire | 1 | $10.00 |
| Standoff kit | 1 | $8.00 |
| Mounting hardware | 1 | $12.00 |
| TVS/ESD protection parts | 1 | $5.00 |
| UV protection | 1 | $8.00 |
| **Phase 1 total** | | **~$285** |

Expansion provisions built in now: the ToughC5's native RS485 port is pre-wired
to a labeled terminal block, one spare gland is installed (plugged), the
enclosure has room left for a boost module, the 5 W panel (nearly the same price
as a 2 W one) means no panel swap later, and the ADS1115's fourth channel (A3)
sits free.

Wiring: three moisture probes feed ADS1115 channels A0-A2 (power the probes only
while sampling); SHT40 and VEML7700 share the 3.3 V I2C bus; DS18B20 on one GPIO
with its 4.7k pull-up; rain gauge on a GPIO with pull-up and debounce. Wake every
15 minutes, sample everything in under 60 seconds, transmit one compact record,
and sleep with the display off.

## Phase 2: soil chemistry upgrade (~$222, months later)

| Part | Est. |
|---|---|
| 7-in-1 NPK/pH/EC RS485 soil probe | $211.00 |
| 12 V boost module | $8.00 |
| MOSFET load switch | $3.00 |
| **Phase 2 total** | **~$222** |

The probe wires straight to the waiting terminal block — no transceiver needed,
it speaks Modbus RTU to the ToughC5's RS485 port. The probe wants 12-24 V but
the system is 3.7 V, so the boost module steps up only while sampling: wake,
enable 12 V, read, disable, sleep. At ~60 seconds per reading the draw is a
rounding error on the battery.

**All-in across both phases: about $509.**

## Other upgrades

| Upgrade | Est. | Notes |
|---|---|---|
| 4th soil-moisture zone | ~$6 | One more SEN0193 on the free A3 channel |
| Second 18650 in parallel | ~$12 | Cell + holder, for more cloudy-day buffer |

## Trade-offs vs the $1,397 deluxe design

What the cheaper build gives up:

- **Soil chemistry entirely** in Phase 1 (returns as the Phase 2 upgrade).
- **Probe quality:** four industrial IP68 Modbus moisture probes with 5 m cables
  became three cheap capacitive probes on short leads — less accurate, shorter
  lived, must sit near the hub.
- **One moisture zone:** four zones down to three.
- **Power:** a 200 W panel plus 100 Ah battery with 3.6 days of autonomy became a
  5 W panel and a single 18650. Survival depends on aggressive deep sleep; a long
  cloudy stretch is a real risk the deluxe would have shrugged off.
- **Sensor ruggedness:** industrial RS485 air/light sensors became hobbyist
  breakout boards needing their own weatherproofing.
- **Distribution:** a fused 12 V bus with proper enclosure gave way to a simple
  single-cell setup with less room to grow.

What survived intact: the ToughC5 hub, soil temperature, air temperature and
humidity, ambient light, and the rain gauge.

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

## Power notes

- Panel -> bq24074 charger -> protected 18650 -> load. Never feed the charger's
  <=4.4 V load output into a USB-C port; verify every battery polarity before
  connecting.
- Commissioning: bench-test the power chain, calibrate each moisture probe in
  air, dry media, and wet media, soak-test outdoors 48-72 hours through midday
  heat and overnight dew, and confirm the float reports both states reliably.

## Docs

A detailed build document with full wiring tables, the power math,
commissioning gates, and decision notes is saved with this project as the
reference file.
