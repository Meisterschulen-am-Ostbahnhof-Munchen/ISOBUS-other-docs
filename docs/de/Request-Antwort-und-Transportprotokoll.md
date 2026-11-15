# Request for PGN, Antwort und Transportprotokoll

Diese Seite erklärt, was auf dem ISOBUS passiert, wenn ein Gerät eine Nachricht (PGN) **anfragt**: Wer antwortet, an wen geht die Antwort, und was geschieht bei mehr als 8 Datenbyte. Sie zeigt außerdem, welche Bausteine der logiBUS®-Bibliothek zusammengehören und welche Übungen deshalb als Sender und Empfänger **zusammen** laufen.

## Grundbegriffe

**PDU-Format.** Jede PGN gehört zu einem von zwei Formaten:

- **PDU1:** Die PGN enthält eine Zieladresse (DA). Die Nachricht kann **gezielt** an ein Gerät oder **global** an alle gehen (Zieladresse `0xFF`).
- **PDU2:** Die PGN enthält keine Zieladresse. Die Nachricht ist immer für alle gedacht.

**Request for PGN.** Ein Gerät fragt eine PGN mit der Request-PGN `0xEA00` an. Die Nachricht trägt drei Datenbyte, nämlich die angefragte PGN. Das Request kann an ein einzelnes Gerät (gezielt) oder an alle (global) gehen.

**Transportprotokoll (TP).** Ein CAN-Rahmen trägt höchstens 8 Datenbyte. Längere PGNs gehen über das Transportprotokoll in mehreren Rahmen:

- **BAM** (Broadcast Announce Message): Der Sender kündigt die Nachricht an alle an und sendet die Pakete ohne Rückmeldung. BAM gibt es nur global.
- **RTS/CTS** (Request to Send / Clear to Send): Der Sender baut eine Verbindung zu **einem** Empfänger auf, der den Empfang der Pakete steuert und bestätigt. RTS/CTS gibt es nur gezielt.

Mit TP lassen sich bis zu 1785 Datenbyte übertragen (255 Pakete zu je 7 Byte).

## Wie wird ein Request beantwortet?

Welche Antwort kommt, hängt von PDU-Format, Datenlänge und der Zieladresse des Requests ab:

| PDU-Format | Länge | Request | Antwort | Bestätigung (ACK/NACK) |
| --- | --- | --- | --- | --- |
| 1 | bis 8 Byte | gezielt | gezielt | gezielt an den Anfrager |
| 1 | bis 8 Byte | global | global | keine |
| 1 | über 8 Byte | gezielt | RTS/CTS | gezielt an den Anfrager |
| 1 | über 8 Byte | global | BAM | keine |
| 2 | bis 8 Byte | gezielt | global | gezielt an den Anfrager |
| 2 | bis 8 Byte | global | global | keine |
| 2 | über 8 Byte | gezielt | RTS/CTS | gezielt an den Anfrager |
| 2 | über 8 Byte | global | BAM | keine |

Merksatz: Ein **globales** Request wird immer **global** beantwortet und nie bestätigt. Bei mehr als 8 Byte wird daraus ein BAM. Ein **gezieltes** Request bekommt eine Bestätigung, bei mehr als 8 Byte läuft die Antwort über RTS/CTS.

## Was macht die logiBUS®-Firmware?

Diese Angaben stammen aus dem Quellcode der Firmware und sind nicht auf Hardware gemessen.

- **Automatische Antwort:** Die Firmware beantwortet ein Request selbst, wenn für die angefragte PGN ein Sende-PGN installiert ist und dieses **aktiv oder deaktiviert** ist. Ein Sende-PGN im Zustand „gesperrt“ (so startet es nach der Installation) antwortet nicht. Vor dem Senden holt die Firmware die aktuellen Daten.
- **Gezielt oder global:** Ein globales Request wird global beantwortet. Ein gezieltes Request an unsere Adresse wird gezielt an den Anfrager beantwortet.
- **Mehr als 8 Byte:** Die Antwort geht über denselben Sendeweg. Global ergibt BAM, gezielt RTS/CTS.
- **NACK:** Auf ein **gezieltes** Request, das nicht beantwortet werden kann (kein passendes, aktives Sende-PGN), sendet die Firmware ein NACK an den Anfrager. Auf ein globales Request kommt nie ein NACK. Das Verhalten auf dem zweiten CAN-Anschluss ist nicht geprüft.
- **Grenze:** Über die PGN-Bausteine lassen sich höchstens **1785 Byte** senden. Das erweiterte Transportprotokoll (ETP, für noch größere Mengen) steht den PGN-Bausteinen nicht zur Verfügung. Die Firmware selbst benutzt ETP nur intern, zum Beispiel für den Objektpool-Upload des Virtual Terminals.
- **Empfang:** Die Firmware nimmt TP-Nachrichten (BAM und RTS/CTS) an. Die bisherigen Empfangsbausteine (`AlPgnRxNew8B` und Verwandte) geben der Anwendung aber nur **8 Byte** weiter. Längere PGNs bis 1785 Byte soll der neue Baustein `AlPgnRxNew_TP` liefern. Er ist noch in Arbeit und nicht getestet.

