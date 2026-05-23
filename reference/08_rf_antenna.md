# 08 - RF and Antenna (ESP32-S3)

> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 61-63, 70-76

RF data is measured **at the antenna port** (where the RF cable connects), **including front-end
loss**. The front-end test circuit is a **0 Ohm resistor** (no matching network in the reference
measurement). RF tests conducted at VDD = 3.3 V (+/- 5%), T_ambient = 25 C.

---

## 1. Frequency Range

| Standard          | Frequency Range          |
|-------------------|--------------------------|
| Wi-Fi 2.4 GHz     | 2412 - 2484 MHz          |
| Bluetooth LE      | 2402 - 2480 MHz          |
| Wireless standard | IEEE 802.11 b/g/n        |

ESP32-S3 supports **antenna diversity** via an external RF switch controlled by GPIO(s).

---

## 2. Antenna Port — LNA_IN

- **Pin 1: LNA_IN** is the single-ended RF input/output pin (TX and RX share this pin).
- **Target impedance: 50 Ohm** (single-ended, unbalanced).
- A **pi-matching network** (3 components, typically series-L + two shunt-C, or series-C + two
  shunt-L depending on antenna) is placed between LNA_IN and the antenna feed point.
- The exact pi-network values **depend on the chosen antenna** and PCB stack-up — they must
  be tuned with a VNA. Reference component **footprints** are always placed on the PCB even
  if some positions are populated with 0 Ohm or DNP (Do-Not-Populate).

Typical reference design topology (from Espressif schematics, exact values per antenna):

```
LNA_IN ----[C1 series]----+----[L2 series]----+---- ANT (PCB trace / connector)
                          |                   |
                       [L1 shunt]          [C2 shunt]
                          |                   |
                         GND                 GND
```

Starter values often used: **C1 = 0 Ohm (jumper)**, **L1 = DNP**, **C2 = DNP**, and tune from there
with a VNA. The datasheet itself **does not specify component values**; values are determined by the
antenna and layout. See *ESP32-S3 Hardware Design Guidelines* for the recommended PCB pattern.

---

## 3. Wi-Fi TX Power (Table 6-2)

Measured with spectral mask and EVM meeting 802.11 standards.

| Rate                    | Typ (dBm) |
|-------------------------|-----------|
| 802.11b, 1 Mbps         | **21.0**  |
| 802.11b, 11 Mbps        | 21.0      |
| 802.11g, 6 Mbps         | 20.5      |
| 802.11g, 54 Mbps        | 19.0      |
| 802.11n, HT20, MCS0     | 19.5      |
| 802.11n, HT20, MCS7     | 18.5      |
| 802.11n, HT40, MCS0     | 19.5      |
| 802.11n, HT40, MCS7     | 18.0      |

TX power is **adjustable** by software (firmware sets per-rate output level).

---

## 4. Wi-Fi TX EVM (Table 6-3)

| Rate                                | Typ (dB) | Limit (dB) |
|-------------------------------------|----------|------------|
| 802.11b, 1 Mbps, @21 dBm            | -24.5    | -10        |
| 802.11b, 11 Mbps, @21 dBm           | -24.5    | -10        |
| 802.11g, 6 Mbps, @20.5 dBm          | -21.5    | -5         |
| 802.11g, 54 Mbps, @19 dBm           | -28.0    | -25        |
| 802.11n, HT20, MCS0, @19.5 dBm      | -23.0    | -5         |
| 802.11n, HT20, MCS7, @18.5 dBm      | -29.5    | -27        |
| 802.11n, HT40, MCS0, @19.5 dBm      | -23.0    | -5         |
| 802.11n, HT40, MCS7, @18 dBm        | -29.5    | -27        |

---

## 5. Wi-Fi RX Sensitivity (Table 6-4)

PER (packet error rate) limits: 8% for 802.11b, 10% for 802.11g/n.

