# NMEA 2000 Fast Packet

Fast Packet is the NMEA 2000 method for sending messages with **9 to 223 data bytes** over CAN. It is **not** the ISOBUS transport protocol (TP/ETP from ISO 11783-3), but a simpler sequence of CAN frames. This page describes the protocol and the state in the CCI stack on which the logiBUS firmware is based.

!!! warning "State today: Fast Packet is not active in logiBUS"
    In the stack configuration (`IsoConf.h`) `ISO_NMEA_FPT` is commented out. The file `IsoDataM.c` is compiled, but its content is entirely inside `#if defined(ISO_NMEA_FPT)`.

    - **Sending:** A PGN in the range `0x1F000` to `0x1FFFF` with more than 8 bytes goes to a **specific destination** via the ISOBUS transport protocol (RTS/CTS). Nothing is sent to **global**, the stack returns `E_NOACT`.
    - **Receiving:** Fast Packet frames are not reassembled. The application sees at most individual 8-byte frames.
    - Whether and when Fast Packet will be activated has not been decided.

The statements about the stack come from the source code of the CCI stack (`IsoDataM.c`, `IsoMnDl.c`, `IsoCore.c`, `IsoDD.c`, `IsoConf.h`). Hardware was not verified.

## The protocol

Fast Packet applies to PGNs with 9 to 223 bytes. NMEA 2000 uses it for PGNs in the range `0x1F000` to `0x1FFFF` (data page 1, PDU2) and also for addressed PDU1 PGNs on data page 1, for example 126208 (`0x1ED00`), 126464 (`0x1EE00`) and 126720 (`0x1EF00`, manufacturer-specific, addressed). The CCI stack only handles the range `0x1F000` to `0x1FFFF` (see below).

- **No handshake:** There is no RTS/CTS, no acknowledgement and no retransmission. The sender transmits a sequence of CAN frames with the same PGN.
- **Byte 0 of every frame:** Bits 7 to 5 are the **sequence counter** (0 to 7, incremented per message), bits 4 to 0 the **frame counter** (0 to 31).
- **Frame 0:** Byte 1 is the total length (at most 223), bytes 2 to 7 are data bytes 1 to 6.
- **Frames 1 to n:** Bytes 1 to 7 are 7 further data bytes each. The last frame is padded with `0xFF`.
- **Maximum:** 6 + 31 × 7 = **223 bytes** (`BLOCK_SIZE_NMEA` in the stack).

Whether a PGN is sent as Fast Packet or as a single frame is **not stated in the frame**. The receiver has to know it from a PGN list.

## Comparison with the ISOBUS transport protocol

| | ISOBUS TP | NMEA 2000 Fast Packet |
| --- | --- | --- |
| Length | up to 1785 bytes (TP), more with ETP | up to 223 bytes |
| Handshake | BAM (global) or RTS/CTS (specific destination) | none |
| Acknowledgement, retransmission | by the receiver with RTS/CTS | none |
| PGN range | any PGN | in the CCI stack only `0x1F000` to `0x1FFFF` |
| Recognition at the receiver | by the protocol itself | only from the PGN list |

## Classification: NMEA 2000, J1939 and ISOBUS

The three worlds share the same basic framework. Statements with a reference are backed by the standards, statements marked "assessment" are not. Of NMEA 2000 itself, the main document and appendices A and B were not available, only the IEC standard about it.

