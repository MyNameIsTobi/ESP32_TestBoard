> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 13 (Section 1.1 Nomenclature, 1.2 Comparison, 1.3 Chip Revision)

# ESP32-S3 Varianten

## Nomenclature (Part-Number Aufbau)

Schema: `ESP32-S3 [F] [H/N] [x] [R] [H] [x] [V]`

| Position | Bedeutung | Werte |
|---|---|---|
| `ESP32-S3` | Chip series | — |
| `F` | Flash present | Flash integriert wenn vorhanden |
| `H/N` | Flash temperature | **H** = High temp / **N** = Normal temp |
| `x` (nach F/H/N) | Flash size in MB | z.B. 4, 8 |
| `R` | PSRAM present | PSRAM integriert wenn vorhanden |
| `H` | PSRAM temperature | **H** = High temp |
| `x` (nach R/H) | PSRAM size in MB | z.B. 2, 8, 16 |
| `V` | Voltage marker | nur 1.8 V externe SPI flash |

### Suffix-Cheatsheet

| Suffix | Bedeutung |
|---|---|
| `FN8` | **F**lash, **N**ormal temp, **8** MB Flash, kein PSRAM |
| `FN4` / `FH4` | 4 MB Flash (Normal / High temp) |
| `R8` | 8 MB PSRAM (Octal SPI), kein internes Flash |
| `R2` | 2 MB PSRAM (Quad SPI), kein internes Flash |
| `RH2` | 2 MB PSRAM mit High temp Rating |
| `R8V` / `R16V` | PSRAM + 1.8 V externer SPI Flash erforderlich |
| `FH4R2` | 4 MB Flash (High temp) + 2 MB PSRAM (Quad SPI) |

---

## Series Comparison (Tabelle 1-1)

| Part Number | In-Package Flash | In-Package PSRAM | Ambient Temp. | VDD_SPI Voltage | Chip Revision | Status |
|---|---|---|---|---|---|---|
| ESP32-S3 | — | — | -40 ~ 105 °C | 3.3 V / 1.8 V | v0.1 / v0.2 | active |
| **ESP32-S3FN8** | **8 MB (Quad SPI)** | **—** | **-40 ~ 85 °C** | **3.3 V** | **v0.1 / v0.2** | **active (target chip)** |
| ESP32-S3RH2 | — | 2 MB (Quad SPI) | -40 ~ 105 °C | 3.3 V | v0.2 | active |
| ESP32-S3R8 | — | 8 MB (Octal SPI) | -40 ~ 65 °C | 3.3 V | v0.1 / v0.2 | active |
| ESP32-S3R16V | — | 16 MB (Octal SPI) | -40 ~ 65 °C | 1.8 V | v0.2 | active |
| ESP32-S3FH4R2 | 4 MB (Quad SPI) | 2 MB (Quad SPI) | -40 ~ 85 °C | 3.3 V | v0.1 / v0.2 | active |
| ESP32-S3R8V | — | 8 MB (Octal SPI) | -40 ~ 65 °C | 1.8 V | v0.1 / v0.2 | **EOL** |
| ESP32-S3R2 | — | 2 MB (Quad SPI) | -40 ~ 85 °C | 3.3 V | v0.1 / v0.2 | **EOL** (replaced by ESP32-S3RH2) |

---

## Notes (laut Footnotes der Tabelle)

1. **In-Package Flash** — by default Max Clock 80 MHz; **Auto-suspend wird nicht unterstützt**. Für 120 MHz Flash-Clock oder Auto-suspend → Espressif kontaktieren.
2. **Ambient Temperature** = Umgebungstemperatur unmittelbar außerhalb des Chips.
3. **Octal SPI PSRAM Chips** (S3R8, S3R8V, S3R16V): wenn PSRAM **ECC** aktiviert ist, kann Ambient bis 85 °C, aber die nutzbare PSRAM-Größe reduziert sich um 1/16.
4. **VDD_SPI** — siehe Power Supply Section.
5. **ESP32-S3R2** wurde durch **ESP32-S3RH2** ersetzt (siehe PCN).
6. **ESP32-S3FH4R2** hat ein abweichendes QFN56-Package (Figure 7-2 im Datasheet) — die anderen Varianten verwenden das Standard-QFN56 (Figure 7-1).

---

## Chip Revision (Section 1.3)

> "ESP32-S3 now has multiple chip revisions available on the market using the same part number."

Für Revision-Identifikation und Errata-Fixes pro Revision → ESP32-S3 Series SoC Errata.

---

## Target Chip: ESP32-S3FN8 — Highlights

| Property | Value |
|---|---|
| In-Package Flash | **8 MB Quad SPI** |
| In-Package PSRAM | **keiner** |
| Ambient Temp. | -40 ~ 85 °C |
| VDD_SPI | **3.3 V** |
| Chip Revision | v0.1 / v0.2 |
| Package | QFN56 (7×7 mm), Standard-Layout (Figure 7-1) |
| Flash Clock (Default) | 80 MHz max., kein Auto-suspend |
