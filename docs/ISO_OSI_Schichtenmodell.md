# ISO/OSI Schichtenmodell

```{index} single: OSI-Modell
```
```{index} single: SAE J1939
```
```{index} single: ISO 11783
```
```{index} single: Schichtenmodell
```

## Allgmeines:

Das ISO/OSI-Referenzmodell (auch OSI-Modell genannt) ist ein theoretisches Modell, das die Kommunikation zwischen verschiedenen Computern und Netzwerken beschreibt. Es wurde von der Internationalen Organisation für Normung (ISO) entwickelt und ist in der Norm ISO 7498-1 spezifiziert.

Das OSI-Modell besteht aus sieben Schichten, die jeweils spezifische Aufgaben im Kommunikationsprozess haben:

Physikalische Schicht: Übertragung von Bits über das physikalische Medium (Kabel, Funk, Glasfaser etc.)

Sicherungsschicht: Fehlererkennung und -korrektur, Adressierung der Geräte (MAC-Adresse)

Vermittlungsschicht: Routing von Paketen zwischen verschiedenen Netzwerken

Transportschicht: Aufteilung der Daten in Pakete, Gewährleistung der Zuverlässigkeit und Sicherstellung der vollständigen Übertragung

Sitzungsschicht: Aufbau, Aufrechterhaltung und Abbau von Sitzungen zwischen Anwendungen

Darstellungsschicht: Umwandlung der Daten in ein für die Anwendung verständliches Format

Anwendungsschicht: Schnittstelle zwischen Anwendungen und dem Netzwerk, z.B. E-Mail, Web-Browser oder Dateitransfer

Das OSI-Modell dient als Referenz für die Entwicklung von Netzwerkprotokollen und -architekturen. Es ermöglicht es, verschiedene Netzwerktechnologien zu integrieren und zu standardisieren, so dass Geräte von verschiedenen Herstellern miteinander kommunizieren können.

## ISO/OSI-Referenzmodell und SAE J1939

SAE J1939 ist ein Protokoll, das speziell für den Einsatz in Nutzfahrzeugen und -maschinen entwickelt wurde. Es basiert auf dem CAN-Bus-Standard und verwendet das ISO/OSI-Referenzmodell als Grundlage für seine Schichtenarchitektur.

Die SAE J1939-Protokollarchitektur besteht aus fünf Schichten:

Anwendungsschicht: Hier werden die spezifischen Anwendungsdaten definiert, die zwischen den Geräten im Netzwerk ausgetauscht werden.

Transport- oder Dienstschicht: Diese Schicht ist für die Aufteilung der Daten in kleinere Pakete und die Gewährleistung der Zuverlässigkeit der Datenübertragung verantwortlich.

Netzwerkschicht: Hier werden die Adressierung und der Austausch von Paketen zwischen verschiedenen Netzwerken geregelt.

Sicherungsschicht: Diese Schicht bietet Fehlererkennung und -korrektur sowie den Zugriff auf das Medium (CAN-Bus).

Physikalische Schicht: Hier wird die elektrische und physikalische Übertragung der Daten über das Medium (CAN-Bus) abgewickelt.

Wie das OSI-Modell ermöglicht auch die Schichtenarchitektur von SAE J1939 die Interoperabilität und Integration verschiedener Geräte von unterschiedlichen Herstellern in ein Netzwerk. Dies erleichtert die Entwicklung und Implementierung von Anwendungen für Nutzfahrzeuge und -maschinen, die auf SAE J1939 basieren.

[https://www.csselectronics.com/pages/j1939-explained-simple-intro-tutorial](https://www.csselectronics.com/pages/j1939-explained-simple-intro-tutorial)

![](https://cdn.shopify.com/s/files/1/0579/8032/1980/files/j1939-osi-model-7-layer-standards-sae.svg)

## ISO/OSI-Referenzmodell und ISO 11783

[https://www.csselectronics.com/pages/isobus-introduction-tutorial-iso-11783](https://www.csselectronics.com/pages/isobus-introduction-tutorial-iso-11783)

![](https://cdn.shopify.com/s/files/1/0579/8032/1980/files/isobus-osi-model-layer-iso-11783.svg)