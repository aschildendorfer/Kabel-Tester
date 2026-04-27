# MCU-Bewertung fuer TDR-Signalmessung an Coax-Kabeln

## 1. Ziel
Auswahl eines geeigneten Mikrocontrollers (MCU) fuer den Einsatz im BNC/Coax-TDR-Tester
gemaess [hw-concept-tdr.md](hw-concept-tdr.md).

Kriterien: einfache Handhabung, geringe Kosten, geeignet fuer DIY-Prototyp.

---

## 2. Anforderungen aus dem TDR-Konzept

| Anforderung                   | Wert / Beschreibung                                           |
|-------------------------------|---------------------------------------------------------------|
| Zeitaufloesung (Zeitmessung)  | ~ 3–10 ns (fuer Ortsaufloesung ≤ 0,5 m bei VF = 0,66)       |
| Messbereich Kabellaenge       | 1 m bis 150 m                                                 |
| Pulsgenerierung               | Steile Flanke im ns-Bereich                                   |
| ADC oder TDC                  | Schneller ADC (≥ 1 MSPS) oder externe TDC-Unterstuetzung     |
| Schnittstellen                | USB / UART fuer Debug, GPIO fuer LEDs/Display, SPI/I2C       |
| WLAN optional                 | Fuer Datenuebertragung / Fernzugriff wuenschenswert           |
| Stromversorgung               | USB 5 V oder Akku                                             |
| Kosten                        | Gering, DIY-freundlich (< 20 EUR bevorzugt)                   |
| Community / Bibliotheken      | Gut dokumentiert, aktive Community                            |

**Wichtige Anmerkung zur Zeitaufloesung:**
Fuer eine Ortsaufloesung von 0,5 m bei einem Verkuerzungsfaktor VF = 0,66 ergibt sich
eine benoetigte Zeitaufloesung von:

```
t = (2 * d) / (c * VF) = (2 * 0,5 m) / (3e8 m/s * 0,66) ≈ 5 ns
```

Ein einzelner MCU-ADC allein reicht dafuer in der Regel nicht aus.
**Ergaenzend** wird ein externer TDC (z. B. TDC7200) oder ein Komparator mit Zeitstempel
empfohlen. Der MCU steuert die Messung, verarbeitet die Ergebnisse und gibt sie aus.

---

## 3. Bewertungstabelle MCU-Vergleich

