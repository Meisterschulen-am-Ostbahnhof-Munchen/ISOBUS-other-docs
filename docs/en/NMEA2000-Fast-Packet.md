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

| PGN (hex) | PGN (dec) | Name in the stack |
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
