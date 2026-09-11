# Kalibrierverfahren: Zwei-Punkt und Mehrpunkt-Kalibrierung

## Warum überhaupt kalibrieren?

Kein Sensor liefert von Haus aus genau den physikalischen Wert, den man eigentlich messen will. Ein Rohsignal (zum Beispiel eine Spannung, ein Strom oder ein digitalisierter Zahlenwert 0…4095 von einem Analog-Digital-Wandler) hängt zunächst nur mittelbar mit der gewünschten physikalischen Größe zusammen – etwa dem Lenkwinkel, dem Füllstand oder dem Öldruck. Zwei Fehlerquellen treten dabei praktisch immer gemeinsam auf:

- ein **Nullpunktfehler** (Offset): das Rohsignal ist bei der tatsächlichen physikalischen Null nicht selbst null, sondern um einen konstanten Betrag verschoben, und
- ein **Steigungsfehler** (Verstärkungs- oder Spannenfehler): eine Änderung der physikalischen Größe erzeugt nicht exakt die erwartete Änderung des Rohsignals, sondern eine mehr oder weniger davon abweichende.

Kalibrieren heißt: an mindestens zwei bekannten, real angefahrenen Referenzpunkten (zum Beispiel "Anschlag links" und "Anschlag rechts") das jeweils gemessene Rohsignal mit dem dort tatsächlich gültigen physikalischen Sollwert in Beziehung setzen, damit daraus eine Umrechnungsvorschrift für alle Zwischenwerte entsteht.

## Das klassische Verfahren: Nullpunkt, dann Steigung

Die traditionelle, aus der Instrumentierungstechnik stammende Vorgehensweise entspricht exakt dem schulischen Aufstellen einer Geradengleichung `y = m·x + b`, nur in zwei nacheinander ausgeführten Handgriffen:

1. **Nullpunktabgleich:** Die untere Referenz anfahren (zum Beispiel den linken Anschlag), den gewünschten Zielwert vorgeben und den Nullpunkt-Trimmer betätigen. Intern wird daraus ein Offset `b` berechnet, so dass die Anzeige an genau dieser Stelle stimmt.
2. **Steigungsabgleich:** Die obere Referenz anfahren (zum Beispiel den rechten Anschlag), den zweiten Zielwert vorgeben und den Spannen-Trimmer betätigen. Daraus wird die Steigung `m` berechnet – unter Verwendung des im ersten Schritt bereits ermittelten Offsets.

Das Verfahren funktioniert, hat aber eine eingebaute Tücke: der zweite Schritt **setzt zwingend voraus**, dass der erste Schritt bereits ausgeführt wurde, denn die Steigungsberechnung rechnet mit dem beim Nullpunktabgleich gemerkten Zwischenergebnis weiter. Wird versehentlich zuerst die Spanne und gar kein Nullpunkt abgeglichen (oder die Reihenfolge vertauscht), rechnet das System mit einem noch nicht sinnvoll gesetzten – oft einem technischen Default- oder Vorgängerwert – und das Ergebnis ist eine unbemerkt falsche Kalibrierung, die auf den ersten Blick plausibel aussieht. In der Praxis begegnet man dem, indem man die Bedienung technisch zwingt: der Spannen-Abgleich lässt sich schlicht nicht auslösen, bevor nicht mindestens einmal der Nullpunkt gesetzt wurde. Das schützt zuverlässig vor dem Bedienfehler, kostet aber zusätzlichen Aufwand – eine Ablaufüberwachung, die es eigentlich nur deshalb braucht, weil das Rechenverfahren selbst eine Reihenfolge erzwingt.

## Das intelligentere Verfahren: unabhängige Referenzpunkte

Der modernere Ansatz löst genau dieses Problem, indem er den Rechenschritt vom Kalibriervorgang trennt:

- Beim Anfahren einer Referenzposition wird **nur** das dort gemessene Rohsignal unverändert gespeichert – ein reiner Momentaufnahme-Vorgang, ohne jede Berechnung und ohne Bezug zu einem anderen Referenzpunkt.
- Die eigentliche Umrechnung (die Gerade durch die gespeicherten Punkte) wird **nicht** beim Kalibrieren berechnet, sondern jedes Mal frisch dann, wenn ein neuer Messwert tatsächlich angezeigt oder weiterverarbeitet werden soll.

