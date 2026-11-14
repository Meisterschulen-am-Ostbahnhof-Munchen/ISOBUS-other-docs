# NMEA 2000 Fast Packet

Fast Packet ist das Verfahren von NMEA 2000, Nachrichten mit **9 bis 223 Datenbyte** über CAN zu senden. Es ist **nicht** das ISOBUS-Transportprotokoll (TP/ETP aus ISO 11783-3), sondern eine einfachere Folge von CAN-Rahmen. Die Seite beschreibt das Protokoll und den Stand im CCI-Stack, auf dem die logiBUS-Firmware aufsetzt.

!!! warning "Stand heute: Fast Packet ist in logiBUS nicht aktiv"
    In der Konfiguration des Stacks (`IsoConf.h`) ist `ISO_NMEA_FPT` auskommentiert. Die Datei `IsoDataM.c` wird zwar übersetzt, ihr Inhalt steckt aber komplett in `#if defined(ISO_NMEA_FPT)`.

    - **Senden:** Eine PGN im Bereich `0x1F000` bis `0x1FFFF` mit mehr als 8 Byte geht an ein **Einzelziel** über das ISOBUS-Transportprotokoll (RTS/CTS). An **global** wird nichts gesendet, der Stack liefert `E_NOACT`.
    - **Empfangen:** Fast-Packet-Rahmen werden nicht zusammengesetzt. Die Anwendung sieht höchstens einzelne 8-Byte-Rahmen.
    - Ob und wann Fast Packet aktiviert wird, ist nicht entschieden.

Die Angaben zum Stack stammen aus dem Quelltext des CCI-Stacks (`IsoDataM.c`, `IsoMnDl.c`, `IsoCore.c`, `IsoDD.c`, `IsoConf.h`). Hardware wurde nicht geprüft.

## Das Protokoll

Fast Packet gilt für PGNs im NMEA-2000-Bereich (Data Page 1, `0x1F000` bis `0x1FFFF`) mit 9 bis 223 Byte.

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
| PGN-Bereich | beliebige PGNs | nur `0x1F000` bis `0x1FFFF` |
| Erkennung beim Empfänger | durch das Protokoll selbst | nur aus der PGN-Liste |

## Verhalten des Stacks, wenn `ISO_NMEA_FPT` aktiv wäre

