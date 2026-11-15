# NMEA 2000 Fast Packet

Fast Packet ist das Verfahren von NMEA 2000, Nachrichten mit **9 bis 223 Datenbyte** über CAN zu senden. Es ist **nicht** das ISOBUS-Transportprotokoll (TP/ETP aus ISO 11783-3), sondern eine einfachere Folge von CAN-Rahmen. Die Seite beschreibt das Protokoll und den Stand im CCI-Stack, auf dem die logiBUS-Firmware aufsetzt.

!!! warning "Stand heute: Fast Packet ist in logiBUS nicht aktiv"
    In der Konfiguration des Stacks (`IsoConf.h`) ist `ISO_NMEA_FPT` auskommentiert. Die Datei `IsoDataM.c` wird zwar übersetzt, ihr Inhalt steckt aber komplett in `#if defined(ISO_NMEA_FPT)`.

    - **Senden:** Eine PGN im Bereich `0x1F000` bis `0x1FFFF` mit mehr als 8 Byte geht an ein **Einzelziel** über das ISOBUS-Transportprotokoll (RTS/CTS). An **global** wird nichts gesendet, der Stack liefert `E_NOACT`.
    - **Empfangen:** Fast-Packet-Rahmen werden nicht zusammengesetzt. Die Anwendung sieht höchstens einzelne 8-Byte-Rahmen.
    - Ob und wann Fast Packet aktiviert wird, ist nicht entschieden.

Die Angaben zum Stack stammen aus dem Quelltext des CCI-Stacks (`IsoDataM.c`, `IsoMnDl.c`, `IsoCore.c`, `IsoDD.c`, `IsoConf.h`). Hardware wurde nicht geprüft.

## Das Protokoll

Fast Packet gilt für PGNs mit 9 bis 223 Byte. NMEA 2000 nutzt es für PGNs im Bereich `0x1F000` bis `0x1FFFF` (Data Page 1, PDU2) und auch für adressierte PDU1-PGNs auf Data Page 1, zum Beispiel 126208 (`0x1ED00`), 126464 (`0x1EE00`) und 126720 (`0x1EF00`, herstellereigen, adressiert). Der CCI-Stack behandelt nur den Bereich `0x1F000` bis `0x1FFFF` (siehe unten).

- **Kein Handshake:** Es gibt kein RTS/CTS, keine Bestätigung und keine Wiederholung. Der Sender schickt eine Folge von CAN-Rahmen mit derselben PGN.
- **Byte 0 jedes Rahmens:** Bit 7 bis 5 sind der **Sequenzzähler** (0 bis 7, pro Nachricht um 1 erhöht), Bit 4 bis 0 der **Rahmenzähler** (0 bis 31).
- **Rahmen 0:** Byte 1 ist die Gesamtlänge (höchstens 223), Byte 2 bis 7 sind die Datenbytes 1 bis 6.
- **Rahmen 1 bis n:** Byte 1 bis 7 sind je 7 weitere Datenbytes. Der letzte Rahmen wird mit `0xFF` aufgefüllt.
- **Maximum:** 6 + 31 × 7 = **223 Byte** (im Stack `BLOCK_SIZE_NMEA`).

Ob eine PGN als Fast Packet oder als einzelner Rahmen gesendet wird, steht **nicht im Rahmen**. Der Empfänger muss es aus einer PGN-Liste wissen.

## Vergleich mit dem ISOBUS-Transportprotokoll

| | ISOBUS TP | NMEA 2000 Fast Packet |
| --- | --- | --- |
| Länge | bis 1785 Byte (TP), mehr mit ETP | bis 223 Byte |
| Handshake | BAM (global) oder RTS/CTS (Einzelziel) | keiner |
| Bestätigung, Wiederholung | bei RTS/CTS durch den Empfänger | keine |
| PGN-Bereich | beliebige PGNs | im CCI-Stack nur `0x1F000` bis `0x1FFFF` |
| Erkennung beim Empfänger | durch das Protokoll selbst | nur aus der PGN-Liste |

## Einordnung: NMEA 2000, J1939 und ISOBUS

Die drei Welten teilen sich dasselbe Grundgerüst. Die Angaben mit Fundstelle sind aus den Normen belegt, mit „Einschätzung“ gekennzeichnete Angaben nicht. Von NMEA 2000 selbst lagen das Main Document und die Anhänge A und B nicht vor, nur die IEC-Norm dazu.