| Kriterium                        | Arduino Uno/Nano     | Arduino Due          | ESP32                | ESP32-S3             | RPi Pico (RP2040)    | **RPi Pico W/WH**    | Raspberry Pi 4B / Zero 2W |
|----------------------------------|----------------------|----------------------|----------------------|----------------------|----------------------|----------------------|---------------------------|
| **CPU-Takt**                     | 16 MHz               | 84 MHz               | 240 MHz (Dual)       | 240 MHz (Dual)       | 133 MHz (Dual)       | **133 MHz (Dual)**   | 1,8 GHz / 1,0 GHz         |
| **Architektur**                  | AVR 8-bit            | ARM Cortex-M3        | Xtensa LX6           | Xtensa LX7           | ARM Cortex-M0+       | **ARM Cortex-M0+**   | ARM Cortex-A72 / A53      |
| **ADC Aufloesung**               | 10 bit               | 12 bit               | 12 bit               | 12 bit               | 12 bit               | **12 bit**           | Kein integrierter ADC     |
| **ADC-Abtastrate (max.)**        | ~77 kSPS             | ~1 MSPS              | ~2 MSPS              | ~2 MSPS              | **500 kSPS**         | **500 kSPS**         | Kein integrierter ADC     |
| **PIO / Hardware-Timer**         | Nein (Software-PWM)  | Nein                 | RMT / MCPWM          | RMT / MCPWM          | **8x PIO SM**        | **8x PIO SM**        | Kein PIO                  |
| **Timer-Aufloesung**             | 62,5 ns (16 MHz)     | ~12 ns (84 MHz)      | ~4 ns (240 MHz)      | ~4 ns (240 MHz)      | **~7,5 ns (133 MHz)**| **~7,5 ns (133 MHz)**| Sub-ns (Linux HRTimer)    |
| **WLAN integriert**              | Nein                 | Nein                 | Ja (2,4 GHz)         | Ja (2,4 GHz)         | Nein                 | **Ja (2,4 GHz)**     | Ja (Wi-Fi + BT)           |
| **USB**                          | USB-B (Seriell)      | USB-Micro            | USB (ueber CP2102)   | USB 1.1 nativ        | **USB 1.1 nativ**    | **USB 1.1 nativ**    | USB-C / Micro-USB         |
| **GPIO-Anzahl**                  | 14 (6 PWM)           | 54 (12 PWM)          | 34                   | 45                   | **26**               | **26**               | 40 (Pi4) / 40 (Zero2W)    |
| **SPI / I2C / UART**             | 1/1/1                | 1/2/4                | 4/2/3                | 4/2/3                | **2/2/2**            | **2/2/2**            | 2/1/5                     |
| **Flash-Speicher**               | 32 KB                | 512 KB               | 4 MB (typisch)       | 8 MB (typisch)       | **2 MB**             | **2 MB**             | SD-Karte (OS)             |
| **RAM**                          | 2 KB                 | 96 KB                | 520 KB               | 512 KB + 8 MB PSRAM  | **264 KB**           | **264 KB**           | 2–8 GB (Pi4) / 512 MB     |
| **Betriebsspannung I/O**         | 5 V                  | 3,3 V                | 3,3 V                | 3,3 V                | **3,3 V**            | **3,3 V**            | 3,3 V                     |
| **Stromaufnahme (typisch)**      | ~50 mA               | ~130 mA              | ~80–240 mA           | ~80–240 mA           | **~25 mA**           | **~25–45 mA**        | 600 mA – 3 A              |
| **Preis (ca.)**                  | 5–10 EUR             | 30–40 EUR            | 4–8 EUR              | 5–10 EUR             | 4–5 EUR              | **6–8 EUR**          | 15–80 EUR + Kuehlkoerper  |
| **Formfaktor**                   | DIP / Nano           | Arduino-Shield       | Modul / DevKit       | Modul / DevKit       | 21 x 51 mm           | **21 x 51 mm**       | Platine (gross)           |
| **Vorgeloetete Header**          | Ja (Nano)            | Nein (standard)      | Nein (standard)      | Nein (standard)      | Nein (Pico)          | **Ja (WH-Variante)** | Ja (Pi4)                  |
| **MicroPython-Unterstuetzung**   | Nein                 | Nein                 | Ja (MicroPython)     | Ja (MicroPython)     | Ja (MicroPython)     | **Ja (MicroPython)** | CPython (Linux)           |
| **C/C++-Unterstuetzung**         | Ja (Arduino IDE)     | Ja (Arduino IDE)     | Ja (ESP-IDF / Arduino)| Ja                  | Ja (Pico SDK)        | **Ja (Pico SDK)**    | Ja (Linux GCC)            |
| **Bewertung TDR-Eignung**        | ⚠️ Gering            | ✅ Ausreichend        | ✅ Gut               | ✅ Gut               | ✅ Sehr gut          | **✅✅ Sehr gut**    | ⚠️ Ueberqualifiziert      |

---

## 4. Detailbewertung der Kandidaten

### 4.1 Arduino Uno / Nano (ATmega328P)
- **Staerken:** Sehr weit verbreitet, einfachste Einstiegsplattform, guenstig.
- **Schwaerchen fuer TDR:** 16 MHz Takt reicht fuer ns-genaue Zeitmessung nicht aus;
  ADC mit max. 77 kSPS viel zu langsam fuer direkte Signalabtastung.
- **Fazit:** Nur geeignet fuer einfache Durchgangspruefer (Abschnitt 1.1), **nicht fuer TDR**.

### 4.2 Arduino Due (SAM3X8E)
- **Staerken:** 84 MHz, 12-bit ADC mit bis zu 1 MSPS, mehr RAM.
- **Schwaechen fuer TDR:** Kein WiFi, grosser Formfaktor, hoehere Kosten, veraltetes Board.
- **Fazit:** Technisch ausreichend fuer TDR mit externem TDC, aber nicht optimal fuer DIY.

