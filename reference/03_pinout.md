# ESP32-S3FN8 Pin-Belegung (QFN56)

> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 15-31 (Pin Layout, Pin Overview, IO MUX, Peripheral Pin Assignment, Pin Mapping Chip-Flash)

## Chip-Variante

- **Target:** ESP32-S3FN8 (QFN56, 7 x 7 mm)
- **Internal flash:** 8 MB (in-package), connected via SPI0/1
- **Internal PSRAM:** none on FN8 (no in-package PSRAM)
- **Total pins:** 57 (56 leads + center thermal/GND pad = pin 57)

## WICHTIG fuer S3FN8: Reservierte Flash-Pins

Der **FN8** hat **8 MB internal flash** (kein in-package PSRAM). Die SPI0/1-Pins sind **fest mit dem internen Flash verdrahtet** und duerfen NICHT fuer andere Zwecke verwendet werden. Quad SPI (4-line) wird intern genutzt, daher sind folgende Pins gesperrt:

| Pin No. | Pin Name | Funktion am S3FN8 | Status |
|---------|----------|-------------------|--------|
| 26 | GPIO26 | SPICS1 (in-package PSRAM CE# - nicht bestueckt am FN8, aber Pin trotzdem belegt) | Reserviert (P4) - nicht verwenden |
| 27 | GPIO27 | nicht bestueckt am FN8 (PSRAM signal) | Reserviert (P4) - nicht verwenden |
| 28 | SPICS1 | CS1 (PSRAM CE#) - am FN8 ungenutzt | Reserviert |
| 29 | VDD_SPI | Flash supply (1.8 V or 3.3 V) | Power |
| 30 | GPIO28 / SPIHD | Flash HOLD# (Quad SPI) | Reserviert - Flash |
| 31 | GPIO29 / SPIWP | Flash WP# (Quad SPI) | Reserviert - Flash |
| 32 | GPIO30 / SPICS0 | Flash CS# | Reserviert - Flash |
| 33 | GPIO31 / SPICLK | Flash CLK | Reserviert - Flash |
| 34 | GPIO32 / SPIQ | Flash DO (SO/SIO1) | Reserviert - Flash |
| 35 | SPID | Flash DI (SI/SIO0) | Reserviert - Flash |

> **Achtung:** GPIO26-GPIO32 zeigen die Peripheral Pin Assignment-Spalte oft als P4 (not recommended). Am FN8 (Quad SPI flash) sind GPIO33-GPIO37 **frei verwendbar** (nur bei Octal SPI/OPI waeren sie als DQ4-DQ7/DQS belegt - das ist bei FN8 nicht der Fall).

## Vollstaendige Pin-Tabelle (alle 57 Pins)

| Pin# | Name | Type | Default / Boot Function | At Reset | After Reset | Power Rail | Notes |
|------|------|------|------------------------|----------|-------------|------------|-------|
| 1 | LNA_IN | Analog | RF LNA input/output | - | - | - | Antenna feed; only used if no module/PA |
| 2 | VDD3P3 | Power | Analog power input (3.3 V) | - | - | - | Connect to 3V3 + decoupling |
| 3 | VDD3P3 | Power | Analog power input (3.3 V) | - | - | - | Connect to 3V3 + decoupling |
| 4 | CHIP_PU | Analog (Input) | Chip enable | - | - | - | **NEVER float!** High = on, Low = off. RC + button for reset |
| 5 | GPIO0 | IO | GPIO0 / F0 default | WPU, IE | WPU, IE | VDD3P3_RTC | **STRAPPING** (boot mode select). Default WPU. RTC_GPIO0 |
| 6 | GPIO1 | IO | GPIO1 | IE | IE | VDD3P3_RTC | RTC_GPIO1, TOUCH1, ADC1_CH0 |
| 7 | GPIO2 | IO | GPIO2 | IE | IE | VDD3P3_RTC | RTC_GPIO2, TOUCH2, ADC1_CH1 |
| 8 | GPIO3 | IO | GPIO3 | IE | IE | VDD3P3_RTC | **STRAPPING** (JTAG signal source). No internal PU/PD! TOUCH3, ADC1_CH2 |
| 9 | GPIO4 | IO | GPIO4 | - | - | VDD3P3_RTC | RTC_GPIO4, TOUCH4, ADC1_CH3 |
| 10 | GPIO5 | IO | GPIO5 | - | - | VDD3P3_RTC | RTC_GPIO5, TOUCH5, ADC1_CH4 |
| 11 | GPIO6 | IO | GPIO6 | - | - | VDD3P3_RTC | RTC_GPIO6, TOUCH6, ADC1_CH5 |
| 12 | GPIO7 | IO | GPIO7 | - | - | VDD3P3_RTC | RTC_GPIO7, TOUCH7, ADC1_CH6 |
| 13 | GPIO8 | IO | GPIO8 | - | - | VDD3P3_RTC | RTC_GPIO8, TOUCH8, ADC1_CH7. SUBSPICS1 alt |
| 14 | GPIO9 | IO | GPIO9 | - | - | VDD3P3_RTC | RTC_GPIO9, TOUCH9, ADC1_CH8. SUBSPIHD/FSPIHD |
| 15 | GPIO10 | IO | GPIO10 | - | IE | VDD3P3_RTC | RTC_GPIO10, TOUCH10, ADC1_CH9. FSPIIO4, SUBSPICS0, FSPICS0 |
| 16 | GPIO11 | IO | GPIO11 | - | IE | VDD3P3_RTC | RTC_GPIO11, TOUCH11, ADC2_CH0. FSPIIO5, SUBSPID, FSPID |
| 17 | GPIO12 | IO | GPIO12 | - | IE | VDD3P3_RTC | RTC_GPIO12, TOUCH12, ADC2_CH1. FSPIIO6, SUBSPICLK, FSPICLK |
| 18 | GPIO13 | IO | GPIO13 | - | IE | VDD3P3_RTC | RTC_GPIO13, TOUCH13, ADC2_CH2. FSPIIO7, SUBSPIQ, FSPIQ |
| 19 | GPIO14 | IO | GPIO14 | - | IE | VDD3P3_RTC | RTC_GPIO14, TOUCH14, ADC2_CH3. FSPIDQS, SUBSPIWP, FSPIWP |
| 20 | VDD3P3_RTC | Power | RTC + part of Digital power domain | - | - | - | Powers RTC IO (GPIO0-GPIO21). Tie to 3V3 |
| 21 | XTAL_32K_P | IO | GPIO15 / 32K crystal + | - | - | VDD3P3_RTC | RTC_GPIO15, ADC2_CH4. U0RTS. 32 kHz crystal optional |
| 22 | XTAL_32K_N | IO | GPIO16 / 32K crystal - | - | - | VDD3P3_RTC | RTC_GPIO16, ADC2_CH5. U0CTS. 32 kHz crystal optional |
| 23 | GPIO17 | IO | GPIO17 | - | IE | VDD3P3_RTC | RTC_GPIO17, ADC2_CH6. U1TXD. **Drive 10 mA** (lower) |
| 24 | GPIO18 | IO | GPIO18 | - | IE | VDD3P3_RTC | RTC_GPIO18, ADC2_CH7. U1RXD, CLK_OUT3. **Drive 10 mA** |
| 25 | GPIO19 | IO | GPIO19 / **USB_D-** | - | - | VDD3P3_RTC | RTC_GPIO19, ADC2_CH8. USB Serial/JTAG D- by default. **40 mA drive** |
| 26 | GPIO20 | IO | GPIO20 / **USB_D+** | USB_PU | USB_PU | VDD3P3_RTC | RTC_GPIO20, ADC2_CH9. USB Serial/JTAG D+ by default. **40 mA drive** |
| 27 | GPIO21 | IO | GPIO21 | - | - | VDD3P3_RTC | RTC_GPIO21 |
| 28 | SPICS1 | IO | SPICS1 (PSRAM CE# - n/a on FN8) | WPU, IE | WPU, IE | VDD_SPI | **Reserved for flash/PSRAM** - do not use as GPIO on FN8 |
| 29 | VDD_SPI | Power | Flash/PSRAM supply (1.8 V or 3.3 V) | - | - | - | Decoupling required. Default 3.3 V via VDD3P3_RTC |
| 30 | SPIHD | IO | Flash HOLD# (GPIO28) | WPU, IE | WPU, IE | VDD_SPI | **Reserved - internal flash on FN8** |
| 31 | SPIWP | IO | Flash WP# (GPIO29) | WPU, IE | WPU, IE | VDD_SPI | **Reserved - internal flash on FN8** |
| 32 | SPICS0 | IO | Flash CS# (GPIO30) | WPU, IE | WPU, IE | VDD_SPI | **Reserved - internal flash on FN8** |
| 33 | SPICLK | IO | Flash CLK (GPIO31) | WPU, IE | WPU, IE | VDD_SPI | **Reserved - internal flash on FN8** |
| 34 | SPIQ | IO | Flash DO (GPIO32) | WPU, IE | WPU, IE | VDD_SPI | **Reserved - internal flash on FN8** |
| 35 | SPID | IO | Flash DI | WPU, IE | WPU, IE | VDD_SPI | **Reserved - internal flash on FN8** |
| 36 | SPICLK_N | IO | SPI clk negative (GPIO48) | IE | IE | VDD_SPI / VDD3P3_CPU | Octal SPI diff clock. Free on FN8 (Quad flash) but 1.8 V tolerant only if VDD_SPI = 1.8 V |
| 37 | SPICLK_P | IO | SPI clk positive (GPIO47) | IE | IE | VDD_SPI / VDD3P3_CPU | See above |
| 38 | GPIO33 | IO | GPIO33 | - | IE | VDD_SPI / VDD3P3_CPU | Free on FN8 (would be DQ4 in OPI). FSPIHD, SUBSPIHD, SPIIO4 |
| 39 | GPIO34 | IO | GPIO34 | - | IE | VDD_SPI / VDD3P3_CPU | Free on FN8. FSPICS0, SUBSPICS0, SPIIO5 |
| 40 | GPIO35 | IO | GPIO35 | - | IE | VDD_SPI / VDD3P3_CPU | Free on FN8. FSPID, SUBSPID, SPIIO6 |
| 41 | GPIO36 | IO | GPIO36 | - | IE | VDD_SPI / VDD3P3_CPU | Free on FN8. FSPICLK, SUBSPICLK, SPIIO7 |
| 42 | GPIO37 | IO | GPIO37 | - | IE | VDD_SPI / VDD3P3_CPU | Free on FN8. FSPIQ, SUBSPIQ, SPIDQS |
| 43 | GPIO38 | IO | GPIO38 | - | IE | VDD3P3_CPU | FSPIWP, SUBSPIWP |
| 44 | MTCK | IO | GPIO39 / JTAG MTCK | IE (WPU if EFUSE_DIS_PAD_JTAG=0) | IE | VDD3P3_CPU | **JTAG TCK**. CLK_OUT3, SUBSPICS1 |
| 45 | MTDO | IO | GPIO40 / JTAG MTDO | - | IE | VDD3P3_CPU | **JTAG TDO**. CLK_OUT2 |
| 46 | VDD3P3_CPU | Power | Digital power input (3.3 V) | - | - | - | Decoupling required |
| 47 | MTDI | IO | GPIO41 / JTAG MTDI | - | IE | VDD3P3_CPU | **JTAG TDI**. CLK_OUT1 |
| 48 | MTMS | IO | GPIO42 / JTAG MTMS | - | IE | VDD3P3_CPU | **JTAG TMS** |
| 49 | U0TXD | IO | GPIO43 / UART0 TX | WPU, IE | WPU, IE | VDD3P3_CPU | Default ROM log output. CLK_OUT1 |
| 50 | U0RXD | IO | GPIO44 / UART0 RX | WPU, IE | WPU, IE | VDD3P3_CPU | Default ROM log input. CLK_OUT2 |
| 51 | GPIO45 | IO | GPIO45 | WPD, IE | WPD, IE | VDD3P3_CPU | **STRAPPING** (VDD_SPI voltage). Default WPD |
| 52 | GPIO46 | IO | GPIO46 | WPD, IE | WPD, IE | VDD3P3_CPU | **STRAPPING** (boot mode + ROM print). Default WPD |
| 53 | XTAL_N | Analog | Main crystal - | - | - | - | 40 MHz crystal. ESP32-S3 **cannot operate without main XTAL** |
| 54 | XTAL_P | Analog | Main crystal + | - | - | - | 40 MHz crystal |
| 55 | VDDA | Power | Analog power input (3.3 V) | - | - | - | RF analog supply. Decoupling required |
| 56 | VDDA | Power | Analog power input (3.3 V) | - | - | - | RF analog supply. Decoupling required |
| 57 | GND | Power | External ground (center pad) | - | - | - | Solder thermal pad + vias to GND plane |

### Legende At Reset / After Reset

- **IE** - input enabled
- **WPU** - internal weak pull-up enabled (~45 kOhm)
- **WPD** - internal weak pull-down enabled (~45 kOhm)
- **USB_PU** - USB pull-up enabled
- **-** - no internal pull configured (Hi-Z)

### Drive Strengths (Standard)

- GPIO17, GPIO18: **10 mA**
- GPIO19, GPIO20 (USB): **40 mA**
- All other pins: **20 mA**

## IO MUX Funktionen (Direct Routing)

Folgende Signale koennen via IO MUX **direkt** (ohne GPIO Matrix Latency) an Pins gefuehrt werden. F0 = default, F1 = GPIO function, F2-F4 = alternate.

| Pin# | GPIO | F0 (default) | F1 | F2 | F3 | F4 |
|------|------|--------------|----|----|----|-----|
| 5 | GPIO0 | GPIO0 | GPIO0 | - | - | - |
| 6 | GPIO1 | GPIO1 | GPIO1 | - | - | - |
| 7 | GPIO2 | GPIO2 | GPIO2 | - | - | - |
| 8 | GPIO3 | GPIO3 | GPIO3 | - | - | - |
| 9 | GPIO4 | GPIO4 | GPIO4 | - | - | - |
| 10 | GPIO5 | GPIO5 | GPIO5 | - | - | - |
| 11 | GPIO6 | GPIO6 | GPIO6 | - | - | - |
| 12 | GPIO7 | GPIO7 | GPIO7 | - | - | - |
| 13 | GPIO8 | GPIO8 | GPIO8 | - | SUBSPICS1 | - |
| 14 | GPIO9 | GPIO9 | GPIO9 | - | SUBSPIHD | FSPIHD |
| 15 | GPIO10 | GPIO10 | GPIO10 | FSPIIO4 | SUBSPICS0 | FSPICS0 |
| 16 | GPIO11 | GPIO11 | GPIO11 | FSPIIO5 | SUBSPID | FSPID |
| 17 | GPIO12 | GPIO12 | GPIO12 | FSPIIO6 | SUBSPICLK | FSPICLK |
| 18 | GPIO13 | GPIO13 | GPIO13 | FSPIIO7 | SUBSPIQ | FSPIQ |
| 19 | GPIO14 | GPIO14 | GPIO14 | FSPIDQS | SUBSPIWP | FSPIWP |
| 21 | GPIO15 | GPIO15 | GPIO15 | U0RTS | - | - |
| 22 | GPIO16 | GPIO16 | GPIO16 | U0CTS | - | - |
| 23 | GPIO17 | GPIO17 | GPIO17 | U1TXD | - | - |
| 24 | GPIO18 | GPIO18 | GPIO18 | U1RXD | CLK_OUT3 | - |
| 25 | GPIO19 | GPIO19 | GPIO19 | U1RTS | CLK_OUT2 | - |
| 26 | GPIO20 | GPIO20 | GPIO20 | U1CTS | CLK_OUT1 | - |
| 27 | GPIO21 | GPIO21 | GPIO21 | - | - | - |
| 28 | GPIO26 | **SPICS1** | GPIO26 | - | - | - |
| 30 | GPIO27 | **SPIHD** | GPIO27 | - | - | - |
| 31 | GPIO28 | **SPIWP** | GPIO28 | - | - | - |
| 32 | GPIO29 | **SPICS0** | GPIO29 | - | - | - |
| 33 | GPIO30 | **SPICLK** | GPIO30 | - | - | - |
| 34 | GPIO31 | **SPIQ** | GPIO31 | - | - | - |
| 35 | GPIO32 | **SPID** | GPIO32 | - | - | - |
| 36 | GPIO48 | SPICLK_N_DIFF | GPIO48 | SUBSPICLK_N_DIFF | - | - |
| 37 | GPIO47 | SPICLK_P_DIFF | GPIO47 | SUBSPICLK_P_DIFF | - | - |
| 38 | GPIO33 | GPIO33 | GPIO33 | FSPIHD | SUBSPIHD | SPIIO4 |
| 39 | GPIO34 | GPIO34 | GPIO34 | FSPICS0 | SUBSPICS0 | SPIIO5 |
| 40 | GPIO35 | GPIO35 | GPIO35 | FSPID | SUBSPID | SPIIO6 |
| 41 | GPIO36 | GPIO36 | GPIO36 | FSPICLK | SUBSPICLK | SPIIO7 |
| 42 | GPIO37 | GPIO37 | GPIO37 | FSPIQ | SUBSPIQ | SPIDQS |
| 43 | GPIO38 | GPIO38 | GPIO38 | FSPIWP | SUBSPIWP | - |
| 44 | GPIO39 | **MTCK** | GPIO39 | CLK_OUT3 | SUBSPICS1 | - |
| 45 | GPIO40 | **MTDO** | GPIO40 | CLK_OUT2 | - | - |
| 47 | GPIO41 | **MTDI** | GPIO41 | CLK_OUT1 | - | - |
| 48 | GPIO42 | **MTMS** | GPIO42 | - | - | - |
| 49 | GPIO43 | **U0TXD** | GPIO43 | CLK_OUT1 | - | - |
| 50 | GPIO44 | **U0RXD** | GPIO44 | CLK_OUT2 | - | - |
| 51 | GPIO45 | GPIO45 | GPIO45 | - | - | - |
| 52 | GPIO46 | GPIO46 | GPIO46 | - | - | - |

Type-Codes: I = input, O = output, T = high impedance, I1 = forced 1 input when other F selected, I0 = forced 0 input.

## Peripheral Pin Assignment (Priorities)

Aus Tabelle 2-9 (Peripheral Pin Assignment). **Priority levels:**

- **P1 (Priority 1)** - fixed pins via IO MUX or RTC IO MUX. Highest performance.
- **P2 (Priority 2)** - any GPIO via GPIO Matrix, no restrictions.
- **P3 (Priority 3)** - GPIO via GPIO Matrix, but conflicts possible (strapping, USB, JTAG, UART0, 8-line SPI).
- **P4 (Priority 4)** - already used by in-package flash/PSRAM. **Avoid on FN8: GPIO26-GPIO32.**

### Fixed P1 Peripheral Assignments (IO MUX)

| Peripheral | Signal | Pin (GPIO) |
|------------|--------|------------|
| USB Serial/JTAG | USB_D- | GPIO19 (P1) |
| USB Serial/JTAG | USB_D+ | GPIO20 (P1) |
| Full-speed USB OTG | USB_D- | GPIO19 (P2) |
| Full-speed USB OTG | USB_D+ | GPIO20 (P2) |
| JTAG (pads) | MTCK | GPIO39 (P1) |
| JTAG (pads) | MTDO | GPIO40 (P1) |
| JTAG (pads) | MTDI | GPIO41 (P1) |
| JTAG (pads) | MTMS | GPIO42 (P1) |
| UART0 | U0TXD | GPIO43 (P1) |
| UART0 | U0RXD | GPIO44 (P1) |
| UART1 | U1TXD | GPIO17 (P1) |
| UART1 | U1RXD | GPIO18 (P1) |
| UART1 | U1RTS | GPIO19 (P1) |
| UART1 | U1CTS | GPIO20 (P1) |
| SPI2 (FSPI) recommended | FSPICLK | GPIO12 (P1) |
| SPI2 (FSPI) recommended | FSPIQ | GPIO13 (P1) |
| SPI2 (FSPI) recommended | FSPID | GPIO11 (P1) |
| SPI2 (FSPI) recommended | FSPIHD | GPIO9 (P1) |
| SPI2 (FSPI) recommended | FSPIWP | GPIO14 (P1) |
| SPI2 (FSPI) recommended | FSPICS0 | GPIO10 (P1) |
| ADC1 | CH0-CH9 | GPIO1-GPIO10 |
| ADC2 | CH0-CH9 | GPIO11-GPIO20 |
| Touch Sensor | T1-T14 | GPIO1-GPIO14 |

UART2, I2C, I2S, LED PWM, MCPWM, RMT, PCNT, TWAI, LCD/Camera, SD/MMC, SPI3: alle haben **keine P1-Pins** und werden via GPIO Matrix auf beliebige verfuegbare GPIOs gelegt.

## Power-Up Glitches (Beachten!)

Folgende Pins haben kurze (~60 us) Pegelstoerungen waehrend Power-up:

- **Low-level glitch** (60 us): GPIO1-GPIO14, XTAL_32K_P, XTAL_32K_N, GPIO17, GPIO18
- **GPIO18**: low + high glitch
- **GPIO19**: low + high glitch (high glitch up to 3.2 ms total!)
- **GPIO20**: pull-down + high glitch (high glitch up to 2 ms total!)

> **Konsequenz:** Diese Pins niemals fuer Signale verwenden, die einen sauberen Pegel waehrend Boot brauchen (z.B. Reset-Eingaenge fuer Periphere, Enable-Pins fuer MOSFETs/Boost-Regler).

## Empfohlene "frei verwendbare" GPIOs auf S3FN8

Diese GPIOs sind am sichersten fuer eigene IO-Funktionen (kein Strapping, keine USB-Default-Belegung, kein UART0, kein JTAG, kein Flash):

**Sehr sicher:** GPIO4, GPIO5, GPIO6, GPIO7, GPIO8, GPIO9, GPIO10, GPIO11, GPIO12, GPIO13, GPIO14, GPIO15, GPIO16, GPIO17, GPIO18, GPIO21

**Mit Vorsicht (alternative Funktion mit Default):**
- GPIO1, GPIO2 - kein Strapping, aber Touch/ADC1 Default
- GPIO33-GPIO38 - frei am FN8 (waeren nur bei Octal-Flash belegt)
- GPIO47, GPIO48 - SPI diff clock, frei am FN8

**Vorsicht / nicht empfohlen:**
- GPIO0, GPIO3, GPIO45, GPIO46 - **Strapping pins** (siehe `04_strapping_pins.md`)
- GPIO19, GPIO20 - **USB D-/D+** (nur nutzen wenn USB nicht gebraucht)
- GPIO39-GPIO42 - **JTAG** (nutzbar, aber dann kein JTAG-Debug mehr)
- GPIO43, GPIO44 - **UART0** (Debug-Log)
- GPIO26-GPIO32 - **Internal flash** (NIEMALS verwenden auf FN8)
