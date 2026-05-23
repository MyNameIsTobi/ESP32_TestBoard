# ESP32-S3 Strapping-Pins

> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 32-35 (Chapter 3: Boot Configurations)

## Warum sind Strapping-Pins kritisch?

Strapping-Pins werden vom Chip **beim Reset / Power-on** abgetastet und steuern damit, wie der Chip bootet (Boot-Modus, VDD_SPI-Spannung, ROM-Log, JTAG-Quelle). **Falsche Pegel an den Strapping-Pins = Chip bootet nicht oder bootet im falschen Modus**, z.B. in den UART-Download-Modus statt aus dem Flash.

Nach dem Reset werden die Werte in **Latches** gespeichert und die Pins sind als normale GPIOs nutzbar. Trotzdem gilt: extern angebrachte Last (LEDs, Pull-Widerstaende, anderer Logik-Output) darf den Reset-Pegel **nicht ueberschreiben**.

### Timing

- **t_SU (Setup time)**: 0 ms min - Power rails muessen stabil sein bevor CHIP_PU high geht
- **t_H (Hold time)**: **3 ms min** - nach CHIP_PU high muessen die Strapping-Pegel mindestens 3 ms gehalten werden, bevor die Pins als GPIO genutzt werden duerfen

## Default-Konfiguration der Strapping-Pins

| Strapping Pin | Default (internal) | Default Bit Value | Required external resistor |
|---------------|--------------------|--------------------|----------------------------|
| GPIO0 | Weak pull-up (WPU) | 1 (SPI boot) | Optional 10 kOhm PU + BOOT button to GND |
| GPIO3 | **Floating** (no internal PU/PD!) | - | **Muss extern getrieben werden** (PU oder PD) |
| GPIO45 | Weak pull-down (WPD) | 0 (3.3 V VDD_SPI) | Optional, default ist meist OK |
| GPIO46 | Weak pull-down (WPD) | 0 (SPI boot) | Optional 10 kOhm PD |

## Funktion der einzelnen Strapping-Pins

### GPIO0 + GPIO46: Chip Boot Mode

Bestimmen den Boot-Modus nach Reset:

| Boot Mode | GPIO0 | GPIO46 | Beschreibung |
|-----------|-------|--------|--------------|
| **SPI boot mode** (default) | **1** | Any value | Normalbetrieb: bootet aus internem Flash |
| Joint Download Boot mode | 0 | 0 | Firmware-Download via USB-Serial/JTAG, USB-OTG oder UART |

Joint Download Boot unterstuetzt:
- USB-Serial-JTAG Download Boot
- USB-OTG Download Boot
- UART Download Boot

Zusaetzlich existiert noch ein **SPI Download Boot mode** (siehe Technical Reference Manual > Chip Boot Control).

> **Beschaltung empfohlen:**
> - GPIO0: 10 kOhm Pull-Up nach 3V3 + Taster nach GND ("BOOT"-Button). Bei Druck waehrend Reset = Download-Modus.
> - GPIO46: Pull-Down 10 kOhm nach GND (oder offen lassen, da internal WPD reicht - aber externer PD ist robuster).
> - **GPIO0 niemals dauerhaft mit Last gegen GND verbinden**, sonst boot der Chip immer in Download.

### GPIO45: VDD_SPI Voltage Control

Bestimmt die Versorgungsspannung des internen Flash (VDD_SPI). Wirkt nur, wenn `EFUSE_VDD_SPI_FORCE = 0` (Default).

| Source | Voltage | EFUSE_VDD_SPI_FORCE | GPIO45 | EFUSE_VDD_SPI_TIEH |
|--------|---------|---------------------|--------|--------------------|
| **VDD3P3_RTC via R_SPI** (default) | **3.3 V** | **0** | **0** | Ignored |
| (forced) VDD3P3_RTC via R_SPI | 3.3 V | 1 | Ignored | 1 |
| Flash Voltage Regulator | 1.8 V | 0 | 1 | Ignored |
| Flash Voltage Regulator | 1.8 V | 1 | Ignored | 0 |

> **Fuer ESP32-S3FN8** (internal flash = 3.3 V): GPIO45 = **0** (Pull-Down). Das ist auch die Default-Einstellung (WPD). Externer 10 kOhm PD nach GND ist gute Praxis.
> **Achtung:** GPIO45 versehentlich auf High gezogen -> VDD_SPI = 1.8 V -> Flash bekommt zu wenig Spannung -> Boot-Fehler.