### 4.3 ESP32 (Xtensa LX6)
- **Staerken:** Sehr guenstig, integriertes WLAN/BT, 240 MHz Dual-Core, 12-bit ADC bis 2 MSPS,
  RMT-Peripheral fuer praezise Pulsausgabe.
- **Schwaechen fuer TDR:** ADC-Qualitaet teils eingeschraenkt (Rauschen, nichtlinear), kein
  PIO fuer flexible Timing-Pipelines.
- **Fazit:** Gute Option, Community sehr gross; ADC-Schwaechen muessen beachtet werden.

### 4.4 ESP32-S3
- **Staerken:** Verbesserte Version mit USB-OTG, PSRAM-Unterstuetzung, AI-Erweiterungen.
- **Schwaechen fuer TDR:** Aehnlich wie ESP32; ADC-Charakteristik verbessert aber noch
  immer nicht ideal; etwas teuerer.
- **Fazit:** Upgrade-Pfad von ESP32, gute Wahl wenn PSRAM benoetigt wird.

### 4.5 Raspberry Pi Pico (RP2040) – ohne WLAN
- **Staerken:** **PIO (Programmable I/O)** – 8 unabhaengige State Machines erlauben
  taktzyklengenaue Pulsausgabe und Zeiterfassung (7,5 ns @ 133 MHz); 12-bit ADC,
  guenstiger Preis, kompakte Bauform, exzellente SDK-Dokumentation.
- **Schwaechen:** Kein integriertes WLAN, nur 2 MB Flash.
- **Fazit:** Ausgezeichnete Basis fuer TDR-Prototypen; PIO macht praezise Pulserzeugung
  direkt auf dem Chip moeglich.

### 4.6 Raspberry Pi Pico W / WH (RP2040 + CYW43439) ⭐ EMPFEHLUNG
- **Staerken gegenueber Pico:** Identische Hardware + **integriertes 2,4-GHz-WLAN und
  Bluetooth** ueber Infineon CYW43439; die **WH-Variante hat vorgeloetete Stiftleisten**
  fuer Plug-and-Play-Prototyping.
- **PIO-Vorteil fuer TDR:**
  - State Machine kann einen genau definierten ns-Puls erzeugen
  - Zweite State Machine misst den Zeitstempel der Reflexion mit Zyklusgenauigkeit
  - Kein Betriebssystem-Jitter (Bare-Metal)
- **WLAN-Vorteil:** Messergebnisse drahtlos an PC/Smartphone uebertragen, OTA-Updates.
- **Kosten:** ca. 6–8 EUR (WH ca. 1–2 EUR Aufpreis fuer Header).
- **Programmieroption:** MicroPython fuer schnellen Einstieg, C/C++ (Pico SDK) fuer
  maximale Performance.
- **Fazit:** **Beste Wahl** fuer den TDR-DIY-Prototypen – optimales Preis-/
  Leistungsverhaeltnis, WLAN inkludiert, vorgeloetete Header vereinfachen den Aufbau.

### 4.7 Raspberry Pi 4B / Zero 2W (Linux SBC)
- **Staerken:** Vollstaendiges Linux-Betriebssystem, hohe Rechenleistung, Python-Ecosystem.
- **Schwaechen fuer TDR:** **Kein integrierter ADC**, Linux-Jitter problematisch fuer
  ns-genaue Zeitmessung ohne Echtzeit-Erweiterung (PREEMPT_RT), hoher Stromverbrauch,
  teurer (Pi 4B), Bootezeit.
- **Fazit:** Als reine MCU-Loesung **nicht empfohlen**. Koennte als uebergeordneter
  Host-Computer zum RPi Pico W dienen (USB/UART-Verbindung).

---

## 5. Entscheidungsmatrix (Punkte 0–5)

