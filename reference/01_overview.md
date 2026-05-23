> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 2-5 (Product Overview, Features) and 6 (Applications)

# ESP32-S3 Übersicht

ESP32-S3 ist ein Low-Power MCU-basierter SoC mit integriertem 2.4 GHz Wi-Fi und Bluetooth Low Energy (BLE). Er besteht aus einem High-Performance Dual-Core Xtensa 32-bit LX7 Mikroprozessor, einem ULP-Coprozessor, Wi-Fi-Baseband, Bluetooth LE-Baseband, RF-Modul und zahlreichen Peripherals.

---

## Wi-Fi

| Feature | Details |
|---|---|
| Standard | IEEE 802.11b/g/n |
| Bandbreite | 20 MHz und 40 MHz im 2.4 GHz Band |
| Modus | 1T1R, Datenrate bis 150 Mbps |
| QoS | Wi-Fi Multimedia (WMM) |
| Aggregation | TX/RX A-MPDU, TX/RX A-MSDU |
| ACK | Immediate Block ACK |
| Fragmentierung | Fragmentation and defragmentation |
| Beacon | Automatic Beacon monitoring (hardware TSF) |
| Interfaces | 4 virtuelle Wi-Fi Interfaces |
| Modi | Infrastructure BSS in Station, SoftAP, oder Station + SoftAP (Anm.: bei Scan im Station-Mode wechselt der SoftAP-Channel mit) |
| Antenne | Antenna diversity |
| Sonstiges | 802.11mc FTM |

## Bluetooth

| Feature | Details |
|---|---|
| Stack | Bluetooth LE: Bluetooth 5, Bluetooth Mesh |
| TX Power | High-power mode bis 20 dBm |
| Speed | 125 Kbps, 500 Kbps, 1 Mbps, 2 Mbps |
| Advertising | LE Advertising Extensions, Multiple Advertising Sets |
| Channel | LE Channel Selection Algorithm #2 |
| Koexistenz | Internal co-existence mechanism Wi-Fi/BT (gemeinsame Antenne) |

## CPU und Memory

| Feature | Details |
|---|---|
| CPU | Xtensa dual-core 32-bit LX7 |
| Clock | bis 240 MHz |
| CoreMark | 1329.92 (2 cores @ 240 MHz); 5.54 CoreMark/MHz |
| Pipeline | Five-stage |
| Datenbus | 128-bit mit dedizierten SIMD-Instruktionen |
| FPU | Single precision floating point unit |
| ULP | ULP-RISC-V + ULP-FSM coprocessors |
| DMA | General DMA controller, 5 TX + 5 RX Channels |
| Cache | L1 cache |
| ROM | 384 KB |
| SRAM | 512 KB |
| RTC SRAM | 16 KB |
| eFuse | 4096-bit (bis 1792 bits für User) |
| SPI | SPI, Dual SPI, Quad SPI, Octal SPI, QPI, OPI |
| Flash | Controller mit Cache; In-Circuit Programming (ICP) |

## Peripherals

| Kategorie | Details |
|---|---|
| GPIOs | 45 programmierbar (4 Strapping GPIOs) |
| In-Package Memory | 6 GPIOs reserviert für Flash *oder* PSRAM; 7 GPIOs wenn beides integriert |
| UART | 3 Interfaces |
| I2C | 2 Interfaces |
| I2S | 2 Interfaces |
| LCD | 1 LCD Interface |
| Camera | 8-bit ~ 16-bit DVP |
| SPI (Memory) | 2 Ports für Flash/RAM |
| SPI (GP) | 2 General-Purpose Ports |
| TWAI | Kompatibel zu ISO 11898-1 (CAN 2.0) |
| USB | Full-speed USB OTG + USB Serial/JTAG |
| SD/MMC | Host Controller, 2 Slots |
| LED PWM | bis 8 Channels |
| MCPWM | 2 Motor Control PWM |
| RMT | TX/RX |
| PCNT | Pulse Count Controller |
| ADC | 2× 12-bit SAR ADC, bis 20 Channels |
| Temp Sensor | ja |
| Touch | 14 kapazitive Touch-Sensing-IOs |
| Timer | 4× 54-bit GP-Timer, 1× 52-bit System Timer, 3× Watchdog |

## Power Management

| Feature | Details |
|---|---|
| Control | Fine-resolution: Clock-Frequenz, Duty Cycle, Wi-Fi Modes, Komponenten einzeln |
| Power Modes | Active, Modem-sleep, Light-sleep, Deep-sleep |
| Deep-sleep | 7 µA |
| RTC Memory | bleibt in Deep-sleep aktiv |

## Security

| Feature | Details |
|---|---|
| Secure Boot | Permission Control auf internen + externen Memory |
| Flash Encryption | Memory Encryption/Decryption |
| SHA | FIPS PUB 180-4 |
| AES | FIPS PUB 197 |
| RSA | ja |
| HMAC | ja |
| RSA_DS | Digital Signature Peripheral |
| RNG | Random Number Generator |

## RF Modul

| Feature | Details |
|---|---|
| Components | Antenna Switches, RF Balun, PA, LNA |
| TX 802.11b | bis +21 dBm |
| TX 802.11n | bis +19.5 dBm |
| BLE RX Sens | bis -104.5 dBm (125 Kbps) |

---

## Block Diagram (funktionale Architektur)

Der SoC ist in folgende Hauptblöcke gegliedert:

### CPU and Memory
- Xtensa Dual-core 32-bit LX7 Mikroprozessor
- Cache, SRAM, ROM
- JTAG
- Interrupt Matrix

### RF
- 2.4 GHz Balun + Switch
- 2.4 GHz Receiver / Transmitter
- RF Synthesizer
- External Main Clock
- Fast RC Oscillator
- Phase Lock Loop (PLL)

### Wireless Digital Circuits
- Wi-Fi MAC + Wi-Fi Baseband
- Bluetooth LE Link Controller + Baseband

### Peripherals
GDMA, System Timer, General-purpose Timers, GPIO, RTC GPIO, DIG ADC, RTC ADC, SD/MMC Host, Pulse Counter, World Controller, USB Serial/JTAG, eFuse Controller, SPI0/1, SPI2/3, I2S, Main System Watchdog Timers, RTC Watchdog Timer, USB OTG, TWAI, I2C, UART, LED PWM, MCPWM, Super Watchdog, Touch Sensor, RMT, LCD Interface, Camera Interface, RTC I2C, Temperature Sensor.

### Security Block
SHA, RSA, AES, RNG, HMAC, RSA_DS, Secure Boot, Permission Control, Flash Encryption.

### RTC (Low-Power Domain)
RTC Memory, PMU, ULP Coprocessor — diese Komponenten arbeiten im Deep-sleep weiter.

> **Hinweis:** Im Block Diagram unterscheidet das Datasheet zwischen "Normal" Komponenten und "Low power consumption components capable of working in Deep-sleep mode" (graphisch markiert). Letztere = RTC-Domäne (RTC Memory, PMU, ULP, RTC Watchdog, RTC GPIO/ADC/I2C, Touch, Temp Sensor, Super Watchdog).

---

## Anwendungen (laut Datasheet)

Smart Home, Industrial Automation, Health Care, Consumer Electronics, Smart Agriculture, POS Machines, Service Robot, Audio Devices, Generic Low-power IoT Sensor Hubs, Generic Low-power IoT Data Loggers, Cameras for Video Streaming, USB Devices, Speech Recognition, Image Recognition, Wi-Fi + Bluetooth Networking Card, Touch and Proximity Sensing.
