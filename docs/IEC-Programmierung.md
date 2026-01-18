# IEC-Programmierung nach IEC 61131-3

```{index} single: IEC-Programmierung nach IEC 61131-3
```

<https://de.wikipedia.org/wiki/International_Electrotechnical_Commission>

<https://de.wikipedia.org/wiki/EN_61131>

<https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/PlcLib>

PLC Lib with IEC 61131 alike functions.

Very much inspired by OSCAT: <http://www.oscat.de/> and CoDeSys: <https://www.helpme-codesys.com/>

<https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/TimeDelay>

<https://www.xplore-dna.net/course/view.php?id=15>

<https://www.xplore-dna.net/mod/page/view.php?id=153>

## Grundverknüpfungen

<https://de.wikipedia.org/wiki/Boolesche_Algebra>

### UND

<https://www.xplore-dna.net/mod/page/view.php?id=155>

<https://content.helpme-codesys.com/de/CODESYS%20Development%20System/_cds_operator_and.html>

### ODER

<https://www.xplore-dna.net/mod/page/view.php?id=153>

<https://content.helpme-codesys.com/de/CODESYS%20Development%20System/_cds_operator_or.html>

### NICHT

<https://www.xplore-dna.net/mod/page/view.php?id=157>

<https://content.helpme-codesys.com/de/CODESYS%20Development%20System/_cds_operator_not.html>

### Exclusiv-ODER

<https://www.xplore-dna.net/mod/page/view.php?id=154>

<https://content.helpme-codesys.com/de/CODESYS%20Development%20System/_cds_operator_xor.html>

\# Speicherbausteine

<https://de.wikipedia.org/wiki/Flipflop>

### SR-Flipflop

<https://www.xplore-dna.net/mod/page/view.php?id=173>

<https://content.helpme-codesys.com/de/libs/Standard/Current/Bistable-Function-Blocks/SR.html>

<https://content.helpme-codesys.com/de/CODESYS%20Safety%20for%20EtherCAT%20Safety%20Module/el6900_fb_sr.html>

### RS-Flipflop

<https://www.xplore-dna.net/mod/page/view.php?id=1038>

<https://content.helpme-codesys.com/de/libs/Standard/Current/Bistable-Function-Blocks/RS.html>

<https://content.helpme-codesys.com/de/CODESYS%20Safety%20for%20EtherCAT%20Safety%20Module/el6900_fb_rs.html>

### TOGGLE (Stromstoßschalter)

<http://www.oscat.de/>

oscat_basic333_de.pdf Seite 280; Punkt 17.16. TOGGLE

## Zeitbausteine

### TON: Einschaltverzögerung

Dieser Baustein verzögert ein Signal ab dem Einschaltzeitpunkt um eine festgelegte Dauer.

<https://www.xplore-dna.net/mod/page/view.php?id=167>

<https://content.helpme-codesys.com/en/libs/Standard/Current/Timer/TON.html>

### TOF: Ausschaltverzögerung

Dieser Baustein verlängert ein Signal ab dem Ausschaltzeitpunkt um eine festgelegte Dauer.

<https://www.xplore-dna.net/mod/page/view.php?id=168>

<https://content.helpme-codesys.com/en/libs/Standard/Current/Timer/TOF.html>

### TONOF: Ein-/Ausschaltverzögerung

<http://oscat.de/images/OSCATBasic/oscat_basic333_de.pdf>
Seite 215

### TP: Impulsbildung

<https://www.xplore-dna.net/mod/page/view.php?id=166>

<https://content.helpme-codesys.com/en/libs/Standard/Current/Timer/TP.html>

## Signalflanken eines Operanden abfragen

Flankenerkennung

<https://www.xplore-dna.net/mod/page/view.php?id=158>

### R_TRIG

<https://content.helpme-codesys.com/de/libs/Standard/Current/Trigger/R_TRIG.html>

<https://www.plcacademy.com/function-block-diagram-programming/>

--> bei R_TRIG Function Block

Quelle: <https://www.plcacademy.com/function-block-diagram-programming/>

### F_TRIG

<https://content.helpme-codesys.com/de/libs/Standard/Current/Trigger/F_TRIG.html>

<https://www.plcacademy.com/function-block-diagram-programming/>

--> bei F_TRIG Function Block

Quelle: <https://www.plcacademy.com/function-block-diagram-programming/>