Dadurch entfällt die Abhängigkeit zwischen den beiden Kalibrierschritten vollständig: Es gibt schlicht nichts mehr, worauf der zweite Schritt "warten" müsste, weil beide Schritte für sich genommen nur eine unabhängige Zahl ablegen. Die beiden Referenzpunkte lassen sich in beliebiger Reihenfolge setzen, beliebig oft wiederholen und – besonders praxisrelevant – auch einzeln nachkalibrieren, etwa wenn nur einer der beiden Anschläge im Feld nachjustiert werden muss, ohne dass der andere neu abgeglichen werden müsste.

Das Prinzip lässt sich unmittelbar auf drei (oder mehr) Referenzpunkte erweitern. Immer dann, wenn ein Sensor neben den beiden Endanschlägen auch eine ausgezeichnete Mittelstellung besitzt – der klassische Fall ist ein Lenkwinkelsensor mit den drei Referenzen "voller Einschlag links", "Geradeausstellung" und "voller Einschlag rechts" – reicht eine einzige Geradengleichung nicht mehr aus, weil mechanische Toleranzen dazu führen können, dass die Kennlinie links und rechts der Mitte unterschiedlich steil verläuft. Man legt dann für jeden der drei Punkte unabhängig das gemessene Rohsignal ab und verbindet anschließend die Punkte stückweise linear – zwischen Links-Anschlag und Mitte mit der einen Steigung, zwischen Mitte und Rechts-Anschlag mit einer eigenen, davon unabhängigen Steigung. Auch hier gilt unverändert: weil jeder der drei Punkte nur unabhängig einen Rohwert speichert und die Interpolation erst beim Auslesen passiert, gibt es zwischen den drei Kalibrierschritten keinerlei erzwingbare Reihenfolge – jeder Punkt kann für sich gesetzt und später für sich allein nachjustiert werden.

## Die allgemeine Lehre dahinter

Der Unterschied zwischen beiden Verfahren ist letztlich kein Sonderfall der Sensorkalibrierung, sondern ein allgemeines Entwurfsprinzip, das weit darüber hinaus gültig bleibt: Immer wenn mehrere unabhängige Einzelinformationen erfasst werden sollen, lohnt es sich zu fragen, ob sie beim Erfassen sofort zu einem laufenden Zwischenergebnis verrechnet werden müssen – oder ob es nicht einfacher und robuster ist, jede Einzelinformation zunächst unverändert für sich abzulegen und das eigentliche Ergebnis erst bei Bedarf, aus allen vorliegenden Einzelwerten gemeinsam, zu berechnen.

Die erste Variante spart im Erfassungsschritt selbst etwas Rechenaufwand, erkauft sich das aber mit einer verdeckten Reihenfolge-Abhängigkeit zwischen den Erfassungsschritten – mit allen Folgen, die das für Bedienung, Fehlersicherheit und Wartbarkeit hat: eine zusätzliche Ablaufkontrolle wird nötig, ein einzelner Schritt lässt sich nicht mehr risikofrei allein wiederholen, und der eigentliche Grund für die Reihenfolge ist von außen oft gar nicht erkennbar, sondern nur aus der internen Rechenvorschrift heraus zu verstehen. Die zweite Variante ist im Erfassungsschritt selbst minimal aufwendiger – man muss den Rohwert eben separat vorhalten, statt ihn sofort zu verrechnen –, gewinnt dafür aber Robustheit: jeder Erfassungsschritt ist für sich genommen vollständig unabhängig, beliebig wiederholbar und in beliebiger Reihenfolge ausführbar, weil er keinerlei Wissen über die anderen Schritte braucht.

Diese Abwägung – "sofort verrechnen und dabei eine Abhängigkeit in Kauf nehmen" gegenüber "erstmal nur festhalten und die Abhängigkeit auflösen, indem man sie an das Ende verschiebt" – begegnet einem in der Steuerungs- und Messtechnik immer wieder, weit über die Kalibrierung einzelner Sensoren hinaus. Es lohnt sich, bei jedem mehrstufigen Erfassungs- oder Einstellvorgang bewusst zu prüfen, welche der beiden Varianten vorliegt – und ob eine vermeintlich "logische" Reihenfolge tatsächlich in der Sache begründet ist, oder nur ein Nebeneffekt davon, wie die Berechnung zufällig aufgebaut wurde.
