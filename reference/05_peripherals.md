# ESP32-S3FN8 Peripheral Pin Assignments

> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 20-31 (IO MUX Functions, RTC Functions, Analog Functions, Peripheral Pin Assignment, Pin Mapping Chip-Flash)

## USB

Zwei USB-Controller, **beide nutzen die gleichen Pins GPIO19/GPIO20**:

| Function | D- | D+ | Notes |
|----------|------|------|-------|
| **USB Serial/JTAG Controller** (P1, IO MUX direkt) | **GPIO19** | **GPIO20** | Internal PHY. Default fuer ROM-Bootloader + Flashing + JTAG-Debug. USB_PU aktiv per Default. |
| **Full-speed USB OTG** | GPIO19 | GPIO20 | Internal PHY, gleiche Pins. Muss per Code/Config umgeschaltet werden. |

> **Beschaltung:**
> - D- (GPIO19): direkt an USB-Connector Pin 2 (oder 3 bei Type-C)
> - D+ (GPIO20): direkt an USB-Connector Pin 3 (oder 2)
> - Keine Serien-Widerstaende noetig (interner PHY), aber 22 Ohm Serie wird oft empfohlen fuer EMI
> - 1.5 kOhm Pull-Up auf D+ ist intern via USB_PU - kein externer noetig
> - D-/D+ Glitches beim Power-Up: GPIO19 hat low+high glitch (gesamt bis 3.2 ms!), GPIO20 hat PD+high glitch (gesamt bis 2 ms). Host-side: USB-Connect erst nach ~10 ms ueblich.

> **Swap:** Per `USB_SERIAL_JTAG_EXCHG_PINS` Register kann USB_D+/USB_D- getauscht werden - hilft bei PCB-Routing.

## SPI Flash (intern auf S3FN8)

Der ESP32-S3FN8 hat **8 MB internal flash** (kein in-package PSRAM). Das Flash haengt an SPI0/1 ueber Quad SPI Mode. Die Pins sind **fest belegt** und duerfen NICHT als GPIO verwendet werden:

