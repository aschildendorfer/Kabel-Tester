# HW-Konzept TDR Signallaufzeit Messung

## 1. Ziel
Dieses Dokument beschreibt ein Hardware-Konzept fuer einen BNC/Coax TDR-Tester, der:
- Kabelbruch erkennt
- Kurzschluss erkennt
- Fehlerposition ueber Laufzeitmessung abschaetzt

## 2. Messprinzip (Kurzfassung)
Ein schneller Puls wird in das Kabel eingespeist. Reflexionen durch Impedanzaenderungen werden zeitlich erfasst.
Aus der Laufzeit t wird die Entfernung d berechnet:

d = (c * VF * t) / 2

mit:
- c: Lichtgeschwindigkeit
- VF: Verkuerzungsfaktor des Kabels

## 3. System-Architektur
Geplanter Signalpfad:

1. Pulsquelle
2. Kopplungsnetzwerk / Richtkopplung
3. BNC-Testport
4. Reflexionsabgriff
5. Analog-Frontend (Verstaerker + Schutz)
6. Zeitmessung (Komparator + TDC oder schneller ADC)
7. MCU/SoC fuer Auswertung
8. Anzeige/Kommunikation

## 4. Blockmodule im Detail

### 4.1 Pulsquelle
- Ziel: steile Flanke fuer gute Ortsaufloesung
- Richtwert: Anstiegszeit im niedrigen ns-Bereich
- Loesung: schneller Treiberbaustein oder Logik mit Impulsformung

### 4.2 Koppel- und Schutznetzwerk
- Serienwiderstand zur Strombegrenzung
- ESD-Schutz am BNC-Port
- Anpassnetzwerk nahe 50 Ohm, um Eigenreflexionen zu reduzieren

### 4.3 Reflexionsabgriff
- Option A: Resistive Bruecke (einfach, guenstig)
- Option B: Richtkoppler (bessere Trennung von Sende- und Reflexionssignal)
- Ziel: Sendeimpulsunterdrueckung am Messkanal verbessern

### 4.4 Analog-Frontend
- Breitband-Verstaerker fuer kleine Reflexionssignale
- Begrenzer/Schutz gegen Ueberspannung
- Optional schaltbare Verstaerkung fuer kurze/lange Kabel

### 4.5 Zeitmessung
- Variante 1: Komparator + Time-to-Digital Converter (TDC)
- Variante 2: schneller ADC + digitale Peak/Edge-Erkennung
- Startsignal: Pulsstart
- Stoppsignal: erste signifikante Reflexion

### 4.6 Auswertungseinheit
- MCU liest Zeitmessung
- Kompensation von Systemoffset (Stecker, Leiterbahnen, Schalter)
- Distanzberechnung mit hinterlegtem VF-Profil
- Klassifikation: offen/Kabelbruch, Kurzschluss, unklar

## 5. Schnittstellen
- 1x BNC Testport (50 Ohm)
- USB-C oder UART fuer Debug/Logging
- Taster: Messung starten
- LEDs oder kleines Display fuer Ergebnisanzeige

## 6. Stromversorgung
- Versorgung ueber USB 5 V oder Akku
- Lokale LDOs fuer Analogteil und Digitalteil getrennt
- Saubere Massefuehrung mit sternfoermiger Rueckfuehrung empfohlen

## 7. PCB-Richtlinien
- Kurze HF-Pfade zwischen BNC, Kopplung und Frontend
- Kontrollierte Impedanz fuer kritische Leitungen (nahe 50 Ohm)
- Analoge und digitale Bereiche trennen
- Gute Masseflaeche und gezielte Via-Stitching entlang HF-Pfaden

## 8. Kalibrierkonzept
- Offset-Kalibrierung ohne Kabel
- Referenzkalibrierung mit bekannten Kabellaengen
- VF-Profile pro Kabeltyp (z. B. RG58, RG59, RG6)
- Speicherung von Kalibrierdaten im nichtfluechtigen Speicher

## 9. Messbereich und Zielwerte (V1)
- Kabellaenge: 1 m bis 150 m
- Ortsaufloesung: <= 1 m
- Wiederholgenauigkeit: <= 0,5 m unter stabilen Bedingungen
- Messdauer: <= 2 s pro Messung

## 10. Risiken und Gegenmassnahmen
- Zu langsame Flanken -> schlechte Aufloesung
  - Gegenmassnahme: schnellere Treiberstufe, sauberes Layout
- Starke Eigenreflexion im Geraet
  - Gegenmassnahme: besseres Matching, Kalibrierung, ggf. Richtkoppler
- Falscher VF-Wert
  - Gegenmassnahme: Kabeltyp-Auswahl + lernbare Kalibrierprofile

## 11. Vorschlag fuer Prototyping-Phasen
1. Breadboard/Lab-Setup mit Oszilloskop, Puls sichtbar machen
2. Reflexionsabgriff und Komparatorschwelle stabilisieren
3. Erste Distanzberechnung auf MCU
4. V1-PCB mit reproduzierbarer Messkette
5. Verifikation mit Referenzkabeln

## 12. Offene Entscheidungen
- TDC-basierte oder ADC-basierte Zeitmessung
- Resistive Bruecke vs. Richtkoppler
- Anzeige nur LED oder zusaetzlich Display
- Zielkosten pro Geraet in Serie