| Rate                  | Typ (dBm) |
|-----------------------|-----------|
| 802.11b, 1 Mbps       | **-98.4** |
| 802.11b, 2 Mbps       | -95.4     |
| 802.11b, 5.5 Mbps     | -93.0     |
| 802.11b, 11 Mbps      | -88.6     |
| 802.11g, 6 Mbps       | -93.2     |
| 802.11g, 9 Mbps       | -91.8     |
| 802.11g, 12 Mbps      | -91.2     |
| 802.11g, 18 Mbps      | -88.6     |
| 802.11g, 24 Mbps      | -86.0     |
| 802.11g, 36 Mbps      | -82.4     |
| 802.11g, 48 Mbps      | -78.2     |
| 802.11g, 54 Mbps      | -76.5     |
| 802.11n, HT20, MCS0   | -92.6     |
| 802.11n, HT20, MCS1   | -91.0     |
| 802.11n, HT20, MCS2   | -88.2     |
| 802.11n, HT20, MCS3   | -85.0     |
| 802.11n, HT20, MCS4   | -81.8     |
| 802.11n, HT20, MCS5   | -77.4     |
| 802.11n, HT20, MCS6   | -75.8     |
| 802.11n, HT20, MCS7   | -74.2     |
| 802.11n, HT40, MCS0   | -90.0     |
| 802.11n, HT40, MCS1   | -88.0     |
| 802.11n, HT40, MCS2   | -85.2     |
| 802.11n, HT40, MCS3   | -82.0     |
| 802.11n, HT40, MCS4   | -79.0     |
| 802.11n, HT40, MCS5   | -74.4     |
| 802.11n, HT40, MCS6   | -72.8     |
| 802.11n, HT40, MCS7   | -71.4     |

**Best-case sensitivity: -98.4 dBm @ 802.11b 1 Mbps.**

---

## 6. Wi-Fi Maximum RX Level (Table 6-5)

| Rate                  | Typ (dBm) |
|-----------------------|-----------|
| 802.11b, 1 Mbps       | 5         |
| 802.11b, 11 Mbps      | 5         |
| 802.11g, 6 Mbps       | 5         |
| 802.11g, 54 Mbps      | 0         |
| 802.11n, HT20, MCS0   | 5         |
| 802.11n, HT20, MCS7   | 0         |
| 802.11n, HT40, MCS0   | 5         |
| 802.11n, HT40, MCS7   | 0         |

---

## 7. Wi-Fi Adjacent Channel Rejection (Table 6-6)

| Rate                  | Typ (dB) |
|-----------------------|----------|
| 802.11b, 1 Mbps       | 35       |
| 802.11b, 11 Mbps      | 35       |
| 802.11g, 6 Mbps       | 31       |
| 802.11g, 54 Mbps      | 20       |
| 802.11n, HT20, MCS0   | 31       |
| 802.11n, HT20, MCS7   | 16       |
| 802.11n, HT40, MCS0   | 25       |
| 802.11n, HT40, MCS7   | 11       |

---

## 8. Bluetooth LE TX Characteristics

### 8.1 BLE 1 Mbps (Table 6-8)

| Parameter                          | Min    | Typ    | Max    | Unit |
|------------------------------------|--------|--------|--------|------|
| RF power control range             | -24.00 | 0      | **20.00** | dBm  |
| Gain control step                  | -      | 3.00   | -      | dB   |
| Carrier freq offset, Max \|fn\|    | -      | 2.50   | -      | kHz  |
| Modulation, delta f1_avg           | -      | 249.00 | -      | kHz  |
| In-band spurious, +/-2 MHz offset  | -      | -37.00 | -      | dBm  |
| In-band spurious, +/-3 MHz offset  | -      | -42.00 | -      | dBm  |
| In-band spurious, > +/-3 MHz       | -      | -44.00 | -      | dBm  |

### 8.2 BLE 2 Mbps (Table 6-9)

| Parameter                          | Min    | Typ    | Max    | Unit |
|------------------------------------|--------|--------|--------|------|
| RF power control range             | -24.00 | 0      | 20.00  | dBm  |
| Modulation, delta f1_avg           | -      | 499.00 | -      | kHz  |
| In-band spurious, +/-4 MHz offset  | -      | -43.80 | -      | dBm  |
| In-band spurious, +/-5 MHz offset  | -      | -45.80 | -      | dBm  |