| Kriterium               | Gewichtung | Arduino Uno | Arduino Due | ESP32 | ESP32-S3 | Pico   | **Pico W/WH** | RPi 4B |
|-------------------------|------------|-------------|-------------|-------|----------|--------|---------------|--------|
| TDR-Zeitaufloesung      | 25 %       | 1           | 3           | 3     | 3        | 4      | **4**         | 2      |
| ADC-Qualitaet           | 15 %       | 1           | 3           | 3     | 3        | 3      | **3**         | 0      |
| Kosten                  | 20 %       | 4           | 2           | 5     | 4        | 5      | **4**         | 2      |
| Einfache Handhabung     | 15 %       | 5           | 3           | 4     | 3        | 4      | **5**         | 3      |
| WLAN / Konnektivitaet   | 10 %       | 0           | 0           | 5     | 5        | 0      | **5**         | 5      |
| Community / Doku        | 10 %       | 5           | 3           | 4     | 3        | 4      | **4**         | 4      |
| DIY-Prototyping         | 5 %        | 4           | 2           | 3     | 3        | 4      | **5**         | 2      |
| **Gesamtpunktzahl**     | **100 %**  | **2,85**    | **2,65**    | **3,65** | **3,35** | **3,55** | **4,05** | **2,35** |

> Berechnung Beispiel Pico W/WH: (4×0,25) + (3×0,15) + (4×0,20) + (5×0,15) + (5×0,10) + (4×0,10) + (5×0,05) = **4,05**

---

## 6. Empfehlung

### ✅ Primaerentscheidung: **Raspberry Pi Pico WH**

Der **RPi Pico WH** (RP2040, mit WLAN, vorgeloeteten Stiftleisten) ist die bevorzugte MCU
fuer den TDR-Prototypen:

| Eigenschaft             | Details                                                              |
|-------------------------|----------------------------------------------------------------------|
| Chip                    | RP2040 (Dual-Core ARM Cortex-M0+ @ 133 MHz)                        |
| WLAN                    | 2,4 GHz IEEE 802.11b/g/n (Infineon CYW43439)                       |
| Bluetooth               | Bluetooth 5.2 LE                                                     |
| PIO State Machines      | 8x (ideal fuer ns-genaue Pulserzeugung und Zeiterfassung)           |
| ADC                     | 3 Kanaele, 12 bit, 500 kSPS (ergaenzt durch externen TDC/Komparator)|
| GPIO                    | 26 nutzbare Pins, alle 3,3 V                                        |
| USB                     | USB 1.1 Micro-B nativ (Serielle Konsole, UF2-Flashen)              |
| Vorgeloetete Header     | Ja (WH-Variante), sofort steckbar ins Breadboard                    |
| Preis                   | ca. 6–8 EUR                                                          |
| Programmiersprachen     | MicroPython, C/C++ (Pico SDK), CircuitPython                        |

### ⚡ Hinweis zur Zeitmessung
Der RP2040-ADC allein reicht fuer sub-5-ns-Aufloesung nicht aus.
Fuer die TDR-Messung wird empfohlen:

- **Option A (einfach):** Externer Komparator (z. B. ADCMP601) + PIO-Zeitstempel
  → PIO zaehlt Taktzyklen zwischen Pulsstart und Reflexionsdetektion (7,5 ns Aufloesung)
- **Option B (praezise):** Externer TDC-IC (z. B. TDC7200 von Texas Instruments) + SPI
  → Aufloesung < 1 ns moeglich, Pico liest per SPI aus

### 🔄 Alternative: ESP32 (bei Bedarf groesserer Community)
Falls das breite ESP32-Ecosystem (Arduino-Bibliotheken, ESPHome, etc.) benoetigt wird,
ist der ESP32 eine gute Alternative – insbesondere wegen des integrierten WLAN und der
grossen Community. Die ADC-Nichtlinearitaet muss durch Kalibrierung kompensiert werden.

---

## 7. Naechste Schritte

1. **Prototyp-Hardware bestellen:** RPi Pico WH + Komparator-Breakout (z. B. ADCMP601) +
   BNC-Buchse + Breadboard-Zuleitung
2. **PIO-Programm entwickeln:** Puls erzeugen + Reflexion zeitstempeln
3. **Erste TDR-Signale auf Oszilloskop validieren** (Meilenstein M1 aus README.md)
4. **Distanzberechnung implementieren** (MicroPython oder C/Pico SDK)
5. **Optional:** Externes TDC7200-Modul einbinden fuer hoehere Aufloesung (Meilenstein M2)
