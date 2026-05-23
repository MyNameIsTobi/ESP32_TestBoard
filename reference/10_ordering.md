> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 77, 82-87 (+ Querverweise zur Variantentabelle)

# 10 - Ordering Information (ESP32-S3 Serie)

## Unser Ziel-Chip

> **ESP32-S3FN8** ist das Bauteil fur dieses Projekt.

- Variant: ESP32-S3 mit **integriertem 8 MB Flash (Quad SPI, 1.8/3.3 V)**, **kein PSRAM**
- Package: QFN56, 7 x 7 mm, 0.40 mm Pitch (siehe `09_package.md`)
- Temperatur-Range: Standard ESP32-S3 Range (siehe Datasheet Tabelle 1-1)

## Full Ordering Code

Espressif hat in v2.1 den Header von "Ordering Code" zu **"Part Number"**
umbenannt (Revision History v2.1, 2025-11-28). Beide Begriffe meinen dasselbe.

**Bestellnummer (Part Number) fur unser Projekt:**

```
ESP32-S3FN8
```

Diese Part Number bestellt man direkt so beim Distributor (Mouser, Digi-Key,
LCSC, etc.). Es gibt **kein Suffix fur Trockenbeutel / Tape-Reel im Datasheet**;
die Verpackungsoption wird beim Distributor uber Order-MOQ / Packing-Option
gewahlt (siehe Abschnitt "Tape & Reel" unten).

## Part-Number-Decoder ("F"/"H"/"R"/"V" Letters)

Aus der ESP32-S3 Serien-Vergleichstabelle (Tabelle 1-1) lasst sich folgendes
Schema ableiten:

```
ESP32 - S3 [F|H] [N|R] <Groesse> [V] [<Suffix>]
   |     |   |     |        |       |       |
   |     |   |     |        |       |       +-- Sonst-Suffix (Revision/Variante,
   |     |   |     |        |       |           z.B. "2" in FN4R2)
   |     |   |     |        |       +-- "V" = 1.8 V Flash/PSRAM Interface
   |     |   |     |        |           (sonst 3.3 V)
   |     |   |     |        +-- Speichergroesse in MB (4 / 8 / 16)
   |     |   |     +-- Speichertyp:
   |     |   |          N = NOR Flash (in-package)
   |     |   |          R = PSRAM (in-package), oft kombiniert
   |     |   +-- Temperatur / Process:
   |     |        F = Standard (kommerzieller Range)
   |     |        H = High temp / Industrial Range
   |     +-- SoC Familie (S3)
   +-- ESP32 Familie
```

### Bekannte ESP32-S3 Varianten (Auszug)

| Part Number       | In-Package Flash | In-Package PSRAM | Hinweis                                  |
|-------------------|------------------|------------------|------------------------------------------|
| ESP32-S3          | -                | -                | nackt, externer Flash                    |
| **ESP32-S3FN8**   | **8 MB Quad NOR**| **-**            | **<- unser Chip**                        |
| ESP32-S3FH4R2     | 4 MB Quad NOR    | 2 MB Quad PSRAM  | abweichendes EPAD (5.7 mm, siehe 09_package.md) |
| ESP32-S3FN4R2     | 4 MB Quad NOR    | 2 MB Quad PSRAM  | PSRAM-Variante                           |
| ESP32-S3R2        | -                | 2 MB Quad PSRAM  | EOL seit v2.1 (NRND)                     |
| ESP32-S3RH2       | -                | 2 MB Quad PSRAM  | Ersatz fur R2 (industrial)               |
| ESP32-S3R8        | -                | 8 MB Octal PSRAM | aktiv                                    |
| ESP32-S3R8V       | -                | 8 MB Octal PSRAM | 1.8 V, **EOL seit v2.0**                 |
| ESP32-S3R16V      | -                | 16 MB Octal PSRAM (1.8 V) | seit v1.8 (2023-11)             |

> Genaue Tabelle und Feature-Matrix: Datasheet Kapitel 1, **Table 1-1 ESP32-S3
> Series Comparison**.

## Marking-Decode (was steht auf dem Chip-Top?)

Das Datasheet zeigt im Figure 7-1 nur einen Platzhalter-String
("56L SLP (7x7mm)"). Die **echte Marking-Decodierung** (Lot-Code, Date-Code,
ROM-Version, Part-Number-Schreibweise) wird im **separaten Dokument
"ESP32-S3 Chip Packaging Information"** dokumentiert (Link in Kapitel 7).

Was wir aus dem Datasheet sicher wissen:

- **Pin-1-Indikator:** schwarzer Dot auf Top, oben links neben Pin 1
- **Part-Marking-Bereich:** zentral auf der Oberseite
- **Pin-Nummerierung:** gegen den Uhrzeigersinn ab Pin 1 (Top View)

> Fur ROM-Code / Date-Code / Lot-ID Decodierung: externes Dokument abrufen
> oder bei Espressif Sales anfragen.

## Tape & Reel / Tray Packaging

Das Datasheet selbst enthalt **keine Tape-/Reel-/Tray-Detail-Mase**. Espressif
verweist hierfur konsequent auf:

> **"ESP32-S3 Chip Packaging Information"** (separates PDF von Espressif)
> -> dort: Tape-Breite, Sprocket-Pitch, Reel-Durchmesser, Stuckzahl/Reel,
>    Stuckzahl/Tray, Dry-Pack-Hinweise.

