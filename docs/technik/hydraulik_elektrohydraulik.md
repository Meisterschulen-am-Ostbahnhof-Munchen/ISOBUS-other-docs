# 💧 Hydraulik & Elektrohydraulik in Land- und Baumaschinen

> 📌 **Quellennachweis & Rechtsgrundlage:**  
> **Quelle:** *Rahmenlehrplan für die Vorbereitung auf die Meisterprüfung im Land- und Baumaschinenmechatroniker-Handwerk*  
> **Herausgeber:** LandBauTechnik-Bundesverband e. V., Alfredstraße 102, 45131 Essen (Stand: 25.02.2025)  
> **Verordnung:** *Meisterprüfungsverordnung (LandBauMechMstrV)* vom 09.09.2024 (BGBl. 2024 I Nr. 277, in Kraft ab 01.08.2025)

---


**Rahmenlehrplan-Kategorie:** Teil I LE 2.4 | Teil II LE 1.1 & LE 1.3  
**Relevanz:** Kerntechnologie für Arbeitsfunktionen, Kraftübertragung und Lenksysteme.

---

## 1. Hydraulische Systeme & Verstellpumpen

### Pumpentypen & Wirkungsgrade
- **Konstantpumpen:** Zahnradpumpen, Kolbenpumpen (Einsatz bei einfachen Hydraulikkreisläufen).
- **Verstellpumpen:** Axialkolbenpumpen in Schrägscheibenbauweise (Einsatz in Load-Sensing-Systemen).
- **Wirkungsgrade:** Volumetrischer und mechanisch-hydraulischer Wirkungsgrad, Verlustleistungsminimierung.

### Load-Sensing (LS) Steuerungsprinzip
- **Funktion:** Die Pumpe fördert nur den Druck ($p_{LS} + \Delta p$) und den Volumenstrom, den der aktuell aktivste Verbraucher anfordert.
- **LS-Signalleitung:** Übermittelt den höchsten Lastdruck aller aktiven Verbraucher an den Pumpenregler.
- **Vorteile:** Hohe Energieeffizienz, geringe Ölerwärmung, parallele Betätigung mehrerer Verbraucher unabhängig von der Last.

---

## 2. Elektrohydraulik & Proportionalventiltechnik

### Ventiltypen & Ansteuerung
- **Proportional-Wegeventile:** Stufenlose Steuerung von Ölmenge und Bewegungsrichtung.
- **PWM-Ansteuerung:** Pulsweitenmodulierte Signale (typisch 100–300 Hz) zur Reduzierung von Hysterese und Reibung an den Magnetspulen.
- **Schieberpositions-Rückmeldung:** Hall-Sensoren / LVDT-Sensoren zur präzisen Schieberüberwachung im Geschlossenen Regelkreis (Closed Loop).

### CAN-Bus-gesteuerte Ventilblöcke
- Dezentrale Ventilsteuereinheiten mit integriertem CAN-Bus-Knoten (ISOBUS / J1939-Kommunikation).
- Fehlerdiagnose: Kurzschluss, Unterbrechung, Spulenüberhitzung, Schieberverklemmung.

---

## 3. Hydraulikmedien & Schaltplanlesung (ISO 1219)

### Öle & Umweltschutz
- **Mineralöle:** HLP, HVLP (Viskositätklassen ISO VG 32, 46, 68).
- **Bio-Öle:** HEES (synthetische Ester), Biologische Abbaubarkeit, Verträglichkeit mit Dichtungsmaterialien (FKM, NBR).

### Normen & Symbolik
- Lesung komplexer ISO 1219-1 Schaltpläne: Druckbegrenzungsventile, Stromregelventile, Hydrospeicher, Logikventile (Cartridge-Technik).
