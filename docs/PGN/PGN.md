# PGN

```{index} single: PGN
```

### **Kapitel: Was ist eine PGN (Parameter Group Number)?**

#### **Einführung**
In der Welt der mobilen Maschinen, insbesondere in der Land- und Bautechnik, spielt die Kommunikation zwischen verschiedenen elektronischen Steuergeräten (ECUs) eine zentrale Rolle. Um diese Kommunikation effizient und standardisiert zu gestalten, wurde das **ISOBUS-Protokoll** entwickelt, das auf dem **CAN-Bus (Controller Area Network)** basiert. Ein zentrales Element dieses Protokolls ist die **Parameter Group Number (PGN)**, die eine eindeutige Identifikation von Nachrichten ermöglicht. Dieses Kapitel erklärt, was eine PGN ist, wie sie aufgebaut ist und welche Rolle sie in der ISOBUS-Kommunikation spielt.

---

#### **Definition einer PGN**
Eine **Parameter Group Number (PGN)** ist eine 24-Bit-Zahl, die eine spezifische Nachricht oder einen Datensatz innerhalb des ISOBUS-Netzwerks identifiziert. Jede PGN repräsentiert eine logische Gruppierung von Daten, die gemeinsam übertragen werden. Diese Daten können beispielsweise Informationen über die Geschwindigkeit einer Maschine, den Zustand eines Anbaugeräts oder Steuerbefehle für ein Hydrauliksystem enthalten.

---

#### **Aufbau einer PGN**
Eine PGN besteht aus drei Hauptkomponenten:
1. **Extended Data Page (EDP)**: 1 Bit  
   - Gibt an, ob die PGN auf der erweiterten Datenseite (Extended Data Page) liegt.  
   - Wert: 0 (Standard) oder 1 (erweitert).  

2. **Data Page (DP)**: 1 Bit  
   - Hilft bei der Unterscheidung zwischen verschiedenen Nachrichtentypen.  
   - Wert: 0 (Standard) oder 1 (alternativ).  

3. **PF (Parameter Group Format) und PS (Parameter Group Specific)**:  
   - **PF (8 Bits)**: Definiert das Format der PGN.  
     - Wenn PF < 240, dann ist die PGN eine **Peer-to-Peer-Nachricht** (zielgerichtet).  
     - Wenn PF ≥ 240, dann ist die PGN eine **Broadcast-Nachricht** (an alle Geräte gerichtet).  
   - **PS (8 Bits)**:  
     - Bei PF < 240: PS gibt die Zieladresse (Destination Address) an.  
     - Bei PF ≥ 240: PS wird zur weiteren Spezifikation der PGN verwendet.  

Die vollständige PGN wird durch die Kombination von EDP, DP, PF und PS gebildet und als 24-Bit-Wert dargestellt.

---

#### **Beispiel einer PGN**
Nehmen wir die PGN **65096 (Wheel-based Speed and Distance – WBSD)** als Beispiel:
- **EDP**: 0  
- **DP**: 0  
- **PF**: 254  
- **PS**: 72  
- **PGN**: 0x00FE48 (hexadezimal) oder 65096 (dezimal).  

Diese PGN identifiziert eine Nachricht, die Informationen über die radbasierte Geschwindigkeit und Entfernung einer Maschine enthält.

---

#### **Rolle der PGN in der ISOBUS-Kommunikation**
1. **Identifikation von Nachrichten**:  
   Jede PGN identifiziert eindeutig eine bestimmte Nachricht oder einen Datensatz. Dadurch wissen die ECUs, welche Art von Daten sie empfangen oder senden.  

2. **Standardisierung**:  
   Durch die Verwendung von PGNs wird die Kommunikation zwischen Geräten verschiedener Hersteller standardisiert. Dies ermöglicht die Interoperabilität von Traktoren, Anbaugeräten und anderen Maschinen.  

3. **Effiziente Datenübertragung**:  
   PGNs gruppieren verwandte Daten in einer einzigen Nachricht, was die Effizienz der Datenübertragung erhöht und den Netzwerkverkehr reduziert.  

4. **Flexibilität**:  
   PGNs können sowohl zielgerichtete (Peer-to-Peer) als auch allgemeine (Broadcast) Nachrichten darstellen, was die Flexibilität der Kommunikation erhöht.  

---

#### **Arten von PGNs**
1. **Broadcast-PGNs**:  
   - Werden an alle Geräte im Netzwerk gesendet.  
   - Beispiele: Geschwindigkeit, Drehzahl, Betriebsstunden.  

2. **Peer-to-Peer-PGNs**:  
   - Werden an ein spezifisches Gerät gesendet.  
   - Beispiele: Steuerbefehle für ein bestimmtes Anbaugerät.  

3. **Proprietäre PGNs**:  
   - Werden von Herstellern für spezifische Anwendungen definiert.  
   - Sind nicht Teil des ISOBUS-Standards.  

---

#### **Zusammenfassung**
Eine **Parameter Group Number (PGN)** ist ein zentrales Element der ISOBUS-Kommunikation. Sie identifiziert eindeutig eine Nachricht oder einen Datensatz und ermöglicht so die standardisierte und effiziente Übertragung von Daten zwischen verschiedenen elektronischen Steuergeräten. Durch die Verwendung von PGNs wird die Interoperabilität zwischen Geräten verschiedener Hersteller gewährleistet, was insbesondere in der Land- und Bautechnik von großer Bedeutung ist.  

---

#  PGN Liste

```{toctree}
:caption: 'PGN'
PGN_129026
PGN_61474
PGN_65096
PGN_61474
PGN_65091
PGN_65092
PGN_65093
PGN_65094
PGN_65096
PGN_65097
PGN_65256
PGN_65267
```