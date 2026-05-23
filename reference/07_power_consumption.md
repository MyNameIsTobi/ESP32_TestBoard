# 07 - Power Consumption (ESP32-S3)

> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 66-68

Measurement conditions (unless noted): VDD = 3.3 V, T_ambient = 25 C.
TX current is rated at **100% duty cycle**. RX current is rated with peripherals disabled and CPU idle.

---

## 1. Active Mode - Wi-Fi 2.4 GHz (Table 5-7)

| Work Mode           | RF Condition | Description                       | Peak (mA) |
|---------------------|--------------|-----------------------------------|-----------|
| Active (RF working) | TX           | 802.11b, 1 Mbps, @21 dBm          | **340**   |
| Active (RF working) | TX           | 802.11g, 54 Mbps, @19 dBm         | 291       |
| Active (RF working) | TX           | 802.11n, HT20, MCS7, @18.5 dBm    | 283       |
| Active (RF working) | TX           | 802.11n, HT40, MCS7, @18 dBm      | 286       |
| Active (RF working) | RX           | 802.11b/g/n, HT20                 | 88        |
| Active (RF working) | RX           | 802.11n, HT40                     | 91        |

**Worst-case peak: 340 mA** during 802.11b @21 dBm TX bursts.

---

## 2. Active Mode - Bluetooth LE (Table 5-8)

| Work Mode           | RF Condition | Description           | Peak (mA) |
|---------------------|--------------|-----------------------|-----------|
| Active (RF working) | TX           | BLE @ 21.0 dBm        | **335**   |
| Active (RF working) | TX           | BLE @ 9.0 dBm         | 193       |
| Active (RF working) | TX           | BLE @ 0 dBm           | 176       |
| Active (RF working) | TX           | BLE @ -15.0 dBm       | 116       |
| Active (RF working) | RX           | BLE                   | 93        |

---

## 3. Modem-Sleep Mode (Table 5-9, CPU running, Wi-Fi clock gated)

`Typ1` = all peripheral clocks **disabled**. `Typ2` = all peripheral clocks **enabled**.

| CPU freq | Description                                | Typ1 (mA) | Typ2 (mA) |
|----------|--------------------------------------------|-----------|-----------|
| 40 MHz   | WAITI (dual core idle)                     | 13.2      | 18.8      |
| 40 MHz   | Single core, 32-bit data access            | 16.2      | 21.8      |
| 40 MHz   | Dual core, 32-bit data access              | 18.7      | 24.4      |
| 40 MHz   | Single core, 128-bit data access           | 19.9      | 25.4      |
| 40 MHz   | Dual core, 128-bit data access             | 23.0      | 28.8      |
| 80 MHz   | WAITI                                      | 22.0      | 36.1      |
| 80 MHz   | Single core, 32-bit                        | 28.4      | 42.6      |
| 80 MHz   | Dual core, 32-bit                          | 33.1      | 47.3      |
| 80 MHz   | Single core, 128-bit                       | 35.1      | 49.6      |
| 80 MHz   | Dual core, 128-bit                         | 41.8      | 56.3      |
| 160 MHz  | WAITI                                      | 27.6      | 42.3      |
| 160 MHz  | Single core, 32-bit                        | 39.9      | 54.6      |
| 160 MHz  | Dual core, 32-bit                          | 49.6      | 64.1      |
| 160 MHz  | Single core, 128-bit                       | 54.4      | 69.2      |
| 160 MHz  | Dual core, 128-bit                         | 66.7      | 81.1      |
| 240 MHz  | WAITI                                      | 32.9      | 47.6      |
| 240 MHz  | Single core, 32-bit                        | 51.2      | 65.9      |
| 240 MHz  | Dual core, 32-bit                          | 66.2      | 81.3      |
| 240 MHz  | Single core, 128-bit                       | 72.4      | 87.9      |
| 240 MHz  | Dual core, 128-bit                         | **91.7**  | **107.9** |

Notes:
- Accessing **flash** during Modem-sleep adds approx. **+10 mA** (flash @80 Mbit/s, SPI 2-line).

