# Condensed Work State (CWS): 2-Bit-Werte und ihre Verknüpfung

## Einleitung

Der Condensed Work State fasst den Arbeitszustand mehrerer Sections (Teilbreiten) einer Maschine in einem Prozessdatenwert zusammen. Jede Section belegt dabei **2 Bit**. Diese Seite erklärt, was die vier möglichen Bitkombinationen bedeuten, welche DDIs dazugehören und wie man mehrere Werte miteinander verknüpft.

> **Hinweis zur Quellenlage:** Die Inhalte beruhen auf lizenzierten Normtexten, die hier bewusst nur in eigenen Worten und mit Fundstelle wiedergegeben werden. Wo die Norm nichts festlegt, steht das ausdrücklich dabei. Aussagen, die eine Einschätzung und keine Normaussage sind, sind als solche gekennzeichnet.

## Zwei verschiedene Bedeutungen derselben 2 Bit

Fundstelle: ISO 11783-7:2022, Abschnitt 5.2.5, Tabellen 2 und 3 (gilt allgemein für diskrete Parameter).

Der Wert `11` ist der Stolperstein: Er bedeutet bei einem **Ist-Wert** etwas anderes als bei einem **Befehl**.

| Wert | Ist-Zustand (gemessen oder geschätzt, Tabelle 2) | Befehl (Tabelle 3) |
| ---- | ------------------------------------------------ | ------------------ |
| `00` | aus | ausschalten |
| `01` | an | einschalten |
| `10` | Fehler | reserviert (allgemein, siehe Hinweis unten) |
| `11` | nicht verfügbar oder nicht installiert | egal, keine Aktion (der Zustand bleibt, wie er ist) |

Wer Ist-Werte und Befehle gleich behandelt, macht bei einem von beiden etwas falsch. Beispiel: `11` als Ist-Wert heißt „Section gibt es nicht“, als Befehl dagegen „nichts ändern“.

> **Hinweis:** Beim Sollwert-CWS (DDI 290 bis 305) ist `10` laut DDI-Definition ein Fehlerwert und nicht „reserviert“. Maßgeblich ist jeweils die Definition des einzelnen DDI.

## Zuordnung zu den DDIs

Fundstelle: ISO 11783-11, Online-Datenbank isobus.net, DDI-Liste v2026101901 (Stand 2026-10-19). Je Section 2 Bit. Die linke Section (in Fahrtrichtung) liegt in den niederwertigsten Bits des Werts. Nicht belegte Sections werden auf `11` gesetzt.

| DDI | Name | Sections je DDI | `00` | `01` | `10` | `11` |
| --- | ---- | --------------- | ---- | ---- | ---- | ---- |
| 161 bis 176 | Actual Condensed Work State | 16 | aus | an | Fehler | undefiniert, nicht installiert |
| 290 bis 305 | Setpoint Condensed Work State | 16 | ausschalten | einschalten | Fehler | keine Änderung |
| 367 bis 382 | Condensed Section Override State | 16 | nicht übersteuert | übersteuert | reserviert | undefiniert, nicht installiert |

Weitere Angaben aus den Definitionen:

- **Override (367 bis 382):** Der Wert meldet, dass eine Section übersteuert ist und den Befehlen der Section Control nicht folgt. Er ist also eine Maske und **kein** Arbeitszustand. Er liegt im selben Device Element wie der Actual CWS und wird nur auf Anforderung des Task Controllers gesendet.
- **Section Control State (DDI 160):** `00` manuell oder aus (das Gerät ignoriert alle Befehle der Section Control), `01` automatisch oder an. Beim Start und beim Wegfall der Voraussetzungen für den Automatikbetrieb setzt das Gerät auf manuell.
- **Sollwert und Ist-Wert:** Beim Sollwert ist `11` neutral („keine Änderung“), beim Ist-Wert heißt `11` „nicht installiert“. Das belegt die Aussage aus dem vorigen Abschnitt.
- Der Anhang zu DDI 290 regelt nur die Rückwärtskompatibilität zu den DDIs 141 und 161, keine UND/ODER-Verknüpfung.

## Was die Norm zum Kombinieren sagt