- **Common basis:** ISO 11783 (ISOBUS) is based on CAN according to ISO 11898-1 and partly on SAE J1939. Both were developed together (ISO 11783-3:2026 and ISO 11783-1:2017, introduction in each). All three use the 29-bit identifier with priority, data page, PDU format, PDU specific and source address. With a PDU format below 240 (PDU1), PDU specific is the destination address, from 240 (PDU2) it is the group extension and therefore a broadcast (ISO 11783-3:2026, 5.3.2 and 5.3.3; SAE J1939-21, draft 2001, 5.3).
- **NAME and address claim:** ISOBUS defines them in ISO 11783-5:2019, 4.3.2 and 4.4.2, the counterpart at SAE is J1939-81. The industry group is 2 for agriculture and forestry and 4 for marine (SAE J1939, appendix, table B1).
- **NMEA 2000 builds on ISO 11783:** According to IEC 61162-3:2008 (DIN EN 61162-3:2009, with A1 and A2) the data link layer consists of ISO 11783-3 plus additional requirements from the NMEA 2000 main document, section 3 (section 5 of the IEC standard). Network management with address claim follows ISO 11783-5:2001 (section 7). SAE J1939 appears there only in the bibliography. Level B devices support address claim, ISO request and product information (3.1.12), level A devices additionally the ISO transport protocol (3.1.11).
- **Common numbering system:** PGNs, addresses, NAME fields and manufacturer codes are maintained in the isobus.net database and aligned with the SAE J1939 committee (ISO 11783-1:2017, chapter 4 and annex). The NMEA navigation PGNs such as 129029 (`0x1F805`) are in the same number space. Assessment: there is no double assignment.
- **Transport up to 1785 bytes:** The transport protocol (TP.CM on PGN 60416 = `0xEC00`, TP.DT on PGN 60160 = `0xEB00`, with BAM and RTS/CTS) is harmonized between SAE J1939-21 and ISO 11783-3 (ISO 11783-3:2026, 5.9.1 and 5.9.4). The extended transport protocol (ETP, above 1785 bytes) exists only in ISO 11783 (5.10).
- **Fast Packet is NMEA-specific:** Fast Packet does not appear in the available J1939 documents (J1939-21, drafts 1998 and 2001). The IEC standard does not describe the frame itself either, it refers to the NMEA 2000 main document (section 3) for that, which was not available. The frame description above is therefore based on the stack code and on generally documented expert knowledge. What is backed: IEC 61162-3, annex A.4.1 (informative): reassembly uses only the sequence and frame numbers, on a mismatch it is aborted, and a new start is made only at frame counter 0. This fits "no handshake, no retransmission". According to A.4.2 the Fast Packet sequence counter of a PGN does not increment when that PGN is sent via the ISO transport protocol.
- **Which PGNs are Fast Packet:** This is a property of the individual PGN definition in NMEA 2000 appendix B (not available, IEC 61162-3, section 8). The IEC standard has no list of its own. There is no marker in the identifier or in the frame. The list further below is the state of the stack and not reconciled with appendix B. Assessment from community sources (not backed): 126208, 126464 and 126720 as well as the proprietary range `0x1FF00` to `0x1FFFF` are Fast Packet too, and further PGNs are missing from the list (see the section "Further PGNs that may count as Fast Packet").

### Fast Packet on the ISOBUS

ISOBUS itself requires Fast Packet in one place: according to ISO 11783-7:2022, 7.1, control functions use the messages from IEC 61162-3 (NMEA 2000) for navigation and position, and multi-frame messages then use NMEA Fast Packet. Section 7.2 also adopts the parameters. Preferred are, among others, GNSS Position Data, Position Delta High Precision Rapid Update, COG/SOG Rapid Update and GNSS Pseudorange Noise Statistics. A GNSS receiver therefore often sends its multi-frame position messages (for example `0x1F805`) as Fast Packet on the ISOBUS.

Consequence for logiBUS: `0x1F805` is in the list below. As long as `ISO_NMEA_FPT` is not active, the stack does not reassemble such messages, the application only sees individual 8-byte frames.

### ISOBUS and NMEA 2000 devices on the same bus

- According to ISO 11783-7, 7.1 this is the normal case for GNSS receivers.
- All participants take part in the same address claim, the address is decided by the NAME (ISO 11783-5, 4.4.2). The industry group in the NAME shows the origin but does not prevent address conflicts. Both sides need the same bit rate (250 kbit/s for ISOBUS, ISO 11783-2; for NMEA 2000 this is expert knowledge, the IEC standard refers to the main document for it).
- Related does not mean interchangeable: according to IEC 61162-3, section 9, devices designed only for ISO 11783, NMEA 2000 or SAE J1939 and not tested to this standard must not be connected in normal operation.
- The range `0x1F000` to `0x1FFFF` is on data page 1, PDU2. Both NMEA 2000 and ISOBUS use it. New in ISO 11783-3:2026 is Proprietary B also on data page 1 (PF 255, so `0x1FFxx`, section 5.4.6.4).
- Assessment for practice: a receiver has to know per PGN whether it arrives via Fast Packet or via TP. With several GNSS sources on one bus it should bind to the source address or the NAME.

References: ISO 11783-1:2017, -2, -3:2026, -5:2019 and -7:2022, IEC 61162-3:2008 with A1:2010 and A2:2015, SAE J1939-21 (draft 2001) and J1939-81 (draft 2001). The section numbers of the J1939 drafts may differ from the current edition.

## Behavior of the stack if `ISO_NMEA_FPT` were active