- **Gemeinsame Basis:** ISO 11783 (ISOBUS) beruht auf CAN nach ISO 11898-1 und in Teilen auf SAE J1939. Beide wurden gemeinsam entwickelt (ISO 11783-3:2026 und ISO 11783-1:2017, jeweils Einleitung). Alle drei nutzen den 29-Bit-Identifier mit Priorität, Data Page, PDU Format, PDU Specific und Source Address. Bei PDU Format unter 240 (PDU1) ist PDU Specific die Zieladresse, ab 240 (PDU2) die Group Extension und damit eine Rundsendung (ISO 11783-3:2026, 5.3.2 und 5.3.3; SAE J1939-21, Entwurf 2001, 5.3).
- **NAME und Address Claim:** ISOBUS definiert sie in ISO 11783-5:2019, 4.3.2 und 4.4.2, das Gegenstück bei SAE ist J1939-81. Die Industry Group ist 2 für Agrar und Forst und 4 für Marine (SAE J1939, Appendix, Tabelle B1).
- **NMEA 2000 baut auf ISO 11783 auf:** Nach IEC 61162-3:2008 (DIN EN 61162-3:2009, mit A1 und A2) besteht die Sicherungsschicht aus ISO 11783-3 plus Zusatzanforderungen aus dem NMEA 2000 Main Document, Abschnitt 3 (Abschnitt 5 der IEC-Norm). Das Netzwerkmanagement mit Address Claim folgt ISO 11783-5:2001 (Abschnitt 7). SAE J1939 steht dort nur in der Literaturliste. Geräte der Stufe B unterstützen Address Claim, ISO Request und Produktinformation (3.1.12), Geräte der Stufe A zusätzlich das ISO-Transportprotokoll (3.1.11).
- **Gemeinsames Nummernsystem:** PGNs, Adressen, NAME-Felder und Hersteller-Codes werden in der Datenbank isobus.net gepflegt und mit dem SAE-J1939-Komitee abgeglichen (ISO 11783-1:2017, Kapitel 4 und Anhang). Die NMEA-Navigations-PGNs wie 129029 (`0x1F805`) liegen im gleichen Nummernraum. Einschätzung: Es gibt keine Doppelbelegung.
- **Transport bis 1785 Byte:** Das Transportprotokoll (TP.CM auf PGN 60416 = `0xEC00`, TP.DT auf PGN 60160 = `0xEB00`, mit BAM und RTS/CTS) ist zwischen SAE J1939-21 und ISO 11783-3 abgestimmt (ISO 11783-3:2026, 5.9.1 und 5.9.4). Das erweiterte Transportprotokoll (ETP, über 1785 Byte) gibt es nur bei ISO 11783 (5.10).
- **Fast Packet ist NMEA-eigen:** In den vorliegenden J1939-Dokumenten (J1939-21, Entwürfe 1998 und 2001) kommt Fast Packet nicht vor. Auch die IEC-Norm beschreibt den Rahmen nicht selbst, sie verweist dafür auf das NMEA 2000 Main Document (Abschnitt 3), das nicht vorlag. Die Rahmenbeschreibung oben beruht deshalb auf dem Stack-Code und auf allgemein dokumentiertem Fachwissen. Belegt ist dazu in IEC 61162-3, Anhang A.4.1 (informativ): Zusammengesetzt wird nur anhand von Sequenz- und Rahmennummer, bei einer Unstimmigkeit wird abgebrochen, und neu begonnen wird erst bei Rahmenzähler 0. Das passt zu „kein Handshake, keine Wiederholung“. Nach A.4.2 zählt der Fast-Packet-Sequenzzähler einer PGN nicht hoch, wenn diese PGN über das ISO-Transportprotokoll gesendet wird.
- **Welche PGNs Fast Packet sind:** Das ist eine Eigenschaft der einzelnen PGN-Definition in NMEA 2000 Appendix B (nicht vorliegend, IEC 61162-3, Abschnitt 8). Eine eigene Liste gibt es in der IEC-Norm nicht. Ein Kennzeichen im Identifier oder im Rahmen gibt es nicht. Die Liste weiter unten ist der Stand des Stacks und nicht gegen Appendix B abgeglichen. Einschätzung nach Community-Quellen (nicht belegt): Auch 126208, 126464 und 126720 sowie der proprietäre Bereich `0x1FF00` bis `0x1FFFF` sind Fast Packet, und in der Liste fehlen weitere PGNs (siehe den Abschnitt „Weitere PGNs, die als Fast Packet gelten könnten“).