- **Zustände entlang der Device-Element-Hierarchie** (ISO 11783-10:2015, Anhang F.3.5.1): Die Ist-Arbeitszustände der Hierarchieebenen werden kombiniert. Ist der Master-Arbeitszustand (DDI 141) aus, gelten alle Sections als aus. Faktisch ist das ein UND, bei dem „aus“ gewinnt.
- **Reihenfolge beim Einschalten der Section Control** (ISO 11783-10:2015, F.3.5.3 b und c): Der Task Controller schaltet zuerst den Section Control State (DDI 160) ein. Dabei setzt das Gerät alle Arbeitszustände auf einen vom Hersteller festgelegten sicheren Zustand. Erst danach folgen Sollwert-Befehle.
- **Vorrang des Bedieners** (AEF 007, RIG 3 Automation): Der Bediener muss jede Automatik jederzeit übersteuern können. Eine manuelle Abschaltung gewinnt also gegen den Sollwert des Task Controllers.
- **Was fehlt:** Eine Wahrheitstabelle oder Rangfolge für UND/ODER-Verknüpfungen der Werte `10` und `11` gibt es weder in ISO 11783-7, -10 und -11 (auch nicht in der DDI-Datenbank) noch in AEF 007, 009 oder 016.

## Empfohlene Verknüpfung (Einschätzung)

> **Einschätzung, keine Normaussage:** Die folgenden Regeln sind eine Empfehlung von Professor-ISOBUS. Sie füllen die Lücke, die die Normen offenlassen.

Leitgedanke: Eine Verknüpfung soll im Zweifel fail-safe sein, also eher abschalten als eingeschaltet lassen.

### Befehle, UND-Verknüpfung

Rangfolge: **aus > Fehler > an**. Der Wert `11` ist neutral: `UND(11, x) = x`.

Begründung: Lieferte `UND(aus, 11)` den Wert `11`, würde das Ergebnis als Befehl „keine Aktion“ gelesen, und die Section bliebe an. Das ist nicht fail-safe.

Beim Sollwert ist `10` ein echter Fehlerwert. Ein Gerät, das ihn empfängt, sollte ihn wie „aus“ (sicherer Zustand) behandeln. Das ist ebenfalls eine Einschätzung.

| A \ B | `00` aus | `01` an | `10` Fehler | `11` neutral |
| --- | :---: | :---: | :---: | :---: |
| **`00` aus** | `00` | `00` | `00` | `00` |
| **`01` an** | `00` | `01` | `10` | `01` |
| **`10` Fehler** | `00` | `10` | `10` | `10` |
| **`11` neutral** | `00` | `01` | `10` | `11` |

Zeile = Eingang A, Spalte = Eingang B. Die Tabelle folgt aus der Rangfolge oben und ist wie diese eine Einschätzung.

### Befehle, ODER-Verknüpfung

Rangfolge: **Fehler > an > aus**. Der Wert `11` ist neutral: `ODER(11, x) = x`. Ein Empfänger behandelt das Ergebnis `10` wie „aus“.

| A \ B | `00` aus | `01` an | `10` Fehler | `11` neutral |
| --- | :---: | :---: | :---: | :---: |
| **`00` aus** | `00` | `01` | `10` | `00` |
| **`01` an** | `01` | `01` | `10` | `01` |
| **`10` Fehler** | `10` | `10` | `10` | `10` |
| **`11` neutral** | `00` | `01` | `10` | `11` |

Zeile = Eingang A, Spalte = Eingang B. Die Tabelle folgt aus der Rangfolge oben und ist wie diese eine Einschätzung.

### Ist-Zustände, UND-Verknüpfung

Rangfolge: **Fehler > nicht verfügbar > aus > an**. „An“ ergibt sich nur, wenn beide Eingänge „an“ sind. Verbraucher müssen alles außer `01` als „aus“ werten.

Hier bedeutet `11` „nicht installiert“. Die Tabelle gilt deshalb nur für Ist-Werte, nicht für Befehle.