### 8.3 BLE 125 Kbps Coded PHY (Table 6-10)

| Parameter                          | Min    | Typ    | Max    | Unit |
|------------------------------------|--------|--------|--------|------|
| RF power control range             | -24.00 | 0      | 20.00  | dBm  |
| Modulation, delta f1_avg           | -      | 248.00 | -      | kHz  |

### 8.4 BLE 500 Kbps Coded PHY (Table 6-11)

| Parameter                          | Min    | Typ    | Max    | Unit |
|------------------------------------|--------|--------|--------|------|
| RF power control range             | -24.00 | 0      | 20.00  | dBm  |
| Modulation, delta f2_avg           | -      | 213.00 | -      | kHz  |

BLE PHYs are **Class 1 capable** (up to 20 dBm) **without external PA**.

---

## 9. Bluetooth LE RX Sensitivity (@ 30.8% PER)

| BLE PHY      | Sensitivity (dBm) | Max RX signal (dBm) |
|--------------|-------------------|---------------------|
| 1 Mbps       | **-97.5**         | 8                   |
| 2 Mbps       | -93.5             | 3                   |
| 125 Kbps     | **-104.5**        | 8                   |
| 500 Kbps     | -101              | 8                   |

**Best BLE sensitivity: -104.5 dBm at 125 Kbps Coded PHY** (long-range mode).

### Co-channel and adjacent channel C/I (Table 6-12, 1 Mbps excerpt)

| Offset                  | C/I (dB) |
|-------------------------|----------|
| F = F0 (co-channel)     | 9        |
| F = F0 +/- 1 MHz        | -3       |
| F = F0 +/- 2 MHz        | -28 / -30|
| F = F0 +/- 3 MHz        | -31 / -33|
| Image frequency         | -32      |

### Out-of-band blocking (BLE 1 Mbps)

| Range                  | Typ (dBm) |
|------------------------|-----------|
| 30 - 2000 MHz          | -9        |
| 2003 - 2399 MHz        | -19       |
| 2484 - 2997 MHz        | -16       |
| 3000 MHz - 12.75 GHz   | -5        |

Intermodulation: -31 dBm.

---

## 10. Antenna Design Checklist

- **Use a 50 Ohm controlled-impedance trace** from LNA_IN to the matching network and from there
  to the antenna feed (coplanar waveguide on the standard 4-layer 1.6 mm FR4 stack — calculate
  trace width with an impedance calculator for your stack-up).
- **Pi-network footprints** (3 components: series + 2 shunt) must be placed; start with C1 = 0R,
  L1 / C2 = DNP, then tune with a VNA after assembly.
- **Keep RF trace short** (< 10 mm ideally) and as straight as possible. No vias on the RF trace if
  possible.
- **No ground pour breaks** beneath the RF trace.
- **Antenna keepout zone:** the area under and around a PCB antenna must be **free of copper**
  (typically 7 x 15 mm clearance under a chip antenna, manufacturer-dependent). Check antenna
  datasheet.
- **Edge placement**: PCB antenna should be at the **board edge** with the keepout area facing
  outward, away from ground plane.
- **External PA / LNA**: **not supported** since v1.5 of the datasheet (the v1.5 revision history
  explicitly removed the "External PA is supported" feature). Use the chip output as-is.
- **For certified modules**: consider using a pre-certified Espressif module (ESP32-S3-WROOM-1)
  instead of the bare chip — saves FCC / CE certification cost.

---

## 11. Quick Reference (Critical Numbers)

| Parameter                       | Value                  |
|---------------------------------|------------------------|
| Wi-Fi TX peak power             | **+21.0 dBm** (802.11b)|
| Wi-Fi best sensitivity          | **-98.4 dBm** (802.11b 1M) |
| BLE TX power range              | -24 to +20 dBm         |
| BLE best sensitivity (1 Mbps)   | **-97.5 dBm**          |
| BLE best sensitivity (125 Kbps) | **-104.5 dBm**         |
| Antenna port impedance          | **50 Ohm** single-ended|
| RF pin                          | **LNA_IN (Pin 1)**     |
| Frequency band                  | 2402 - 2484 MHz        |