- **Senden:** `iso_TlNmeaFPTSend()` sendet bis 223 Byte, für alle PGNs von `0x1F000` bis `0x1FFFF` mit mehr als 8 Byte, **auch an Einzelziele** (dann also Fast Packet statt TP).
- **Empfangen:** `iso_TlNmeaFptReceive()` setzt die Rahmen zusammen und reicht das Ergebnis wie einen TP-Empfang an die Anwendungsschicht weiter. In 4diac käme es bei `AlPgnRxNew_TP` an.
- **Nur bekannte Absender:** Fast Packet wird nur von Absendern angenommen, die als Netzwerkteilnehmer bekannt sind, also eine Adresse beansprucht haben. Sonst verwirft der Stack die Nachricht („External CF not logged -> ignore message").
- **Einordnung nach fester Liste:** `iso_TlNmeaCheckSingleFrame()` entscheidet anhand der Liste unten. Alle PGNs außerhalb der Liste gelten als einzelner Rahmen.
- **Eigene PGNs:** Das Makro `PGNS_FPP_PROP_CODE` in `IsoConf.h` erweitert die Liste um herstellereigene PGNs. Beispiel aus dem Quelltext: `#define PGNS_FPP_PROP_CODE case 0x1FF00UL:`. Das braucht eine Neuübersetzung der Firmware.
- **4diac-Bausteine:** Es ändert sich nichts. Gesendet wird mit `AlPgnTxNew_TP` (`u16DaSize` höchstens 223, PGN `0x1F…`), empfangen mit `AlPgnRxNew_TP`.

## Feste Liste der Fast-Packet-PGNs

Die Liste hat **73 Einträge**. Die Namen sind die Makronamen aus dem Header des Stacks in der dortigen Schreibweise (zum Beispiel `PRODUKTINFORMATION`), keine offiziellen NMEA-Namen.

!!! note "Stand des Stacks, nicht abgeglichen"
    Die Liste entspricht dem CCI-Stack (letzte Änderung laut Kopf der Datei: 06.12.2024). Sie ist **nicht** mit der aktuellen NMEA-2000-PGN-Liste abgeglichen. Neuere Fast-Packet-PGNs können fehlen.

| PGN (hex) | PGN (dez) | Name im Stack |
| --- | ---: | --- |
| `0x1F014` | 126996 | `PRODUKTINFORMATION` |
| `0x1F016` | 126998 | `CONFIGURATIONINFORMATION` |
| `0x1F105` | 127237 | `HEADING_TRACKCONTROL` |
| `0x1F201` | 127489 | `ENGINE_PARA_DYNAMIC` |
| `0x1F208` | 127496 | `TRIP_PARAMETERS_VESSEL` |
| `0x1F209` | 127497 | `TRIP_PARAMETERS_ENGINE` |
| `0x1F20A` | 127498 | `ENGINE_PARAMETERS_STATIC` |
| `0x1F20F` | 127503 | `AC_INPUT_STATUS` |
| `0x1F210` | 127504 | `AC_OUTPUT_STATUS` |
| `0x1F212` | 127506 | `DCDETAILED_STATUS` |
| `0x1F213` | 127507 | `CHARGER_STATUS` |
| `0x1F215` | 127509 | `INVERTER_STATUS` |
| `0x1F216` | 127510 | `CHARGER_CONFIG_STATUS` |
| `0x1F217` | 127511 | `INVERTER_CONFIG_STATUS` |
| `0x1F218` | 127512 | `AGS_CONFIG_STATUS` |
| `0x1F219` | 127513 | `BATTERY_CONFIG_STATUS` |
| `0x1F21A` | 127514 | `AGS_STATUS` |
| `0x1F513` | 128275 | `DISTANCED_LOG` |
| `0x1F608` | 128520 | `TRACKED_TARGET_DATA` |
| `0x1F805` | 129029 | `GNSS_POSITION_DATA` |
| `0x1F80E` | 129038 | `AIS_CLASSA_POSITION_REPORT` |
| `0x1F80F` | 129039 | `AIS_CLASSB_POSITION_REPORT` |
| `0x1F810` | 129040 | `AIS_CLASSB_EXTPOS_REPORT` |
| `0x1F814` | 129044 | `DATUM` |
| `0x1F815` | 129045 | `USER_DATUM_SETTINGS` |
| `0x1F904` | 129284 | `NAVIGATION_DATA` |
| `0x1F905` | 129285 | `NAVIGATION_ROUTEWP_INFO` |
| `0x1F915` | 129301 | `TIME_TOFROM_MARK` |
| `0x1F916` | 129302 | `BEARING_DIST_BET_TWO_MARKS` |
| `0x1FA02` | 129538 | `GNSS_CONTROL_STATUS` |
| `0x1FA04` | 129540 | `GNSS_SATS_IN_VIEW` |
| `0x1FA05` | 129541 | `ALMANAC_DATA` |
| `0x1FA06` | 129542 | `GNSS_PSEUDOOR_NOISE_STATIST` |
| `0x1FA09` | 129545 | `GNSS_RAIM_OUTPUT` |
| `0x1FA0B` | 129547 | `GNSS_PSEUDOOR_ERROR_STATIST` |
| `0x1FA0D` | 129549 | `DGNSS_CORRECTIONS` |
| `0x1FA0F` | 129551 | `GNSS_DIFF_CORR_RECEIVE_SIGN` |
| `0x1FA14` | 129556 | `GLONASS_ALMANAC_DATA` |
| `0x1FB00` | 129792 | `AIS_DGNSS_BROADC_BIN_MESSAG` |
| `0x1FB01` | 129793 | `AIS_UTC_AND_DATE_REPORT` |
| `0x1FB02` | 129794 | `AIS_CLASS_A_STAT_VOYAGE_DAT` |
| `0x1FB03` | 129795 | `AIS_ADDRESSED_BINARY_MESSAG` |
| `0x1FB04` | 129796 | `AIS_ACKNOWLEDGE` |
| `0x1FB05` | 129797 | `AIS_BINARY_BROADCAST_MESSAG` |
| `0x1FB06` | 129798 | `AIS_SAR_AIRCRAFT_POS_REPORT` |
| `0x1FB07` | 129799 | `RADIO_FREQUENCY_MODE_POWER` |
| `0x1FB08` | 129800 | `AIS_UTC_DATE_INQUIRY` |
| `0x1FB09` | 129801 | `AIS_ADDRESS_SAFE_REL_MESSAG` |
| `0x1FB0A` | 129802 | `AIS_SAFE_RELD_BROADC_MESSAG` |
| `0x1FB0B` | 129803 | `AIS_INTERROGATION` |
| `0x1FB0C` | 129804 | `AIS_ASSIGNMENT_MODE_COMMAND` |
| `0x1FB10` | 129808 | `DSC_CALL_INFORMATION` |
| `0x1FC04` | 130052 | `LORAN_C_TD_DATA` |
| `0x1FC05` | 130053 | `LORAN_C_RANGE_DATA` |
| `0x1FC06` | 130054 | `LORAN_C_SIGNAL_DATA` |
| `0x1FC10` | 130064 | `ROUTE_WP_SERVICE_DB_LIST` |
| `0x1FC11` | 130065 | `ROUTE_WP_SERVICE_ROUTE_LIST` |
| `0x1FC12` | 130066 | `ROUTE_WP_SERVICE_WP_ATTRIB` |
| `0x1FC13` | 130067 | `ROUTE_WP_SERVICE_WP_NAM_POS` |
| `0x1FC14` | 130068 | `ROUTE_WP_SERVICE_QP_NAME` |
| `0x1FC15` | 130069 | `ROUTE_WP_SERVICE_XTE_LIMIT` |
| `0x1FC16` | 130070 | `ROUTE_WP_SERVICE_WP_COMMENT` |
| `0x1FC17` | 130071 | `ROUTE_WP_SERVICE_ROUTE_COM` |
| `0x1FC18` | 130072 | `ROUTE_WP_SERVICE_DB_COMMENT` |
| `0x1FC19` | 130073 | `ROUTE_WP_SERVICE_RADIU_TURN` |
| `0x1FC1A` | 130074 | `ROUTE_WP_SERVICE_WP_LIS_NAM` |
| `0x1FD10` | 130320 | `TIDE_STATION_DATA` |
| `0x1FD11` | 130321 | `SALINITY_STATION_DATA` |
| `0x1FD12` | 130322 | `CURRENT_STATION_DATA` |
| `0x1FD13` | 130323 | `METEOROLOGICAL_STATION_DATA` |
| `0x1FD14` | 130324 | `MOORED_BUOY_STATION_DATA` |
| `0x1FE11` | 130577 | `DIRECTION_DATA` |
| `0x1FE12` | 130578 | `VESSEL_SPEED_COMPONENTS` |