| Pin# | Signal Name | Connected to flash | Mode = Quad SPI (FN8) |
|------|-------------|-------------------|----------------------|
| 28 | SPICS1 | (PSRAM CE# - n/a) | unused on FN8 |
| 29 | VDD_SPI | Flash supply | 3.3 V |
| 30 | SPIHD (GPIO27) | HOLD# (SIO3) | **Reserved** |
| 31 | SPIWP (GPIO28) | WP# (SIO2) | **Reserved** |
| 32 | SPICS0 (GPIO29) | CS# | **Reserved** |
| 33 | SPICLK (GPIO30) | CLK | **Reserved** |
| 34 | SPIQ (GPIO31) | DO (SO/SIO1) | **Reserved** |
| 35 | SPID (GPIO32) | DI (SI/SIO0) | **Reserved** |

> Auf S3FN8 sind GPIO33-GPIO37 **frei verwendbar** als normale IOs (sie waeren nur bei Octal-SPI/OPI als DQ4-DQ7+DQS belegt, was am FN8 nicht der Fall ist).

### VDD_SPI Konfiguration

| Source | Voltage | EFUSE_VDD_SPI_FORCE | GPIO45 | Notes |
|--------|---------|---------------------|--------|-------|
| VDD3P3_RTC via R_SPI (default) | **3.3 V** | 0 | 0 | Korrekt fuer FN8 |
| Internal Flash Voltage Regulator | 1.8 V | 0 | 1 | Nicht fuer FN8 verwenden |

VDD_SPI braucht Decoupling: 1 uF + 100 nF parallel direkt am Pin 29.

## SPI2 (FSPI) - User SPI Master, P1 (IO MUX)

SPI2 ist der schnelle General-Purpose SPI. Empfohlene Pin-Belegung fuer **maximale Geschwindigkeit** (IO MUX direkt):

| Signal | Pin (recommended P1) | Alternative |
|--------|---------------------|-------------|
| FSPICLK | GPIO12 | beliebig via GPIO Matrix |
| FSPIQ (MISO) | GPIO13 | beliebig |
| FSPID (MOSI) | GPIO11 | beliebig |
| FSPIHD | GPIO9 | beliebig |
| FSPIWP | GPIO14 | beliebig |
| FSPICS0 | GPIO10 | beliebig |

Fuer Octal-SPI auf SPI2:
- FSPIIO4 - GPIO10, FSPIIO5 - GPIO11, FSPIIO6 - GPIO12, FSPIIO7 - GPIO13, FSPIDQS - GPIO14

## SPI3 - User SPI Master

SPI3 hat **keine P1-Pins**. Wird ueber GPIO Matrix auf beliebige verfuegbare GPIOs gelegt (mit GPIO Matrix Latency).

## SUBSPI

Sekundaerer SPI-Block (powered by VDD3P3_RTC oder VDD3P3_CPU). Empfohlene Pins:
- SUBSPICLK - GPIO12, SUBSPIQ - GPIO13, SUBSPID - GPIO11, SUBSPIHD - GPIO9, SUBSPIWP - GPIO14, SUBSPICS0 - GPIO10, SUBSPICS1 - GPIO8

## UART

| UART | Default Pins (P1) | Notes |
|------|-------------------|-------|
| **UART0** | TX: **GPIO43**, RX: **GPIO44** | ROM-Bootloader Log Default. Default WPU. |
| **UART1** | TX: GPIO17, RX: GPIO18, RTS: GPIO19, CTS: GPIO20 | Default; bei USB-Nutzung anderswo routen. |
| **UART2** | Keine P1-Pins | Beliebige GPIOs via GPIO Matrix. |

## I2C

I2C0 + I2C1 haben **keine P1-Pins**. Frei waehlbar via GPIO Matrix auf jeden GPIO.

> Faustregel: niedrige GPIOs (4-18) nehmen, die kein Strapping/USB/UART0/Flash sind. Beispiel: SCL=GPIO5, SDA=GPIO4.

### RTC I2C (low-power, im Deep-Sleep nutzbar)

Spezielle RTC-Funktion via RTC IO MUX:

| RTC I2C Signal | Pin (RTC_GPIO -> Pin#) |
|----------------|------------------------|
| sar_i2c_scl_0 | RTC_GPIO0 = GPIO0 (Pin 5) - Achtung: STRAPPING! |
| sar_i2c_sda_0 | RTC_GPIO1 = GPIO1 (Pin 6) |
| sar_i2c_scl_1 | RTC_GPIO2 = GPIO2 (Pin 7) |
| sar_i2c_sda_1 | RTC_GPIO3 = GPIO3 (Pin 8) - Achtung: STRAPPING! |

## ADC

Zwei SAR ADCs, je 12 bit, 10 Kanaele pro ADC. **Achtung:** ADC2 wird vom WiFi/BT-Stack genutzt - bei aktivem Funk nur ADC1 zuverlaessig.

### ADC1 (alle auf GPIO1-GPIO10)

| Channel | GPIO | Pin# |
|---------|------|------|
| ADC1_CH0 | GPIO1 | 6 |
| ADC1_CH1 | GPIO2 | 7 |
| ADC1_CH2 | GPIO3 | 8 (STRAPPING!) |
| ADC1_CH3 | GPIO4 | 9 |
| ADC1_CH4 | GPIO5 | 10 |
| ADC1_CH5 | GPIO6 | 11 |
| ADC1_CH6 | GPIO7 | 12 |
| ADC1_CH7 | GPIO8 | 13 |
| ADC1_CH8 | GPIO9 | 14 |
| ADC1_CH9 | GPIO10 | 15 |

### ADC2 (alle auf GPIO11-GPIO20)

| Channel | GPIO | Pin# | Notes |
|---------|------|------|-------|
| ADC2_CH0 | GPIO11 | 16 | |
| ADC2_CH1 | GPIO12 | 17 | |
| ADC2_CH2 | GPIO13 | 18 | |
| ADC2_CH3 | GPIO14 | 19 | |
| ADC2_CH4 | GPIO15 (XTAL_32K_P) | 21 | konflikt mit 32K-XTAL |
| ADC2_CH5 | GPIO16 (XTAL_32K_N) | 22 | konflikt mit 32K-XTAL |
| ADC2_CH6 | GPIO17 | 23 | |
| ADC2_CH7 | GPIO18 | 24 | |
| ADC2_CH8 | GPIO19 | 25 | konflikt mit USB_D- |
| ADC2_CH9 | GPIO20 | 26 | konflikt mit USB_D+ |

## Touch Sensor

14 kapazitive Touch-Kanaele auf GPIO1-GPIO14:

| Touch | GPIO | Pin# |
|-------|------|------|
| TOUCH1 | GPIO1 | 6 |
| TOUCH2 | GPIO2 | 7 |
| TOUCH3 | GPIO3 | 8 (STRAPPING!) |
| TOUCH4 | GPIO4 | 9 |
| TOUCH5 | GPIO5 | 10 |
| TOUCH6 | GPIO6 | 11 |
| TOUCH7 | GPIO7 | 12 |
| TOUCH8 | GPIO8 | 13 |
| TOUCH9 | GPIO9 | 14 |
| TOUCH10 | GPIO10 | 15 |
| TOUCH11 | GPIO11 | 16 |
| TOUCH12 | GPIO12 | 17 |
| TOUCH13 | GPIO13 | 18 |
| TOUCH14 | GPIO14 | 19 |

> **Layout-Tipp:** Touch-Pads brauchen kurze Leitungen mit definierter Stray-Capacitance (1-50 pF empfohlen). Keine GND-Plane direkt unter dem Pad - lieber Hatch-Fill.

## JTAG

Zwei Optionen, gesteuert ueber GPIO3 (Strapping) und eFuses:

### Option A: USB Serial/JTAG Controller (default)

Nutzt **GPIO19 (USB_D-)** und **GPIO20 (USB_D+)** als JTAG-Interface ueber USB.
- Kein extra Adapter noetig (USB direkt am PC)
- GPIO39-GPIO42 sind dann als **normale GPIOs** frei verwendbar

### Option B: JTAG via Pads (Hardware-Pin JTAG)

Aktiviert wenn `EFUSE_STRAP_JTAG_SEL = 1` und GPIO3 = 0 beim Reset (oder `EFUSE_DIS_USB_JTAG = 1`):

| Signal | GPIO | Pin# |
|--------|------|------|
| MTCK (TCK) | GPIO39 | 44 |
| MTDO (TDO) | GPIO40 | 45 |
| MTDI (TDI) | GPIO41 | 47 |
| MTMS (TMS) | GPIO42 | 48 |

> **Empfehlung:** Bei erstem Board USB-JTAG nutzen (Option A) - spart 4 Pins und einen Adapter. Pads-JTAG nur, wenn USB nicht verfuegbar ist.

## RTC GPIOs (im Deep-Sleep nutzbar)

22 GPIOs koennen als RTC_GPIO genutzt werden (powered by VDD3P3_RTC, ULP-Coprozessor zugaenglich):

| RTC_GPIO# | GPIO | Pin# |
|-----------|------|------|
| RTC_GPIO0 | GPIO0 | 5 |
| RTC_GPIO1 | GPIO1 | 6 |
| RTC_GPIO2 | GPIO2 | 7 |
| RTC_GPIO3 | GPIO3 | 8 |
| RTC_GPIO4 | GPIO4 | 9 |
| RTC_GPIO5 | GPIO5 | 10 |
| RTC_GPIO6 | GPIO6 | 11 |
| RTC_GPIO7 | GPIO7 | 12 |
| RTC_GPIO8 | GPIO8 | 13 |
| RTC_GPIO9 | GPIO9 | 14 |
| RTC_GPIO10 | GPIO10 | 15 |
| RTC_GPIO11 | GPIO11 | 16 |
| RTC_GPIO12 | GPIO12 | 17 |
| RTC_GPIO13 | GPIO13 | 18 |
| RTC_GPIO14 | GPIO14 | 19 |
| RTC_GPIO15 | XTAL_32K_P (GPIO15) | 21 |
| RTC_GPIO16 | XTAL_32K_N (GPIO16) | 22 |
| RTC_GPIO17 | GPIO17 | 23 |
| RTC_GPIO18 | GPIO18 | 24 |
| RTC_GPIO19 | GPIO19 | 25 |
| RTC_GPIO20 | GPIO20 | 26 |
| RTC_GPIO21 | GPIO21 | 27 |

> Nur diese Pins koennen den Chip aus **Deep-Sleep** wecken (per Pegel-Wechsel) oder vom ULP-Coprozessor gelesen/getrieben werden. GPIO22-GPIO48 sind im Deep-Sleep tot.

## 32 kHz Crystal (optional)

| Signal | GPIO | Pin# |
|--------|------|------|
| XTAL_32K_P | GPIO15 | 21 |
| XTAL_32K_N | GPIO16 | 22 |

Wird benoetigt fuer praezisen RTC-Takt waehrend Deep-Sleep. Sonst nutzt der Chip den internen RC-Oszillator (~136 kHz, ungenau).

## Andere Peripherie (alle ohne P1-Pins, frei via GPIO Matrix)

- **I2S0, I2S1** - alle GPIOs moeglich
- **LED PWM (LEDC)** - alle GPIOs
- **MCPWM** - alle GPIOs
- **TWAI (CAN bus)** - alle GPIOs
- **LCD/Camera Interface** - alle GPIOs (16/24-bit parallel)
- **SD/MMC Host** - alle GPIOs (1/4/8-bit)
- **RMT** - alle GPIOs (z.B. fuer WS2812 LEDs)
- **PCNT (Pulse Counter)** - alle GPIOs

## Clock Output (CLK_OUT)

Drei Clock-Output-Funktionen via IO MUX direkt:

| Signal | Available on Pin |
|--------|------------------|
| CLK_OUT1 | GPIO20, GPIO41, GPIO43 |
| CLK_OUT2 | GPIO19, GPIO40, GPIO44 |
| CLK_OUT3 | GPIO18, GPIO39 |

## Main Crystal (XTAL)

| Pin | Function |
|-----|----------|
| XTAL_P (54) | Main crystal + |
| XTAL_N (53) | Main crystal - |

> **WICHTIG:** Datasheet sagt explizit: "ESP32-S3 is unable to operate without an external main crystal clock." Frequenz: **40 MHz** (Standard). Load caps typ. 8-22 pF, abhaengig vom Crystal-Datasheet.

## Zusammenfassung "fixed pins" auf S3FN8

Diese Pins haben **feste Funktion**, die schwer/nicht umroutbar ist:

| Funktion | Pin(s) | Anmerkung |
|----------|--------|-----------|
| Power supply | 2, 3, 20, 29, 46, 55, 56, 57 (GND) | nicht aenderbar |
| Crystal | 53, 54 | nicht aenderbar |
| RF | 1 (LNA_IN) | nur fuer Antenne |
| Chip enable | 4 (CHIP_PU) | nicht aenderbar |
| Internal flash | 28, 30-35 (GPIO26-32) | NICHT verwenden! |
| USB (default) | 19, 20 (GPIO19, GPIO20) | nur frei, wenn USB ungenutzt |
| UART0 (default log) | 43, 44 (GPIO43, GPIO44) | freigeben moeglich, aber ROM-Log geht verloren |
| Strapping | GPIO0, GPIO3, GPIO45, GPIO46 | nutzbar nach Boot, aber Beschaltung kritisch (siehe `04_strapping_pins.md`) |