Distributor-typische Optionen fur QFN56 7x7 (Erfahrungswert, nicht datasheet-belegt):

| Verpackungsform | Typische Stuckzahl | Anwendung                          |
|-----------------|--------------------|-------------------------------------|
| Tape & Reel     | ~1000-2500 pcs     | Pick-and-Place / Reflow Serie       |
| Tray (JEDEC)    | ~120-260 pcs       | Klein- bis Mittelserien             |
| Cut Tape        | <= 100 pcs         | Distributor-Stueckverkauf (Mouser & Co.) |

> Bei Bestellung beim Distributor: pruefen ob das "TR"-Suffix (Tape & Reel)
> in der Bestellzeile gewuenscht ist - die Espressif-Part-Number selbst
> ("ESP32-S3FN8") andert sich dadurch nicht.

## MSL und Verarbeitung

Aus Datasheet Tabelle 5-13 (Reliability):

| Parameter           | Wert                              |
|---------------------|-----------------------------------|
| Moisture Soak Level | **MSL 3** (192 h @ 30 degC / 60 % rH) |
| Reflow              | 260 + 0 degC, 20 s, 3 x           |
| Bake (vor Reflow)   | 24 h @ 125 degC                   |

> Nach Offnen des Trockenbeutels: innerhalb **168 h** verlöten oder neu backen.

## Bezugsquellen / Where to Buy

Datasheet Seite 82 (Related Documentation and Resources) -> "Products":

- ESP32-S3 SoCs: https://espressif.com/en/products/socs?id=ESP32-S3
- ESP Product Selector: https://products.espressif.com/#/product-selector?language=en
- Sales / Get Samples: https://espressif.com/en/contact-us/sales-questions

Typische westliche Distributoren: Mouser, Digi-Key, Newark/Farnell, LCSC.

## Revision History - relevant fuer ESP32-S3FN8

Auszug aus Revision-History (Seiten 83-87), gefiltert auf was den FN8
betreffen koennte:

| Datasheet Version | Datum       | Was hat sich geandert (relevant fur FN8)                        |
|-------------------|-------------|------------------------------------------------------------------|
| **v2.2**          | 2026-03-05  | Update Tabelle 5-10 (Low-Power-Stromverbrauch), Address Mapping  |
| v2.1              | 2025-11-28  | Header "Ordering Code" -> **"Part Number"**; Chip Revision Info hinzugefuegt; ESP32-S3R2 -> EOL, ESP32-S3RH2 neu |
| v2.0              | 2025-04-24  | ESP32-S3R8V -> EOL; CoreMark Score Update; Memory Org. Update; Land-Pattern Source-File Link aktualisiert (Kapitel 7) |
| v1.9              | 2024-09-11  | Pin-Beschreibungen umstrukturiert (Section 2)                    |
| v1.8              | 2023-11-24  | ESP32-S3R16V hinzugefuegt; ADC-Charakteristik aktualisiert       |
| v1.7              | 2023-06     | Diverse Updates - keine direkten Auswirkungen auf FN8            |
| v1.6              | 2023-02     | Bluetooth max transmit power korrigiert; Notes in Kapitel 7 aktualisiert |
| v1.5              | 2022-12     | Ambient Temp fur FH4R2 von -40 ... 105 auf -40 ... 85 reduziert  |
| v1.4              | 2022-11     | Package-Info fur ESP32-S3FH4R2 hinzugefuegt; Errata-Link         |
| v1.3              | 2022-09     | Hinweis zu R8-Serie Temperatur-Limit; Power-up Glitches in Section 2.2 |
| v1.2              | 2022-07     | ROM-Code-Beschreibung in Section 3 aktualisiert                  |
| v1.1              | 2022-04     | Update Pin-Beschreibung; FH4R2-Info hinzugefuegt                 |
| v1.0              | 2022-01     | **Erstes Official Release**; ADC-Calibration Tabelle (5-5), Modem-sleep (5-9), Deep-sleep Beschreibung (5-10), JEDEC-Spec |
| v0.6.1            | 2021-10-12  | Text-Updates                                                     |
| v0.6              | 2021-09-30  | Chip Revision 1 (XTAL_P/N Pin-Swap)                              |
| v0.5.1            | 2021-07-19  | "for chip revision 0" Hinweise                                   |
| v0.5              | 2021-07-09  | Preliminary Version                                              |

> **Fazit fuer den ESP32-S3FN8:** Die Standard-Variante (kein PSRAM, 8 MB Flash)
> ist seit v1.0 (2022-01) im Datasheet stabil dokumentiert. Es gibt **kein
> NRND-/EOL-Vermerk** fuer ESP32-S3FN8 in v2.2 - der Chip ist **aktiv und fuer
> Neudesigns freigegeben.**

## Weiterfuehrende Dokumente (aus Seite 82)

- ESP32-S3 Technical Reference Manual
- ESP32-S3 Hardware Design Guidelines  <- **wichtig fuer dein Erstdesign**
- ESP32-S3 Series SoC Errata
- ESP32-S3 Chip Packaging Information  <- **Tape/Reel + Marking-Decode!**
- ESP32-S3 Product/Process Change Notifications (PCN)
- Certificates: https://espressif.com/en/support/documents/certificates