- **Sending:** `iso_TlNmeaFPTSend()` sends up to 223 bytes, for all PGNs from `0x1F000` to `0x1FFFF` with more than 8 bytes, **also with a specific destination** (so Fast Packet instead of TP). The PGNs of this range are PDU2 without a destination address in the CAN identifier: the stack stores the specific destination, but the frames **always go out as a broadcast** on the bus.
- **Receiving:** `iso_TlNmeaFptReceive()` reassembles the frames and passes the result to the application layer like a TP reception. In 4diac it would arrive at `AlPgnRxNew_TP`.
- **Known senders only:** Fast Packet is only accepted from senders known as network participants, that is, senders that have claimed an address. Otherwise the stack discards the message ("External CF not logged -> ignore message").
- **Classification by fixed list:** `iso_TlNmeaCheckSingleFrame()` decides using the list below. All PGNs outside the list count as a single frame.
- **PGNs in the range but not in the list:** The sender decides only by the range and the length (more than 8 bytes), it does not check the list. The receiver classifies a PGN that is not in the list as a single frame and does not reassemble it: the application receives every 8-byte frame separately. Example: PGN 130569 (`0x1FE09`) is not in the list. Two devices with this stack do not understand each other for such a PGN as long as it is not added to the list (remedy: the macro below, also for standard PGNs).
- **Own PGNs:** The macro `PGNS_FPP_PROP_CODE` in `IsoConf.h` extends the list by manufacturer-specific PGNs. Example from the source: `#define PGNS_FPP_PROP_CODE case 0x1FF00UL:`. This requires recompiling the firmware.
- **Known limitation, addressed PDU1 PGNs:** Fast Packet PGNs on data page 1 in the PDU1 range (126208, 126464, 126720) never go through Fast Packet in the stack. When sending, they use the ISO transport protocol, when receiving they arrive only as a single frame or via TP. The stack marks 126208 and 126464 as not single-frame (`SF NO`) and passes 126720 on as an ISO message (`PGN_PDU1_PropA2`). The other received PDU1 NMEA PGNs are not supported.
- **4diac blocks:** Nothing changes. Send with `AlPgnTxNew_TP` (`u16DaSize` at most 223, PGN `0x1F…`), receive with `AlPgnRxNew_TP`.

## Fixed list of Fast Packet PGNs

The list has **73 entries**. The names are the macro names from the stack header in their original spelling (for example `PRODUKTINFORMATION`), not official NMEA names.

!!! note "State of the stack, not reconciled"
    The list corresponds to the CCI stack (last change according to the file header: 2024-12-06). It is **not** reconciled with the current NMEA 2000 PGN list. Newer Fast Packet PGNs may be missing.

| PGN (hex) | PGN (dec) | Name in the stack | Name according to isobus.net |
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

About the names: The column "Name according to isobus.net" comes from the export of the isobus.net database of 2026-11-14 (document "NMEA 2000"). All 73 PGNs of the list appear there. Two names in the stack differ:

- 127497 (`0x1F209`) is called `TRIP_PARAMETERS_ENGINE` in the stack, "Trip Parameters, Small Craft" in the export.
- 130068 (`0x1FC14`) is called `ROUTE_WP_SERVICE_QP_NAME` in the stack, "Route and WP Service - Route - WP Name" in the export.

The export does not state whether a PGN is Fast Packet.

## Further PGNs that may count as Fast Packet

!!! warning "Note without a standard as reference"
    This section is based on the open-source project canboat (version 10.0.0). Its data was obtained by reverse engineering and, according to the project itself, is not authoritative; many entries are marked as incomplete. A confirmation by NMEA 2000 appendix B is missing.

canboat also lists all 73 PGNs of the stack list as Fast Packet. In addition, canboat lists 49 further PGNs as Fast Packet that appear in the isobus.net export as NMEA 2000 PGNs but are not in the stack list. The names come from the export:

| PGN (hex) | PGN (dec) | Name according to isobus.net |
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

canboat also lists the manufacturer-specific range `0x1FF00` to `0x1FFFF` (130816 to 131071) as Fast Packet, as well as 126720 (`0x1EF00`, manufacturer-specific, addressed). In the stack: the range `0x1FF00` to `0x1FFFF` is inside the sending range but not in the list, so it behaves like the PGNs outside the list described above (remedy `PGNS_FPP_PROP_CODE`). 126720 falls under the limitation for addressed PDU1 PGNs mentioned above.
