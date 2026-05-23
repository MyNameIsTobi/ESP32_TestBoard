# 06 - Electrical Characteristics (ESP32-S3)

> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 64-69

---

## 1. Absolute Maximum Ratings

Stresses above these values may cause **permanent damage** to the device. Operation at these
limits is **not implied** — see Recommended Operating Conditions below for normal use.

| Parameter           | Description                | Min   | Max  | Unit |
|---------------------|----------------------------|-------|------|------|
| Input power pins    | Allowed input voltage      | -0.3  | 3.6  | V    |
| I_output            | Cumulative IO output current | -   | 1500 | mA   |
| T_STORE             | Storage temperature        | -40   | 150  | C    |

Notes:
- I_output verified by pulling all IO pins high while grounded for 24 h at 25 C.
- 3.6 V is a **hard ceiling** — your LDO output must never exceed this, even during transients.

---

## 2. Recommended Operating Conditions

Use these ranges for normal operation. Ambient temperature: -40 to +85 C (standard ESP32-S3FN8).

| Parameter (Pin)   | Description                | Min | Typ | Max | Unit |
|-------------------|----------------------------|-----|-----|-----|------|
| VDDA, VDD3P3      | Analog / RF supply         | 3.0 | 3.3 | 3.6 | V    |
| VDD3P3_RTC        | RTC + low-power domain     | 3.0 | 3.3 | 3.6 | V    |
| VDD_SPI (as input)| SPI flash supply           | 1.8 | 3.3 | 3.6 | V    |
| VDD3P3_CPU        | CPU / digital domain       | 3.0 | 3.3 | 3.6 | V    |
| I_VDD             | Cumulative input current   | 0.5 | -   | -   | A    |

Notes:
- **All four 3V3 rails can be tied together to a single 3.3 V LDO.**
- **Recommended single-supply output current: >= 500 mA.**
- For eFuse burning: VDD3P3_CPU must **not exceed 3.3 V** (eFuse circuits are sensitive).

---

## 3. VDD_SPI Output Characteristics

(Relevant if VDD_SPI is internally powered from VDD3P3_RTC via R_SPI for 3.3 V flash.)

| Parameter | Description                                                  | Typ | Unit |
|-----------|--------------------------------------------------------------|-----|------|
| R_SPI     | Internal resistance VDD3P3_RTC -> VDD_SPI (3.3 V flash/PSRAM)| 14  | Ohm  |
| I_SPI     | Output current when VDD_SPI powered by 1.8 V flash regulator | 40  | mA   |

For the ESP32-S3**FN8** (built-in 3.3 V flash, in-package), VDD_SPI is fed from VDD3P3_RTC via R_SPI.
Ensure: `VDD3P3_RTC >= VDD_flash_min + I_flash_max * R_SPI`.

---

## 4. DC Characteristics (3.3 V, 25 C)

Reference VDD = supply voltage of the respective power domain.

| Parameter | Description                              | Min        | Typ | Max         | Unit |
|-----------|------------------------------------------|------------|-----|-------------|------|
| C_IN      | Pin capacitance                          | -          | 2   | -           | pF   |
| V_IH      | High-level input voltage                 | 0.75 x VDD | -   | VDD + 0.3   | V    |
| V_IL      | Low-level input voltage                  | -0.3       | -   | 0.25 x VDD  | V    |
| I_IH      | High-level input current (leakage)       | -          | -   | 50          | nA   |
| I_IL      | Low-level input current (leakage)        | -          | -   | 50          | nA   |
| V_OH      | High-level output voltage                | 0.8 x VDD  | -   | -           | V    |
| V_OL      | Low-level output voltage                 | -          | -   | 0.1 x VDD   | V    |
| I_OH      | High-level source current (PAD_DRIVER=3, V_OH >= 2.64 V) | - | 40 | - | mA |
| I_OL      | Low-level sink current   (PAD_DRIVER=3, V_OL = 0.495 V)  | - | 28 | - | mA |
| R_PU      | Internal weak pull-up resistor           | -          | 45  | -           | kOhm |
| R_PD      | Internal weak pull-down resistor         | -          | 45  | -           | kOhm |
| V_IH_nRST | CHIP_PU release threshold (high)         | 0.75 x VDD | -   | VDD + 0.3   | V    |
| V_IL_nRST | CHIP_PU assert threshold (low)           | -0.3       | -   | 0.25 x VDD  | V    |