### Fast Packet auf dem ISOBUS

ISOBUS verlangt Fast Packet an einer Stelle selbst: Nach ISO 11783-7:2022, 7.1 verwenden Steuergeräte für Navigation und Position die Nachrichten aus IEC 61162-3 (NMEA 2000), und mehrteilige Nachrichten laufen dabei über NMEA Fast Packet. Abschnitt 7.2 übernimmt auch die Parameter. Bevorzugt werden unter anderem GNSS Position Data, Position Delta High Precision Rapid Update, COG/SOG Rapid Update und GNSS Pseudorange Noise Statistics. Ein GNSS-Empfänger sendet seine mehrteiligen Positionsnachrichten (zum Beispiel `0x1F805`) also oft als Fast Packet auf den ISOBUS.

Folge für logiBUS: `0x1F805` steht in der Liste unten. Solange `ISO_NMEA_FPT` nicht aktiv ist, setzt der Stack solche Nachrichten nicht zusammen, die Anwendung sieht nur einzelne 8-Byte-Rahmen.

### ISOBUS- und NMEA-2000-Geräte am selben Bus

- Das ist nach ISO 11783-7, 7.1 für GNSS-Empfänger der Normalfall.
- Alle Teilnehmer nehmen am selben Address Claim teil, die Adresse wird über den NAME entschieden (ISO 11783-5, 4.4.2). Die Industry Group im NAME zeigt die Herkunft, verhindert aber keine Adresskonflikte. Beide Seiten brauchen dieselbe Bitrate (250 kbit/s bei ISOBUS, ISO 11783-2; für NMEA 2000 ist das Fachwissen, die IEC-Norm verweist dafür nur auf das Main Document).
- Verwandt heißt nicht austauschbar: Nach IEC 61162-3, Abschnitt 9 dürfen Geräte, die nur nach ISO 11783, NMEA 2000 oder SAE J1939 ausgelegt und nicht nach dieser Norm geprüft sind, im Normalbetrieb nicht angeschlossen werden.
- Der Bereich `0x1F000` bis `0x1FFFF` liegt auf Data Page 1, PDU2. Ihn nutzen sowohl NMEA 2000 als auch ISOBUS. Neu in ISO 11783-3:2026 ist Proprietary B auch auf Data Page 1 (PF 255, also `0x1FFxx`, Abschnitt 5.4.6.4).
- Einschätzung für die Praxis: Ein Empfänger muss pro PGN wissen, ob sie über Fast Packet oder über TP kommt. Bei mehreren GNSS-Quellen auf einem Bus sollte er sich an Quelladresse oder NAME binden.

Fundstellen: ISO 11783-1:2017, -2, -3:2026, -5:2019 und -7:2022, IEC 61162-3:2008 mit A1:2010 und A2:2015, SAE J1939-21 (Entwurf 2001) und J1939-81 (Entwurf 2001). Die Abschnittsnummern der J1939-Entwürfe können von der aktuellen Ausgabe abweichen.

## Verhalten des Stacks, wenn `ISO_NMEA_FPT` aktiv wäre