| A \ B | `00` aus | `01` an | `10` Fehler | `11` nicht installiert |
| --- | :---: | :---: | :---: | :---: |
| **`00` aus** | `00` | `00` | `10` | `11` |
| **`01` an** | `00` | `01` | `10` | `11` |
| **`10` Fehler** | `10` | `10` | `10` | `10` |
| **`11` nicht installiert** | `11` | `11` | `10` | `11` |

Zeile = Eingang A, Spalte = Eingang B. Die Tabelle folgt aus der Rangfolge oben und ist wie diese eine Einschätzung.

### Ist-Zustände, ODER-Verknüpfung (Anzeige)

Rangfolge: **Fehler > an > aus > nicht verfügbar**.

| A \ B | `00` aus | `01` an | `10` Fehler | `11` nicht installiert |
| --- | :---: | :---: | :---: | :---: |
| **`00` aus** | `00` | `01` | `10` | `00` |
| **`01` an** | `01` | `01` | `10` | `01` |
| **`10` Fehler** | `10` | `10` | `10` | `10` |
| **`11` nicht installiert** | `00` | `01` | `10` | `11` |

Zeile = Eingang A, Spalte = Eingang B. Die Tabelle folgt aus der Rangfolge oben und ist wie diese eine Einschätzung.

### Override: umschalten statt verknüpfen

Den Override-Zustand nicht per UND oder ODER verknüpfen, sondern als Umschalter verwenden: Ist die Section übersteuert, gilt der manuelle Wert, sonst der Sollwert des Task Controllers.

## Zusammenfassung der Rangfolgen

| Verknüpfung | Rangfolge (höchste zuerst) | Besonderheit |
| ----------- | -------------------------- | ------------ |
| Befehle, UND | aus, Fehler, an | `11` neutral |
| Befehle, ODER | Fehler, an, aus | `11` neutral, `10` wie „aus“ behandeln |
| Ist-Zustände, UND | Fehler, nicht verfügbar, aus, an | alles außer `01` zählt als „aus“ |
| Ist-Zustände, ODER | Fehler, an, aus, nicht verfügbar | nur für Anzeige |
| Override | keine Verknüpfung | Umschalter: manuell oder Task Controller |

## Praxisbezug

Anlass dieser Seite sind die 4diac-Bibliotheken `logiBUS_sections_bit` und `logiBUS_sections_quarter` (Projekt `4diac_training1`, Branch `feature/logiBUS-sections`). Die Bibliotheken (Package `logiBUS::sections`, [PR #347](https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/4diac_training1/pull/347)) enthalten Switcher für Teilbreiten sowie die Masken-Bausteine `SEC_MASK_QUARTER_CMD` (Sollwert) und `SEC_MASK_QUARTER_ACT` (Istwert). Es gibt zwei Masken-Bausteine, weil `11` beim Sollwert „keine Änderung“ und beim Istwert „nicht installiert“ bedeutet. Der Sollwert-Baustein setzt gesperrte Sections immer auf `00` (auch bei `10` und `11`), damit ein späteres `11` („keine Änderung“) die Abschaltung nicht aufhebt. Der Istwert-Baustein ändert bei gesperrten Sections nur `01` in `00`, Fehler (`10`) und „nicht installiert“ (`11`) bleiben sichtbar. Section 1 ist die linke Section und liegt in den niederwertigsten Bits. Die Seite selbst gilt allgemein für jede Implementierung.

## Fundstellen

| Aussage | Fundstelle |
| ------- | ---------- |
| Bedeutung der 2-Bit-Werte als Ist-Wert und als Befehl | ISO 11783-7:2022, Abschnitt 5.2.5, Tabellen 2 und 3 |
| Kombination der Arbeitszustände, Master-Arbeitszustand (DDI 141) | ISO 11783-10:2015, Anhang F.3.5.1 |
| Section Control State (DDI 160), sicherer Zustand, Reihenfolge | ISO 11783-10:2015, F.3.5.3 b und c |
| Bediener kann jede Automatik übersteuern | AEF 007 (RIG 3 Automation) |
| DDI-Definitionen (160, 161 bis 176, 290 bis 305, 367 bis 382) | ISO 11783-11, Online-Datenbank isobus.net, DDI-Liste v2026101901 (Stand 2026-10-19) |
