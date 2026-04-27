# Kabel-Tester
Kabel Tester Projekt für verschiedene Kabeltypen

1. Coax-Kabel mit BNC stecker
1.1 kabelbruch, Kurzscluss messung
1.2 signal Qualität für SDI full HD

2. Mikrofon Kabel
2.1 mit XLR Stecker
2.1 mit 6,3mm Klinken Stecker

## Plan für 1.1 BNC-Kabel Prüfer (Kabelbruch, Kurzschluss)

### Ziel
Ein Prüfmodus, der bei BNC-Kabeln sicher erkennt:
- Kabelbruch (Innenleiter unterbrochen oder Schirm unterbrochen)
- Kurzschluss (Innenleiter gegen Schirm)

### 1. Anforderungen festlegen
- Messzeit pro Kabel: maximal 2 Sekunden
- Eindeutige Ergebnisse: OK, Kabelbruch, Kurzschluss
- Einfache Anzeige über LEDs oder kleines Display

### 2. Messprinzip definieren
- Innenleiter und Schirm an beiden Kabelenden getrennt kontaktieren
- Kontinuität je Leiter prüfen (Durchgang ja/nein)
- Isolation zwischen Innenleiter und Schirm prüfen (kein Durchgang)

### 3. Hardware-Konzept
- 2x BNC-Buchsen (Prüfseite A und B)
- Mikrocontroller oder einfacher Logikaufbau
- Schutzbeschaltung mit Serienwiderstand und ESD-Schutz
- Anzeigeeinheit:
	- Gruene LED: OK
	- Gelbe LED: Kabelbruch
	- Rote LED: Kurzschluss

### 4. Auswerte-Logik
- Fall 1: Innenleiter Durchgang, Schirm Durchgang, keine Verbindung zwischen beiden Leitern -> OK
- Fall 2: Innenleiter ohne Durchgang oder Schirm ohne Durchgang -> Kabelbruch
- Fall 3: Verbindung zwischen Innenleiter und Schirm vorhanden -> Kurzschluss

### 5. Software-Ablauf (falls Mikrocontroller)
- Initialisierung der Ein- und Ausgaenge
- Sequenzielles Schalten der Testsignale
- Mehrfachmessung mit Entprellung (z. B. 5 Messungen)
- Fehlerklassifikation nach Prioritaet:
	- Kurzschluss vor Kabelbruch melden
- Ergebnis anzeigen und optional per serieller Schnittstelle ausgeben

### 6. Testplan
- Referenzkabel messen (erwartet: OK)
- Definierten Kabelbruch im Innenleiter erzeugen (erwartet: Kabelbruch)
- Definierten Kabelbruch im Schirm erzeugen (erwartet: Kabelbruch)
- Definierten Kurzschluss Innenleiter-Schirm erzeugen (erwartet: Kurzschluss)
- Grenzfall mit Wackelkontakt testen

### 7. Meilensteine
- M1: Messprinzip auf Breadboard validiert
- M2: Prototyp-Hardware aufgebaut
- M3: Firmware mit Fehlerklassifikation laeuft stabil
- M4: Testserie abgeschlossen und dokumentiert

### 8. Ergebnis fuer Abschnitt 1.1
Abschnitt 1.1 gilt als abgeschlossen, wenn die Erkennung von Kabelbruch und Kurzschluss reproduzierbar funktioniert und alle vier Kern-Testfaelle bestanden sind.

## Plan zur Ermittlung eines Kabelbruchs ueber Signallaufzeitmessung (TDR)

### Ziel
Position eines Kabelbruchs entlang eines Coax-Kabels bestimmen, nicht nur "Fehler vorhanden".

### 1. Messprinzip (Time Domain Reflectometry)
- Ein schneller Spannungspuls wird in das Kabel eingespeist.
- An Impedanzspruengen (z. B. Kabelbruch, Kurzschluss, offene Leitung) wird ein Teil des Signals reflektiert.
- Aus der Zeitdifferenz zwischen Sendeimpuls und Reflexion wird die Fehlerentfernung berechnet.

### 2. Grundformel fuer die Fehlerentfernung
- Ausbreitungsgeschwindigkeit im Kabel: v = c * VF
- c = 299792458 m/s
- VF = Verkuerzungsfaktor des Kabels (typisch 0,66 bis 0,85 je nach Dielektrikum)
- Entfernung zum Fehler:
	- d = (v * t) / 2
	- t = gemessene Hin- und Ruecklaufzeit der Reflexion

### 3. Anforderungen
- Zeitaufloesung so waehlen, dass Ortsaufloesung praxisgerecht ist (z. B. <= 0,5 m)
- Kabellaenge-Messbereich passend zum Einsatzzweck (z. B. 1 m bis 300 m)
- Robuste Erkennung fuer:
	- offenes Ende / Kabelbruch (positive Reflexion)
	- Kurzschluss (negative Reflexion)

### 4. Hardware-Konzept
- Pulsgenerator mit steiler Flanke (ns-Bereich anstreben)
- Koppel- und Schutznetzwerk (Serienwiderstand, ESD-Schutz)
- Abtastung:
	- schnelles Komparator/TDC-Konzept oder
	- schneller ADC mit Trigger
- BNC-Eingang und definierte Referenzmasse
- Optional: umschaltbare Abschlusswiderstaende zur Kalibrierung

### 5. Messablauf
1. Kabeltyp bzw. VF aus Profil waehlen
2. Nullpunktmessung ohne Kabel bzw. mit Referenzkabel durchfuehren
3. Puls einspeisen und Startzeitpunkt setzen
4. Reflexionssignal erfassen und erste signifikante Reflexion detektieren
5. Zeitdifferenz t bestimmen
6. Distanz d berechnen
7. Ergebnis ausgeben: Fehlerart + Entfernung in m

### 6. Signalinterpretation
- Positive, deutliche Reflexion: hochohmige Stoerung, typisch Kabelbruch / offenes Ende
- Negative, deutliche Reflexion: niederohmige Stoerung, typisch Kurzschluss
- Mehrere Reflexionen: moegliche Mehrfachfehler oder Steckverbinder-Transitions

### 7. Kalibrierung
- VF pro Kabeltyp hinterlegen (RG58, RG59, RG6 usw.)
- Mit bekannten Referenzlaengen Kalibrierkurve aufnehmen
- Systemoffset (Stecker, Leiterplatte, Relais) als fixe Laufzeit abziehen

### 8. Testplan
- Offenes Kabelende bei bekannten Laengen (z. B. 10 m, 50 m, 100 m)
- Definierter Kurzschluss an bekannten Positionen
- Teilweise Beschaedigung / Wackelkontakt mit wiederholten Messungen
- Vergleich mit mechanisch gemessener Laenge zur Fehlerabschaetzung

### 9. Akzeptanzkriterien
- Fehlerart (Bruch vs. Kurzschluss) korrekt in >= 95 Prozent der Testfaelle
- Positionsfehler innerhalb von +/- 2 Prozent der Kabellaenge oder besser
- Reproduzierbarkeit: Abweichung bei 10 Wiederholungen < 0,5 m (bei mittleren Laengen)

### 10. Meilensteine
- M1: TDR-Signal auf Oszilloskop reproduzierbar sichtbar
- M2: Erste automatische Peak-/Reflexionserkennung implementiert
- M3: Distanzberechnung mit VF-Profilen stabil
- M4: Validierung mit Referenzkabeln abgeschlossen