### GPIO46: ROM Message Printing Control + Boot Mode

Doppelfunktion:
1. Boot mode (zusammen mit GPIO0, siehe oben)
2. ROM-Bootloader-Nachrichten an UART0 / USB-Serial-JTAG

Steuerung der ROM-Messages erfolgt zusaetzlich ueber eFuses `EFUSE_UART_PRINT_CONTROL` und `EFUSE_DIS_USB_SERIAL_JTAG_ROM_PRINT`. Default: ROM-Log geht an UART0 **und** USB-Serial/JTAG.

### GPIO3: JTAG Signal Source

Bestimmt, ueber welche Pins JTAG funktioniert. **WICHTIG:** GPIO3 hat **keine internen Pull-Resistoren** - der Pegel muss extern definiert sein (kein floating!).

| JTAG Signal Source | EFUSE_DIS_PAD_JTAG | EFUSE_DIS_USB_JTAG | EFUSE_STRAP_JTAG_SEL | GPIO3 |
|--------------------|--------------------|--------------------|----------------------|-------|
| **USB Serial/JTAG Controller** (default) | **0** | **0** | **0** | Ignored |
| USB Serial/JTAG Controller | 0 | 0 | 1 | 1 |
| USB Serial/JTAG Controller | 1 | 0 | Ignored | Ignored |
| JTAG via pads (MTDI/MTCK/MTMS/MTDO) | 0 | 0 | 1 | **0** |
| JTAG via pads | 0 | 1 | Ignored | Ignored |
| JTAG disabled | 1 | 1 | Ignored | Ignored |

> **Wenn EFUSE_STRAP_JTAG_SEL nicht gebrannt ist (Default)**, ist GPIO3 quasi "Ignored" - aber lass es **nicht floaten**. Best practice: 10 kOhm Pull-Down nach GND, dann ist der Zustand definiert.

## Checkliste fuer die Schaltung

| Pin | Empfohlene externe Beschaltung |
|-----|-------------------------------|
| **GPIO0** | 10 kOhm PU + BOOT-Taster nach GND. Keine andere Last die GND zieht. |
| **GPIO3** | 10 kOhm PD nach GND. Pin nicht floaten lassen! |
| **GPIO45** | 10 kOhm PD nach GND (fuer 3.3 V Flash auf FN8). Verstaerkt internal WPD. |
| **GPIO46** | 10 kOhm PD nach GND. Keine Last die High zieht. |
| **EN (CHIP_PU)** | 10 kOhm PU + 1 uF nach GND (RC-Reset) + RESET-Taster nach GND |

## Gotchas / Haeufige Fehler

1. **GPIO0 mit LED nach GND**: zieht GPIO0 dauerhaft auf Low -> Chip bootet immer in Download-Modus. Loesung: LED nach 3V3 schalten (active low) oder anderen GPIO nehmen.

2. **GPIO46 als Status-LED nach 3V3**: zieht Pin High beim Reset -> Boot-Mode-Konflikt mit GPIO0. Loesung: LED nach GND schalten (active high).

3. **GPIO45 mit externem Pull-Up**: setzt VDD_SPI auf 1.8 V -> Flash auf S3FN8 (3.3 V) bekommt zu wenig Spannung -> Boot-Fehler. **Niemals** GPIO45 high ziehen wenn 3.3 V Flash gebraucht.

4. **GPIO3 floating**: undefined state, JTAG verhaelt sich nicht-deterministisch. Immer extern PU oder PD.

5. **CHIP_PU floating**: Datasheet sagt explizit "Do not leave the CHIP_PU pin floating". Chip startet nicht zuverlaessig.

6. **Strapping waehrend USB-Last**: Wenn USB-Host beim Plug-in den Reset triggert (durch VBUS), muessen die Strapping-Pegel innerhalb 3 ms (t_H) stabil sein. Lange Kabel + Kapazitaeten koennen das verzoegern - daher externe Pulls **immer** verwenden, nicht nur auf internal WPU/WPD verlassen.

## Power-Up Glitches an Strapping-Pins

GPIO3 hat einen Low-Level Glitch von ~60 us beim Power-Up - das ist innerhalb t_H und sollte vom externen Pull-Down ueberstimmt werden, wenn extern korrekt beschaltet.

GPIO0, GPIO45, GPIO46 sind in der Glitch-Tabelle nicht aufgefuehrt - ihre internen WPU/WPD halten den Pegel.
