# Muse Garden Sensor

A deluxe weatherproof garden sensor system for a Houston garden, built around the
M5Stack ToughC5 (ESP32-C5) as the field gateway. One rugged hub, an RS485 sensor
trunk, and a solar-plus-battery power plant sized for Houston sun.

Status: planning only. Nothing has been purchased. Prices below are estimates
observed on October 14, 2026, before tax and shipping.

## System overview

The ToughC5 sits at the center: ESP32-C5 (RISC-V @ 240 MHz), 16 MB flash, 8 MB
PSRAM, dual-band Wi-Fi 6, BLE 5, and an 802.15.4 radio (Zigbee/Thread), with a
2.0-inch capacitive touch display for local readouts, plus a real-time clock and
microSD slot for logging. Its weather-resistant housing (not for immersion) and
M12 cable entries make it a genuine outdoor controller. Power reaches it three
ways: USB-C, 6-24 V on the RS485 interface, or an external 3.7 V battery.

Three signal paths leave the hub:

| Path | Carries | Devices |
|---|---|---|
| RS485 trunk | Modbus RTU sensor bus, 12 V fused rail | 4x soil-moisture zones, air temp/humidity, ambient light, 7-in-1 soil probe |
| Discrete inputs | 1-Wire + pulse counting | Soil-temperature probe, tipping-bucket rain gauge |
| Power | 200 W PV -> 20 A MPPT -> 12.8 V 100 Ah LiFePO4 | Panel, controller, battery, 5 V buck for accessories |

## Sensors

| Sensor | Role | Interface | Est. price |
|---|---|---|---|
| 4x Seeed S-Soil MT-02A | Soil moisture + temperature, one per bed zone (IP68, 5 m cable) | RS485, addresses 1-4 | $79 ea |
| DFRobot SEN0438 | Air temperature / humidity (needs ventilated radiation shield) | RS485 | $29.90 |
| DFRobot SEN0644 | Ambient light, 0-200,000 lux (IP68) | RS485 | $11.90 |
| 7-in-1 RS485 soil probe | Moisture, temperature, EC, pH, nitrogen, phosphorus, potassium | RS485, 12-24 V | $211 |
| Adafruit DS18B20 waterproof | Soil temperature at a fixed reference depth | 1-Wire (needs 4.7k pull-up) | $9.95 |
| Tipping-bucket rain gauge | Rainfall, 0.2794 mm per tip | Pulse / dry contact | $14.95 |
| M5Stack ToughC5 | Gateway, display, radio | - | $49.90 |

The 7-in-1 probe's NPK/pH/EC readings are best treated as trend signals and
checked against a proper lab soil test before any fertilizer decisions.

## Wiring notes

- Run one linear RS485 trunk, device to device, with short stubs only. Add 120-ohm
  termination at the far end; terminate the controller end only if it is the other
  physical end. Keep A/B polarity consistent and share a common DC ground.
- Set each RS485 device's Modbus address one at a time before joining the shared
  bus, label both cable ends, and confirm clean reads at the farthest sensor.
- DS18B20: 3.3/5 V, GND, one GPIO, 4.7k resistor from DATA to V+.
- Rain gauge: GPIO + ground with a pull-up; debounce in firmware and add surge/ESD
  protection on the exposed run.
- Fuse the battery positive close to the battery; run separate fused branches for
  the controller, the sensor rail, and the 5 V buck. Ferrules on stranded wire,
  DIN terminal blocks, UV-rated outdoor cable with drip loops, glands entering
  from the bottom or sides, never the top face.
- Confirm the exact ToughC5 internal connector pinout and every sensor's wire
  colors against its own manual before connecting power. Wire colors are not
  standardized across vendors.
- Commission in order: bench the power system, set the MPPT to the battery maker's
  LiFePO4 profile, soak-test everything above ground for 48-72 hours through
  midday heat and overnight dew, then bury and seal.

## Power plan (Houston)

Design point is a 12 W continuous load (display and radio peaks, converter loss,
warm-weather derating, and headroom for future sensors), or 288 Wh/day.

- 200 W rigid panel (Renogy 200 W ShadowFlux, IP67), true south, ~50-degree tilt
  for winter-biased output and runoff. Conservative winter math: 200 W x 3.7
  peak-sun-hours x 0.75 derate = 555 Wh/day, about 1.9x the design load.
- 20 A MPPT controller (Renogy Rover 20 A) on a LiFePO4 profile.
- 12.8 V 100 Ah LiFePO4 (ExpertPower EP12100): 1,024 Wh usable at 80% depth of
  discharge, ~3.6 days of autonomy. Keep it in its own shaded, ventilated,
  weatherproof box, never sealed in the same undersized enclosure as the
  controller. Standard LiFePO4 charges only from 32-140 F, so configure
  low-temperature charge cut-off for Houston's cold snaps.

## Priced parts (October 14, 2026 estimates)

| Part | Qty | Unit | Extended |
|---|---|---|---|
| M5Stack ToughC5 | 1 | $49.90 | $49.90 |
| Seeed S-Soil MT-02A | 4 | $79.00 | $316.00 |
| DFRobot SEN0438 air temp/humidity | 1 | $29.90 | $29.90 |
| Radiation shield | 1 | $20.99 | $20.99 |
| Adafruit DS18B20 waterproof | 1 | $9.95 | $9.95 |
| DFRobot SEN0644 light | 1 | $11.90 | $11.90 |
| Tipping-bucket rain gauge | 1 | $14.95 | $14.95 |
| 7-in-1 RS485 soil probe | 1 | $211.00 | $211.00 |
| Renogy 200 W ShadowFlux panel | 1 | $239.99 | $239.99 |
| Renogy Rover 20 A MPPT | 1 | $87.99 | $87.99 |
| ExpertPower 12.8 V 100 Ah LiFePO4 | 1 | $329.99 | $329.99 |
| 12 V to 5 V buck, 3 A | 1 | $9.99 | $9.99 |
| Polycase WP-35 enclosure (NEMA 4X) | 1 | $64.86 | $64.86 |
| **Deluxe total** | | | **~$1,397** |
| Core total without premium probe | | | ~$1,186 |

Deliberately not priced: battery box, panel rack/mounting post, cable by measured
length, glands, fuses and disconnects, terminal blocks, surge protection, and
grounding hardware. Those depend on the actual site, so any number would be made
up. Verify exact model revisions, manuals, and cable lengths before buying.

## Docs

A detailed 7-page build document with full wiring tables, the power math, and
decision notes is saved with this project as the reference file.
