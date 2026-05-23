# ESP32-S3FN8 Referenz-DB

Schnell-Nachschlage-Sammlung für Board-Design. Aus Datenblatt extrahiert.

## Wichtig: Quelle der Wahrheit

> **`datasheets/esp32-s3_datasheet_en.pdf` (v2.2) ist die einzige verbindliche Quelle.**
> Diese Markdown-Dateien sind Schnell-Referenz. Bei Konflikt → **immer ins PDF schauen**.
> KI hat das extrahiert, kann fehlerhaft/lückenhaft sein.
> Pi-Matching-Werte, Decoupling-Caps, Crystal-Specs → zusätzlich `Hardware Design Guidelines` von Espressif konsultieren.

## Index

| # | Datei | Inhalt |
|---|-------|--------|
| 01 | [Overview](01_overview.md) | Features, Block-Diagramm, Architektur |
| 02 | [Varianten](02_variants.md) | S3 / S3FN8 / S3R8 etc. Vergleich. **Target: S3FN8** |
| 03 | [Pinout](03_pinout.md) | Alle 56 QFN-Pins + IO MUX |
| 04 | [Strapping-Pins](04_strapping_pins.md) | **KRITISCH**: GPIO0/3/45/46 Boot-Konfig |
| 05 | [Peripherals](05_peripherals.md) | USB, SPI, UART, I2C, ADC, Touch, JTAG Pin-Map |
| 06 | [Electrical](06_electrical.md) | Abs-Max, VDD-Bereich, DC-Specs |
| 07 | [Power Consumption](07_power_consumption.md) | Strom pro Modus, LDO-Dimensionierung |
| 08 | [RF / Antenne](08_rf_antenna.md) | TX-Power, RX-Sensitivity, 50Ω LNA_IN |
| 09 | [Package](09_package.md) | QFN56 7×7mm Mechanik, EPAD 4×4mm |
| 10 | [Ordering](10_ordering.md) | Part-Nummern, Marking |

## Top-Gotchas für S3FN8 (kurz)

1. **Reserved Flash-Pins:** Pins 28-35 (GPIO26-32) intern an Flash. Nicht für GPIO nutzen.
2. **GPIO45 muss PD (10kΩ nach GND)** → sonst VDD_SPI=1.8V → Flash startet nicht.
3. **GPIO3 hat keinen internen Pull** → extern definieren.
4. **CHIP_PU (EN, Pin 4):** Nicht floating! 10kΩ PU + 1µF + Reset-Taster.
5. **40MHz Crystal Pflicht** → S3 läuft nicht ohne externen XTAL.
6. **VDD max 3.6V** absolute Grenze.
7. **LDO mind. 500mA**, Peak 340mA bei WiFi-TX.
8. **Pin 1 = LNA_IN, 50Ω single-ended** → Pi-Matching + 50Ω-Trace zur Antenne.

## Workflow

Beim Schaltplan-Zeichnen: erst hier nachschauen, bei Zweifeln ins PDF.
Beim Layout: Pinout + Package-Datei offen halten.
Beim Bestücken: Ordering-Datei für richtige Bestellnummer.
