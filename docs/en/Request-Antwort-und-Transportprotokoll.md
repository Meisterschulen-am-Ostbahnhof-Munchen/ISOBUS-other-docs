# Request for PGN, response and transport protocol

This page explains what happens on the ISOBUS when a device **requests** a message (PGN): who answers, where the answer goes, and what happens with more than 8 data bytes. It also shows which blocks of the logiBUS® library belong together and which exercises therefore run **together** as sender and receiver.

## Basic terms

**PDU format.** Every PGN belongs to one of two formats:

- **PDU1:** The PGN contains a destination address (DA). The message can go **specifically** to one device or **globally** to all (destination address `0xFF`).
- **PDU2:** The PGN contains no destination address. The message is always meant for everybody.

**Request for PGN.** A device requests a PGN with the request PGN `0xEA00`. The message carries three data bytes, namely the requested PGN. The request can go to a single device (specific) or to all (global).

**Transport protocol (TP).** A CAN frame carries at most 8 data bytes. Longer PGNs are sent over the transport protocol in several frames:

- **BAM** (Broadcast Announce Message): The sender announces the message to everybody and sends the packets without feedback. BAM exists only globally.
- **RTS/CTS** (Request to Send / Clear to Send): The sender sets up a connection to **one** receiver, which controls and acknowledges the reception of the packets. RTS/CTS exists only specifically.

With TP, up to 1785 data bytes can be transferred (255 packets of 7 bytes each).

## How is a request answered?

Which answer comes depends on the PDU format, the data length and the destination address of the request:

| PDU format | Length | Request | Response | Acknowledgement (ACK/NACK) |
| --- | --- | --- | --- | --- |
| 1 | up to 8 bytes | specific | specific | specific to the requester |
| 1 | up to 8 bytes | global | global | none |
| 1 | over 8 bytes | specific | RTS/CTS | specific to the requester |
| 1 | over 8 bytes | global | BAM | none |
| 2 | up to 8 bytes | specific | global | specific to the requester |
| 2 | up to 8 bytes | global | global | none |
| 2 | over 8 bytes | specific | RTS/CTS | specific to the requester |
| 2 | over 8 bytes | global | BAM | none |

Rule of thumb: A **global** request is always answered **globally** and never acknowledged. With more than 8 bytes this becomes a BAM. A **specific** request gets an acknowledgement, and with more than 8 bytes the response goes over RTS/CTS.

## What does the logiBUS® firmware do?

This information comes from the firmware source code and has not been measured on hardware.

- **Automatic response:** The firmware answers a request by itself if a transmit PGN is installed for the requested PGN and it is **active or deactivated**. A transmit PGN in the "disabled" state (the state it starts in after installation) does not answer. Before sending, the firmware fetches the current data.
- **Specific or global:** A global request is answered globally. A specific request to our address is answered specifically to the requester.
- **More than 8 bytes:** The response goes over the same send path. Global gives BAM, specific gives RTS/CTS.
- **NACK:** For a **specific** request that cannot be answered (no matching, active transmit PGN), the firmware sends a NACK to the requester. A global request never gets a NACK. The behaviour on the second CAN port has not been checked.
- **Limit:** At most **1785 bytes** can be sent through the PGN blocks. The extended transport protocol (ETP, for even larger amounts) is not available to the PGN blocks. The firmware itself uses ETP only internally, for example for the object pool upload of the Virtual Terminal.
- **Reception:** The firmware accepts TP messages (BAM and RTS/CTS). The existing receive blocks (`AlPgnRxNew8B` and relatives) only pass **8 bytes** on to the application, though. Longer PGNs up to 1785 bytes are to be delivered by the new block `AlPgnRxNew_TP`. It is still in progress and untested.

## Which blocks answer?

The transmit blocks differ in whether they answer requests:

| Block | Sends | Answers a request |
| --- | --- | --- |
| [`AlPgnTxNew8B`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew8B/) | only on event `REQ` | **no** |
| [`AlPgnTxNew8B_REQ`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew8B_REQ/) | not cyclically | **yes** |
| [`AlPgnTxNew8Bcycl`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew8Bcycl/) and [`AlPgnTxNew8Bcycl_REQ`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew8Bcycl_REQ/) | cyclically | **yes** |
| [`AlPgnTxNew_TP`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew_TP/) | only on event `REQ`, more than 8 bytes | **no** |

On the receiver side, the `_REQ` blocks issue a request themselves:

- [`AlPgnRxNew8B_REQ`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/rx/AlPgnRxNew8B_REQ/): Every event `REQ` sends **one** request. If a single device is given as source, it goes specifically to that device, otherwise globally.
- [`AlPgnRxNew8Bcylc_REQ`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/rx/AlPgnRxNew8Bcylc_REQ/): The driver sends the request **periodically** every `u16DefRepRate` milliseconds (valid 10 to 65023 ms, outside that range no cyclic request is sent). `u16CtrlTime` is the reception monitoring: if the PGN does not arrive for longer than this many milliseconds, the driver reports a timeout. `16#FFFF` switches the monitoring off.
- [`AlPgnRxNew8B`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/rx/AlPgnRxNew8B/) and [`AlPgnRxNew8Bcylc`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/pgn/rx/AlPgnRxNew8Bcylc/): They do **not** issue a request, they only wait for the PGN (the cyclic one with monitoring).

## Which exercise belongs to which?

The exercises run in pairs: one sends, the other receives or requests. All exercises named here use the same PGN `PGN_PDU1_PropA` (61184) and therefore fit together. The assignment is derived from the parameters of the exercises.

| Exercise | Role | Counterpart |
| --- | --- | --- |
| [126](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/en/latest/Uebungen/test_AX/Uebungen_doc/Uebung_126_AX/) and [127](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/en/latest/Uebungen/test_AX/Uebungen_doc/Uebung_127_AX/): Send Message Cyclic | send cyclically (500 ms) **and** answer requests | 130, 131, 132 and 133 |
| [128](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/en/latest/Uebungen/test_AX/Uebungen_doc/Uebung_128_AX/): Send Message GLOBAL | sends only on an event, does **not** answer requests | 130 (Receive) |
| [130](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/en/latest/Uebungen/test_AX/Uebungen_doc/Uebung_130_AX/): Receive Message | waits for the PGN | 126, 127 or 128 |
| [131](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/en/latest/Uebungen/test_AX/Uebungen_doc/Uebung_131_AX/): Receive Message Cyclic | waits with monitoring (1500 ms) | 126 or 127 |
| [132](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/en/latest/Uebungen/test_AX/Uebungen_doc/Uebung_132_AX/): Request Message | requests **once** | 126 or 127 (they answer) |
| [133](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/en/latest/Uebungen/test_AX/Uebungen_doc/Uebung_133_AX/): Request Message Cyclic | requests **periodically** (500 ms), monitoring 1500 ms | 126 or 127 (they answer) |

!!! warning "The counterpart of Request Message is not Send Message"
    For 132 and 133, only a sender that **answers** requests helps. These are the cyclic senders 126 and 127. Exercise 128 sends the same PGN but does not answer any request.

## Sources

- Training material *CCI ISOBUS Driver Workshop Basic*, part 01 Base (basic training March 2022), slide 41 "Part 3 – Data Link Layer – Request and Response". The table above is taken from it in paraphrased form.
- ISO 11783-3 (data link layer, transport protocol) and SAE J1939-21.
- The behaviour of the logiBUS® firmware comes from the firmware source code, not from measurements.
