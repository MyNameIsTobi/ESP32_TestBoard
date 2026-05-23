> **Source of truth: `datasheets/esp32-s3_datasheet_en.pdf`** (v2.2)
> This file is a quick-reference summary. If anything conflicts with the PDF, the PDF wins.
> Extracted from pages: 77-78

# 09 - Package / Gehause (QFN56, 7x7 mm)

## Uberblick

Die ESP32-S3 Serie - **inkl. unserem Ziel ESP32-S3FN8** - wird in einem
**QFN56-Gehause mit 7 x 7 mm Korpermass** ausgeliefert. Alle ESP32-S3 Varianten
teilen sich denselben Footprint / Land Pattern, **mit Ausnahme von
ESP32-S3FH4R2**, das ein groeseres EPAD hat (siehe Abschnitt unten "Variante
FH4R2"). Fur den **ESP32-S3FN8 gilt das Standard-Land-Pattern aus Figure 7-1**.

- Package: QFN56 (Quad Flat No-leads, 56 Pins)
- Korpermass nominal: 7.0 mm x 7.0 mm
- Pin-Pitch: 0.40 mm
- JEDEC Referenz: MO-220
- Pin 1 Markierung: schwarzer Punkt auf der Oberseite, oben links
- Beschriftung Top View: "56L SLP (7x7mm)" (bzw. das ROM-/Marking-Feld, siehe
  Markings-Abschnitt)

## Mass-Tabelle - QFN56 (Standard, gilt fur ESP32-S3FN8)

Quelle: Figure 7-1, Dimensional Reference (Seite 77). Alle Werte in **Millimeter**.

| Symbol | Beschreibung                              |   Min |   Nom |   Max |
|--------|--------------------------------------------|------:|------:|------:|
| A      | Total thickness (Gesamthohe)               | 0.800 | 0.850 | 0.900 |
| A1     | Stand-off (Abstand Body zu PCB)            | 0.000 |   --- | 0.050 |
| A3     | Lead frame thickness                       |       | 0.203 |  REF  |
| D      | Body size X                                | 6.950 | 7.000 | 7.050 |
| E      | Body size Y                                | 6.950 | 7.000 | 7.050 |
| **D2** | **Exposed pad (EPAD) X**                   | 3.950 | 4.000 | 4.050 |
| **E2** | **Exposed pad (EPAD) Y**                   | 3.950 | 4.000 | 4.050 |
| **e**  | **Pin pitch**                              |       | 0.400 |  BSC  |
| b      | Lead width                                 | 0.150 | 0.200 | 0.250 |
| L      | Lead length                                | 0.350 | 0.400 | 0.450 |

### Toleranzen (Form & Position)

| Symbol | Beschreibung                              | Wert (mm) |
|--------|--------------------------------------------|----------:|
| aaa    | Package edge tolerance                     |      0.10 |
| bbb    | Lead tip to exposed pad edge tolerance     |      0.10 |
| ccc    | Mold flatness                              |      0.10 |
| ddd    |                                            |      0.05 |
| eee    | Coplanarity                                |      0.08 |
| fff    | Exposed pad offset                         |      0.10 |

> Hinweis Mass-System: alle Mase in mm; Tolerierung nach **JEDEC MO-220**.

## Variante - ESP32-S3FH4R2 (nur zur Abgrenzung)

Figure 7-2 zeigt eine abweichende Variante **nur fur ESP32-S3FH4R2**: gleiche
Body-Mase 7 x 7 mm, gleicher Pitch 0.40 mm BSC, aber **groseres EPAD**:

| Symbol | Beschreibung                  | Min | Nom | Max | Einheit |
|--------|--------------------------------|----:|----:|----:|---------|
| A      | Total thickness                | 0.8 | 0.85| 0.9 | mm      |
| A1     | Stand off                      | 0   | 0.02| 0.05| mm      |
| A2     | Mold thickness                 |  -- | 0.65|  -- | mm      |
| A3     | L/F thickness                  |     | 0.203 REF |  | mm  |
| b      | Lead width                     | 0.15| 0.2 | 0.25| mm      |
| D / E  | Body size X / Y                |     | 7.000 BSC |  | mm  |
| e      | Lead pitch                     |     | 0.400 BSC |  | mm  |
| **D2** | **EPAD X (FH4R2!)**            | 5.6 | 5.7 | 5.8 | mm      |
| **E2** | **EPAD Y (FH4R2!)**            | 5.6 | 5.7 | 5.8 | mm      |
| L      | Lead length                    | 0.3 | 0.4 | 0.5 | mm      |
| K      | Lead tip to exposed pad edge   |     | 0.25 REF  |  | mm  |

> **Fur ESP32-S3FN8: Diese Tabelle ist NICHT zutreffend.** EPAD bleibt 4.0 x 4.0 mm.

## Empfohlenes Land Pattern (PCB Footprint)

Das Datasheet **liefert keine vollstandige Land-Pattern-Tabelle mit Pad-Mas-en
im PDF**. Stattdessen wird verwiesen auf:

- **Source-Datei (.asc) zum Download** - importierbar in PADS / Altium Designer.
- Alle ESP32-S3 Varianten haben dasselbe Land Pattern, ausgenommen
  ESP32-S3FH4R2 (groseres EPAD - siehe oben). Fur FH4R2 muss D2/E2 in der
  Source-Datei angepasst werden.
- Externes Dokument: **ESP32-S3 Chip Packaging Information** (Espressif).
  Dort stehen Tape/Reel, Marking und Detail-Empfehlungen zum Footprint.

### Praktische Footprint-Eckwerte fur KiCad (aus den Body-Massen abgeleitet)

| Parameter            | Wert            | Hinweis                              |
|----------------------|-----------------|--------------------------------------|
| Package Outline      | 7.00 x 7.00 mm  | Body D x E nominal                   |
| Pin Count            | 56              | 14 Pins pro Seite                    |
| Pin Pitch            | 0.40 mm BSC     | Symbol e                             |
| Pin Pad (b)          | 0.20 mm breit   | typ. Pad-Breite ~ 0.25 mm fur Loten  |
| Pin Pad (L)          | 0.40 mm lang    | Lot-Pad typ. 0.55-0.60 mm lang       |
| EPAD (D2 x E2)       | 4.00 x 4.00 mm  | thermisch + GND, gevia'd auf GND     |
| EPAD-Toleranz        | +-0.05 mm       |                                      |
| Stand-off A1         | 0 ... 0.05 mm   | sehr flach - keine Bauteile drunter  |

> **Hinweis fur KiCad:** Verwende die Espressif `.asc` Source-Datei oder den
> KiCad-Bibliotheks-Footprint `Package_DFN_QFN:QFN-56-1EP_7x7mm_P0.4mm_EP4x4mm`
> als Ausgangspunkt. Pruefe D2/E2 = 4.00 mm vor dem Routing.

## Solder Mask / Paste Opening

Das Datasheet enthalt **keine expliziten Empfehlungen** fur Solder Mask Opening
oder Stencil Paste Opening fur das EPAD. Standardpraxis fur QFN mit zentralem
Thermal Pad:

- **Solder Mask Opening:** Pad + ~50 um (NSMD-Pads bevorzugt)
- **EPAD Stencil:** in 4-9 kleinere Apertures unterteilen
  (je ~1.0 mm x 1.0 mm, ~60-75 % Paste-Coverage), um Voiding und
  Tombstoning zu reduzieren
- **Thermal Vias im EPAD:** Cluster aus 0.3 mm Vias auf 1.0-1.2 mm Raster,
  Tenting oder Via-in-Pad (mit Plug+Plate) je nach Fertigungsmoglichkeit

> Wenn moglich: IPC-7351B "QFN" Land-Pattern-Vorgaben heranziehen.

## Marking / Beschriftung

Quelle: Figure 7-1 (Top View) - "PIN 1 DOT BY MARKING".

```
        +---------------------+
        |@                    |   <- @ = Pin 1 dot
        |                     |
        |    56L SLP          |
        |    (7 x 7mm)        |
        |                     |
        |                     |
        +---------------------+
              TOP VIEW
```

Das Top-Marking enthalt:
- **Pin-1-Punkt** (schwarz, oben links)
- **Part Number / ROM-Code** (auf dem realen Chip; im Datasheet als Platzhalter
  "56L SLP (7x7mm)" dargestellt). Die genaue Decodierung der Marking-Zeile
  steht im externen Dokument **"ESP32-S3 Chip Packaging Information"**.

> Pins werden im Top View **gegen den Uhrzeigersinn** ab Pin 1 nummeriert.

## Thermal Resistance (theta-JA, theta-JC)

Das Datasheet v2.2 in Kapitel 7 *Packaging* enthalt **keine numerischen
Thermal-Resistance-Werte** (weder theta-JA noch theta-JC). Wenn benotigt:
- ESP32-S3 Hardware Design Guidelines konsultieren, oder
- "ESP32-S3 Chip Packaging Information" beim Hersteller anfragen.

## Reliability-Hinweis fur die Verarbeitung

Aus Reliability-Tabelle 5-13 (Seite 69), relevant fur den Loetprozess:

| Item                       | Bedingung                                          |
|----------------------------|----------------------------------------------------|
| MSL (Moisture Soak)        | Level 3: 192 h @ 30 degC, 60 % rH                  |
| IR Reflow Solder           | 260 + 0 degC, 20 s, 3 x                            |
| Bake (Preconditioning)     | 24 h @ 125 degC                                    |
| TCT (Temperature Cycling)  | -65 degC / +150 degC, 500 Zyklen                   |
| HTSL                       | 150 degC, 1000 h                                   |

> **MSL 3** bedeutet: nach dem Offnen des Trockenbeutels innerhalb von 168 h
> bei <=30 degC / <=60 % rH verlöten, sonst Bake nachholen.