- **Senden:** `iso_TlNmeaFPTSend()` sendet bis 223 Byte, für alle PGNs von `0x1F000` bis `0x1FFFF` mit mehr als 8 Byte, **auch bei einem Einzelziel** (dann also Fast Packet statt TP). Die PGNs dieses Bereichs sind PDU2 ohne Zieladresse im CAN-Identifier: Der Stack speichert das Einzelziel zwar, die Rahmen gehen aber **immer als Rundsendung** auf den Bus.
- **Empfangen:** `iso_TlNmeaFptReceive()` setzt die Rahmen zusammen und reicht das Ergebnis wie einen TP-Empfang an die Anwendungsschicht weiter. In 4diac käme es bei `AlPgnRxNew_TP` an.
- **Nur bekannte Absender:** Fast Packet wird nur von Absendern angenommen, die als Netzwerkteilnehmer bekannt sind, also eine Adresse beansprucht haben. Sonst verwirft der Stack die Nachricht („External CF not logged -> ignore message").
- **Einordnung nach fester Liste:** `iso_TlNmeaCheckSingleFrame()` entscheidet anhand der Liste unten. Alle PGNs außerhalb der Liste gelten als einzelner Rahmen.
- **PGNs im Bereich, aber nicht in der Liste:** Der Sender entscheidet nur nach dem Bereich und nach der Länge (mehr als 8 Byte), er prüft die Liste nicht. Der Empfänger ordnet eine PGN, die nicht in der Liste steht, als einzelnen Rahmen ein und setzt sie nicht zusammen: Die Anwendung bekommt jeden 8-Byte-Rahmen einzeln. Beispiel: PGN 130569 (`0x1FE09`) steht nicht in der Liste. Zwei Geräte mit diesem Stack verstehen sich bei einer solchen PGN nicht, solange sie nicht in die Liste eingetragen ist (Abhilfe: das Makro unten, auch für Standard-PGNs).
- **Eigene PGNs:** Das Makro `PGNS_FPP_PROP_CODE` in `IsoConf.h` erweitert die Liste um herstellereigene PGNs. Beispiel aus dem Quelltext: `#define PGNS_FPP_PROP_CODE case 0x1FF00UL:`. Das braucht eine Neuübersetzung der Firmware.
- **Bekannte Einschränkung, adressierte PDU1-PGNs:** Fast-Packet-PGNs auf Data Page 1 im PDU1-Bereich (126208, 126464, 126720) laufen im Stack nie über Fast Packet. Beim Senden gehen sie über das ISO-Transportprotokoll, beim Empfang kommen sie nur als einzelner Rahmen oder per TP an. Der Stack markiert 126208 und 126464 als nicht für Einzelrahmen (`SF NO`) und reicht 126720 als ISO-Nachricht weiter (`PGN_PDU1_PropA2`). Die übrigen empfangenen PDU1-NMEA-PGNs sind nicht unterstützt.
- **4diac-Bausteine:** Es ändert sich nichts. Gesendet wird mit `AlPgnTxNew_TP` (`u16DaSize` höchstens 223, PGN `0x1F…`), empfangen mit `AlPgnRxNew_TP`.

## Feste Liste der Fast-Packet-PGNs

Die Liste hat **73 Einträge**. Die Namen sind die Makronamen aus dem Header des Stacks in der dortigen Schreibweise (zum Beispiel `PRODUKTINFORMATION`), keine offiziellen NMEA-Namen.

!!! note "Stand des Stacks, nicht abgeglichen"
    Die Liste entspricht dem CCI-Stack (letzte Änderung laut Kopf der Datei: 06.12.2024). Sie ist **nicht** mit der aktuellen NMEA-2000-PGN-Liste abgeglichen. Neuere Fast-Packet-PGNs können fehlen.

| PGN (hex) | PGN (dez) | Name im Stack | Name laut isobus.net |
| --- | ---: | --- | --- |
| `0x1F014` | 126996 | `PRODUKTINFORMATION` | Product Information |
| `0x1F016` | 126998 | `CONFIGURATIONINFORMATION` | Configuration Information |
| `0x1F105` | 127237 | `HEADING_TRACKCONTROL` | Heading/Track Control |
| `0x1F201` | 127489 | `ENGINE_PARA_DYNAMIC` | Engine Parameters, Dynamic |
| `0x1F208` | 127496 | `TRIP_PARAMETERS_VESSEL` | Trip Parameters, Vessel |
| `0x1F209` | 127497 | `TRIP_PARAMETERS_ENGINE` | Trip Parameters, Small Craft |
| `0x1F20A` | 127498 | `ENGINE_PARAMETERS_STATIC` | Engine Parameters, Static |
| `0x1F20F` | 127503 | `AC_INPUT_STATUS` | AC Input Status |
| `0x1F210` | 127504 | `AC_OUTPUT_STATUS` | AC Output Status |
| `0x1F212` | 127506 | `DCDETAILED_STATUS` | DC Detailed Status |
| `0x1F213` | 127507 | `CHARGER_STATUS` | Charger Status |
| `0x1F215` | 127509 | `INVERTER_STATUS` | Inverter Status |
| `0x1F216` | 127510 | `CHARGER_CONFIG_STATUS` | Charger Configuration Status |
| `0x1F217` | 127511 | `INVERTER_CONFIG_STATUS` | Inverter Configuration Status |
| `0x1F218` | 127512 | `AGS_CONFIG_STATUS` | AGS Configuration Status |
| `0x1F219` | 127513 | `BATTERY_CONFIG_STATUS` | Battery Configuration Status |
| `0x1F21A` | 127514 | `AGS_STATUS` | AGS Status |
| `0x1F513` | 128275 | `DISTANCED_LOG` | Distance Log |
| `0x1F608` | 128520 | `TRACKED_TARGET_DATA` | Tracked Target Data |
| `0x1F805` | 129029 | `GNSS_POSITION_DATA` | GNSS Position Data |
| `0x1F80E` | 129038 | `AIS_CLASSA_POSITION_REPORT` | AIS Class A Position Report |
| `0x1F80F` | 129039 | `AIS_CLASSB_POSITION_REPORT` | AIS Class B Position Report |
| `0x1F810` | 129040 | `AIS_CLASSB_EXTPOS_REPORT` | AIS Class B Extended Position Report |
| `0x1F814` | 129044 | `DATUM` | Datum |
| `0x1F815` | 129045 | `USER_DATUM_SETTINGS` | User Datum Settings |
| `0x1F904` | 129284 | `NAVIGATION_DATA` | Navigation Data |
| `0x1F905` | 129285 | `NAVIGATION_ROUTEWP_INFO` | Navigation - Route/WP information |
| `0x1F915` | 129301 | `TIME_TOFROM_MARK` | Time to/from Mark |
| `0x1F916` | 129302 | `BEARING_DIST_BET_TWO_MARKS` | Bearing and Distance between two Marks |
| `0x1FA02` | 129538 | `GNSS_CONTROL_STATUS` | GNSS Control Status |
| `0x1FA04` | 129540 | `GNSS_SATS_IN_VIEW` | GNSS Sats in View |
| `0x1FA05` | 129541 | `ALMANAC_DATA` | GPS Almanac Data |
| `0x1FA06` | 129542 | `GNSS_PSEUDOOR_NOISE_STATIST` | GNSS Pseudorange Noise Statistics |
| `0x1FA09` | 129545 | `GNSS_RAIM_OUTPUT` | GNSS RAIM Output |
| `0x1FA0B` | 129547 | `GNSS_PSEUDOOR_ERROR_STATIST` | GNSS Pseudorange Error Statistics |
| `0x1FA0D` | 129549 | `DGNSS_CORRECTIONS` | DGNSS Corrections |
| `0x1FA0F` | 129551 | `GNSS_DIFF_CORR_RECEIVE_SIGN` | GNSS Differential Correction Receiver Signal |
| `0x1FA14` | 129556 | `GLONASS_ALMANAC_DATA` | GLONASS Almanac Data |
| `0x1FB00` | 129792 | `AIS_DGNSS_BROADC_BIN_MESSAG` | AIS DGNSS Broadcast Binary Message |
| `0x1FB01` | 129793 | `AIS_UTC_AND_DATE_REPORT` | AIS UTC and Date Report |
| `0x1FB02` | 129794 | `AIS_CLASS_A_STAT_VOYAGE_DAT` | AIS Class A Static and Voyage Related Data |
| `0x1FB03` | 129795 | `AIS_ADDRESSED_BINARY_MESSAG` | AIS Addressed Binary Message |
| `0x1FB04` | 129796 | `AIS_ACKNOWLEDGE` | AIS Acknowledge |
| `0x1FB05` | 129797 | `AIS_BINARY_BROADCAST_MESSAG` | AIS Binary Broadcast Message |
| `0x1FB06` | 129798 | `AIS_SAR_AIRCRAFT_POS_REPORT` | AIS SAR Aircraft Position Report |
| `0x1FB07` | 129799 | `RADIO_FREQUENCY_MODE_POWER` | Radio Frequency/Mode/Power |
| `0x1FB08` | 129800 | `AIS_UTC_DATE_INQUIRY` | AIS UTC/Date Inquiry |
| `0x1FB09` | 129801 | `AIS_ADDRESS_SAFE_REL_MESSAG` | AIS Addressed Safety Related Message |
| `0x1FB0A` | 129802 | `AIS_SAFE_RELD_BROADC_MESSAG` | AIS Safety Related Broadcast Message |
| `0x1FB0B` | 129803 | `AIS_INTERROGATION` | AIS Interrogation |
| `0x1FB0C` | 129804 | `AIS_ASSIGNMENT_MODE_COMMAND` | AIS Assignment Mode Command |
| `0x1FB10` | 129808 | `DSC_CALL_INFORMATION` | DSC Call Information |
| `0x1FC04` | 130052 | `LORAN_C_TD_DATA` | Loran-C TD Data |
| `0x1FC05` | 130053 | `LORAN_C_RANGE_DATA` | Loran-C Range Data |
| `0x1FC06` | 130054 | `LORAN_C_SIGNAL_DATA` | Loran-C Signal Data |
| `0x1FC10` | 130064 | `ROUTE_WP_SERVICE_DB_LIST` | Route and WP Service - Database List |
| `0x1FC11` | 130065 | `ROUTE_WP_SERVICE_ROUTE_LIST` | Route and WP Service - Route List |
| `0x1FC12` | 130066 | `ROUTE_WP_SERVICE_WP_ATTRIB` | Route and WP Service - Route/WP-List Attributes |
| `0x1FC13` | 130067 | `ROUTE_WP_SERVICE_WP_NAM_POS` | Route and WP Service - Route - WP Name & Position |
| `0x1FC14` | 130068 | `ROUTE_WP_SERVICE_QP_NAME` | Route and WP Service - Route - WP Name |
| `0x1FC15` | 130069 | `ROUTE_WP_SERVICE_XTE_LIMIT` | Route and WP Service - XTE Limit & Navigation Method |
| `0x1FC16` | 130070 | `ROUTE_WP_SERVICE_WP_COMMENT` | Route and WP Service - WP Comment |
| `0x1FC17` | 130071 | `ROUTE_WP_SERVICE_ROUTE_COM` | Route and WP Service - Route Comment |
| `0x1FC18` | 130072 | `ROUTE_WP_SERVICE_DB_COMMENT` | Route and WP Service - Database Comment |
| `0x1FC19` | 130073 | `ROUTE_WP_SERVICE_RADIU_TURN` | Route and WP Service - Radius of Turn |
| `0x1FC1A` | 130074 | `ROUTE_WP_SERVICE_WP_LIS_NAM` | Route and WP Service - WP List - WP Name & Position |
| `0x1FD10` | 130320 | `TIDE_STATION_DATA` | Tide Station Data |
| `0x1FD11` | 130321 | `SALINITY_STATION_DATA` | Salinity Station Data |
| `0x1FD12` | 130322 | `CURRENT_STATION_DATA` | Current Station Data |
| `0x1FD13` | 130323 | `METEOROLOGICAL_STATION_DATA` | Meteorological Station Data |
| `0x1FD14` | 130324 | `MOORED_BUOY_STATION_DATA` | Moored Buoy Station Data |
| `0x1FE11` | 130577 | `DIRECTION_DATA` | Direction Data |
| `0x1FE12` | 130578 | `VESSEL_SPEED_COMPONENTS` | Vessel Speed Components |

Zu den Namen: Die Spalte „Name laut isobus.net“ stammt aus dem Export der Datenbank isobus.net vom 14.11.2026 (Dokument „NMEA 2000“). Alle 73 PGNs der Liste kommen dort vor. Zwei Namen im Stack weichen ab:

- 127497 (`0x1F209`) heißt im Stack `TRIP_PARAMETERS_ENGINE`, im Export „Trip Parameters, Small Craft“.
- 130068 (`0x1FC14`) heißt im Stack `ROUTE_WP_SERVICE_QP_NAME`, im Export „Route and WP Service - Route - WP Name“.

Der Export enthält keine Angabe, ob eine PGN Fast Packet ist.

## Weitere PGNs, die als Fast Packet gelten könnten

!!! warning "Hinweis ohne Norm-Beleg"
    Dieser Abschnitt stützt sich auf das Open-Source-Projekt canboat (Version 10.0.0). Dessen Daten entstanden durch Reverse Engineering und sind laut dem Projekt selbst nicht verbindlich, viele Einträge sind als unvollständig markiert. Eine Bestätigung durch NMEA 2000 Appendix B fehlt.

Alle 73 PGNs der Stack-Liste führt canboat ebenfalls als Fast Packet. Dazu führt canboat 49 weitere PGNs als Fast Packet, die im isobus.net-Export als NMEA-2000-PGNs stehen, aber nicht in der Stack-Liste. Die Namen stammen aus dem Export:

| PGN (hex) | PGN (dez) | Name laut isobus.net |
| --- | ---: | --- |
| `0x1ED00` | 126208 | NMEA - Request/Command/Acknowledge group function |
| `0x1EE00` | 126464 | PGN List - Transmit/Receive PGN's group function |
| `0x1F007` | 126983 | Alert |
| `0x1F008` | 126984 | Alert Response |
| `0x1F009` | 126985 | Alert Text |
| `0x1F00A` | 126986 | Alert Configuration |
| `0x1F00B` | 126987 | Alert Threshold |
| `0x1F00C` | 126988 | Alert Value |
| `0x1F101` | 127233 | Man Overboard Notification (MOB) |
| `0x1F202` | 127490 | Electric Drive Status, Dynamic |
| `0x1F203` | 127491 | Electric Energy Storage Status, Dynamic |
| `0x1F206` | 127494 | Electric Drive Information |
| `0x1F207` | 127495 | Electric Energy Storage Information |
| `0x1F61A` | 128538 | Elevator Car Status |
| `0x1F811` | 129041 | AIS Aids to Navigation (AtoN) Report |
| `0x1FB0D` | 129805 | AIS Data Link Management Message |
| `0x1FB0E` | 129806 | AIS Channel Management |
| `0x1FB0F` | 129807 | AIS Class B Group Assignment |
| `0x1FB11` | 129809 | AIS Class B "CS" Static Data Report, Part A |
| `0x1FB12` | 129810 | AIS Class B "CS" Static Data Report, Part B |
| `0x1FB13` | 129811 | AIS Single Slot Binary Message - DEPRECATED |
| `0x1FB14` | 129812 | AIS Multi Slot Binary Message - DEPRECATED |
| `0x1FB15` | 129813 | AIS Long-Range Broadcast Message |
| `0x1FB16` | 129814 | AIS Single Slot Binary Message |
| `0x1FB17` | 129815 | AIS Multi Slot Binary Message |
| `0x1FB18` | 129816 | AIS Acknowledge |
| `0x1FC0C` | 130060 | Label |
| `0x1FC0D` | 130061 | Channel Source Configuration |
| `0x1FD1A` | 130330 | Lighting System Settings |
| `0x1FE01` | 130561 | Lighting Zone |
| `0x1FE02` | 130562 | Lighting Scene |
| `0x1FE03` | 130563 | Lighting Device |
| `0x1FE04` | 130564 | Lighting Device Enumeration |
| `0x1FE05` | 130565 | Lighting Color Sequence |
| `0x1FE06` | 130566 | Lighting Program |
| `0x1FE07` | 130567 | Watermaker Input Setting and Status |
| `0x1FE08` | 130568 | Entertainment - Diagnostic Status |
| `0x1FE09` | 130569 | Entertainment - Current File and Status |
| `0x1FE0A` | 130570 | Entertainment - Library Data File |
| `0x1FE0B` | 130571 | Entertainment - Library Data Group |
| `0x1FE0C` | 130572 | Entertainment - Library Data Search |
| `0x1FE0D` | 130573 | Entertainment - Supported Source Data |
| `0x1FE0E` | 130574 | Entertainment - Supported Zone Data |
| `0x1FE0F` | 130575 | Entertainment - Parental Control Status |
| `0x1FE14` | 130580 | Entertainment - System Configuration Status |
| `0x1FE15` | 130581 | Entertainment - Zone Configuration Status - DEPRECATED |
| `0x1FE17` | 130583 | Entertainment - Available Audio EQ Presets |
| `0x1FE18` | 130584 | Entertainment - Bluetooth Devices |
| `0x1FE1A` | 130586 | Entertainment - Zone Configuration Status |

Außerdem führt canboat den herstellereigenen Bereich `0x1FF00` bis `0x1FFFF` (130816 bis 131071) als Fast Packet, ebenso 126720 (`0x1EF00`, herstellereigen, adressiert). Im Stack gilt: Der Bereich `0x1FF00` bis `0x1FFFF` liegt im Sendebereich, ist aber nicht in der Liste, verhält sich also wie die oben beschriebenen PGNs außerhalb der Liste (Abhilfe `PGNS_FPP_PROP_CODE`). 126720 fällt unter die oben genannte Einschränkung für adressierte PDU1-PGNs.