---

## 4. Low-Power Modes (Table 5-10)

| Work mode       | Description                                                       | Typ      |
|-----------------|-------------------------------------------------------------------|----------|
| Light-sleep     | VDD_SPI and Wi-Fi powered down, all GPIOs high-impedance          | 240 uA   |
| Deep-sleep      | ULP-FSM co-processor powered                                      | 170 uA   |
| Deep-sleep      | ULP-RISC-V co-processor powered                                   | 190 uA   |
| Deep-sleep      | ULP sensor-monitored pattern (touch @ 1% duty cycle)              | 18 uA    |
| Deep-sleep      | RTC memory and RTC peripherals powered up                         | 8 uA     |
| Deep-sleep      | RTC memory only (RTC peripherals down)                            | **7 uA** |
| Power off       | CHIP_PU = low, chip shut down                                     | 1 uA     |

Notes:
- Light-sleep with **8 MB 8-line PSRAM (3.3 V)**: add +140 uA.
- Light-sleep with **8 MB 8-line PSRAM (1.8 V)**: add +200 uA.
- Light-sleep with **2 MB 4-line PSRAM (3.3 V)**: add +40 uA.
- ESP32-S3FN8 (no PSRAM) — Light-sleep stays at 240 uA.

---

## 5. Power Supply Requirements (Summary for LDO Sizing)

| Requirement                          | Value                                |
|--------------------------------------|--------------------------------------|
| Recommended single-supply current    | **>= 500 mA continuous**             |
| Absolute peak (Wi-Fi 802.11b TX)     | **340 mA** (BLE @21 dBm: 335 mA)     |
| Absolute peak (worst-case headroom)  | Allow ~500 mA peak transient         |
| Quiescent (deep-sleep, RTC only)     | ~7 uA                                |
| Voltage rail                         | 3.3 V (range 3.0 - 3.6 V)            |
| Cumulative ABS-MAX input current     | < 1.5 A (sum of all VDD pins)        |

**LDO recommendation:** choose a device with **>= 500 mA continuous output**, low dropout (LDO drop
+ wiring drop must keep VDD >= 3.0 V even at 500 mA), and good transient response (Wi-Fi TX
bursts are short, fast load steps from ~50 mA to 340 mA in microseconds).

Common choices: **AMS1117-3.3 (1 A) — borderline due to ~1.1 V dropout** (needs >= 4.4 V input).
Better: **NCP1117ST33 (1 A, 1.2 V dropout)**, **TLV1117-33 (1 A)**, or for Li-ion direct power
**ME6211C33M5G / RT9080 / TPS73633 (low-Iq, 500-800 mA, <300 mV dropout)**.

---

## 6. Decoupling Capacitors (from Hardware Design Guidelines)

> The **datasheet itself does not list decoupling cap values**. These come from the
> *ESP32-S3 Hardware Design Guidelines* and Espressif's reference schematics.

Standard layout (reference design for ESP32-S3FN8):

| Net          | Caps                                                | Purpose                      |
|--------------|-----------------------------------------------------|------------------------------|
| VDD3P3 (RF)  | 10 uF + 0.1 uF, both close to pin                   | Bulk + HF decoupling for RF  |
| VDDA / RF    | 0.1 uF + small (10 pF / DNP) close to pin           | RF-clean supply              |
| VDD3P3_CPU   | 0.1 uF per pin                                      | Digital supply               |
| VDD3P3_RTC   | 0.1 uF                                              | RTC + flash supply           |
| VDD_SPI      | 1 uF (close to flash if external)                   | Flash supply                 |
| LDO input    | 10 uF (electrolytic / tantalum) + 0.1 uF (MLCC)     | Input bulk + ESR damping     |
| LDO output   | 10 uF + 0.1 uF                                      | Output stability             |

Rule of thumb: **one 0.1 uF MLCC per power pin, placed within ~3 mm of the pin**, plus
**one 10 uF bulk cap per supply rail near the chip**. Use X7R or X5R, 0402 / 0603 sized.