## Welche Bausteine antworten?

Die Sendebausteine unterscheiden sich darin, ob sie auf Requests antworten:

| Baustein | Sendet | Antwortet auf Request |
| --- | --- | --- |
| [`AlPgnTxNew8B`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew8B/) | nur auf Ereignis `REQ` | **nein** |
| [`AlPgnTxNew8B_REQ`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew8B_REQ/) | nicht zyklisch | **ja** |
| [`AlPgnTxNew8Bcycl`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew8Bcycl/) und [`AlPgnTxNew8Bcycl_REQ`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew8Bcycl_REQ/) | zyklisch | **ja** |
| [`AlPgnTxNew_TP`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/tx/AlPgnTxNew_TP/) | nur auf Ereignis `REQ`, mehr als 8 Byte | **nein** |

Auf der Empfängerseite stellen die `_REQ`-Bausteine selbst ein Request:

- [`AlPgnRxNew8B_REQ`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/rx/AlPgnRxNew8B_REQ/): Jedes Ereignis `REQ` sendet **ein** Request. Ist als Quelle ein einzelnes Gerät angegeben, geht es gezielt an dieses, sonst global.
- [`AlPgnRxNew8Bcylc_REQ`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/rx/AlPgnRxNew8Bcylc_REQ/): Der Treiber sendet das Request **periodisch** alle `u16DefRepRate` Millisekunden (gültig 10 bis 65023 ms, außerhalb davon wird kein zyklisches Request gesendet). `u16CtrlTime` ist die Zeitüberwachung des Empfangs: Kommt die PGN länger als so viele Millisekunden nicht, meldet der Treiber einen Timeout. `16#FFFF` schaltet die Überwachung aus.
- [`AlPgnRxNew8B`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/rx/AlPgnRxNew8B/) und [`AlPgnRxNew8Bcylc`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/pgn/rx/AlPgnRxNew8Bcylc/): Sie stellen **kein** Request, sondern warten nur auf die PGN (der zyklische mit Zeitüberwachung).

## Welche Übung gehört zu welcher?

Die Übungen laufen paarweise: Eine sendet, die andere empfängt oder fragt an. Alle hier genannten Übungen benutzen dieselbe PGN `PGN_PDU1_PropA` (61184) und passen deshalb zusammen. Die Zuordnung ist aus den Parametern der Übungen abgeleitet.

| Übung | Rolle | Gegenstück |
| --- | --- | --- |
| [126](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_AX/Uebungen_doc/Uebung_126_AX/) und [127](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_AX/Uebungen_doc/Uebung_127_AX/): Send Message Cyclic | senden zyklisch (500 ms) **und** antworten auf Requests | 130, 131, 132 und 133 |
| [128](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_AX/Uebungen_doc/Uebung_128_AX/): Send Message GLOBAL | sendet nur auf Ereignis, antwortet **nicht** auf Requests | 130 (Receive) |
| [130](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_AX/Uebungen_doc/Uebung_130_AX/): Receive Message | wartet auf die PGN | 126, 127 oder 128 |
| [131](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_AX/Uebungen_doc/Uebung_131_AX/): Receive Message Cyclic | wartet mit Zeitüberwachung (1500 ms) | 126 oder 127 |
| [132](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_AX/Uebungen_doc/Uebung_132_AX/): Request Message | fragt **einmal** an | 126 oder 127 (sie antworten) |
| [133](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_AX/Uebungen_doc/Uebung_133_AX/): Request Message Cyclic | fragt **periodisch** an (500 ms), Zeitüberwachung 1500 ms | 126 oder 127 (sie antworten) |

!!! warning "Das Gegenstück zu Request Message ist nicht „Send Message“"
    Bei 132 und 133 hilft nur ein Sender, der auf Requests **antwortet**. Das sind die zyklischen Sender 126 und 127. Übung 128 sendet zwar dieselbe PGN, antwortet aber auf kein Request.

## Quellen

- Schulungsunterlage *CCI ISOBUS Driver Workshop Basic*, Teil 01 Base (Basis-Schulung März 2022), Folie 41 „Part 3 – Data Link Layer – Request and Response“. Die Tabelle oben ist sinngemäß daraus übernommen.
- ISO 11783-3 (Data Link Layer, Transportprotokoll) und SAE J1939-21.
- Das Verhalten der logiBUS®-Firmware stammt aus dem Quellcode der Firmware, nicht aus Messungen.