For VDD = 3.3 V:
- V_IH(min) = 2.475 V, V_IL(max) = 0.825 V  — most 3.3 V logic, including 5 V-tolerant open-drain via level shift, will work.
- V_OH(min) = 2.64 V, V_OL(max) = 0.33 V.
- Pull-up/pull-down resistors ~45 kOhm — for I2C use **external** stronger pull-ups (typ. 2.2-10 kOhm).

---

## 5. CHIP_PU / Reset

| Signal   | Description                  | Threshold (VDD = 3.3 V) |
|----------|------------------------------|-------------------------|
| CHIP_PU high | Chip released from reset | >= 2.475 V              |
| CHIP_PU low  | Chip held in reset       | <= 0.825 V              |

Design hint: typical reset network is **CHIP_PU --> 10 kOhm pull-up to 3V3, 1 nF to GND, optional
reset push-button to GND**. No dedicated power-up timing table is given in this datasheet — refer
to the *ESP32-S3 Hardware Design Guidelines* for power-on sequence and the EN/CHIP_PU RC delay.

---

## 6. Crystal Requirements (40 MHz main XTAL)

> The datasheet does **not** include a dedicated XTAL spec table. The crystal requirements are
> covered in the *ESP32-S3 Hardware Design Guidelines* (see `Related Documentation` on p. 82).

Practical values used by Espressif's reference designs (40 MHz):
- Frequency tolerance: **+/- 10 ppm** (initial + temperature + aging combined).
- ESR: **<= 65 Ohm** (lower is better; typical 40-60 Ohm).
- Load capacitance C_L: **8 - 10 pF** (matched on PCB with two ~12-22 pF caps to GND, exact value
  per `C1 = C2 = 2 * (C_L - C_stray)`).
- Drive level: >= 200 uW.

A 32.768 kHz crystal on XTAL_32K_P / XTAL_32K_N is **optional** (only needed for highly accurate
RTC during deep-sleep). Internal RC oscillator is otherwise used.

**Always verify the chosen crystal against the official Hardware Design Guidelines before order.**

---

## 7. ADC Characteristics (informative)

(Useful if you plan to use the on-chip ADC for analog inputs.)

| Symbol | Description                | Min | Max | Unit |
|--------|----------------------------|-----|-----|------|
| DNL    | Differential nonlinearity  | -4  | 4   | LSB  |
| INL    | Integral nonlinearity      | -8  | 8   | LSB  |
| -      | Sampling rate              | -   | 100 | kSPS |

ADC calibration total error (after HW + SW cal):

| Attenuation | Effective range (mV) | Total error (mV) |
|-------------|----------------------|------------------|
| ATTEN0      | 0 - 850              | +/- 5            |
| ATTEN1      | 0 - 1100             | +/- 6            |
| ATTEN2      | 0 - 1600             | +/- 10           |
| ATTEN3      | 0 - 2900             | +/- 50           |

Measurement uses an **external 100 nF cap** on the ADC pin, DC input, 25 C, Wi-Fi disabled.
ADC2 channels cannot be used simultaneously with Wi-Fi.

---

## 8. Reliability (Page 69, Table 5-13 — informative)

| Test            | Condition / Limit                          |
|-----------------|--------------------------------------------|
| ESD HBM         | +/- 2000 V (JS-001)                        |
| ESD CDM         | +/- 1000 V (JS-002)                        |
| Latch up        | +/- 200 mA / 1.5 x VDDmax (JESD78)         |
| HTOL            | 125 C, 1000 h                              |
| Moisture soak   | Level 3: 192 h at 30 C, 60% RH             |
| Reflow          | 260 +0 C, 20 s, 3x                         |

Handling: **MSL 3** — bake before reflow if exposed to humid air for > 168 h.
