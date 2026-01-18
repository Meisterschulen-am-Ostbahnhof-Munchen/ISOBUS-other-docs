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

## 🎧 Podcast

* [Die drei Timer der DIN EN 61131-3 entschlüsselt – TP, TON & TOF präzise erklärt](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/Die-drei-Timer-der-DIN-EN-61131-3-entschlsselt--TP--TON--TOF-przise-erklrt-e3dma77)
* [DIN EN 61131-3 vs. 61499-1: Dein Wegweiser durch die Normen der Industrieautomatisierung](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/DIN-EN-61131-3-vs--61499-1-Dein-Wegweiser-durch-die-Normen-der-Industrieautomatisierung-e36c6nc)
* [DIN EN 61131-3: Das Herz der Land- und Baumaschinen-Mechatronik und der Sprung in die Zukunft mit Ob](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/DIN-EN-61131-3-Das-Herz-der-Land--und-Baumaschinen-Mechatronik-und-der-Sprung-in-die-Zukunft-mit-Ob-e36c2mp)
* [FB_TOF und E_TOF: Verzögerungstimer in IEC 61131-3 und 61499](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/FB_TOF-und-E_TOF-Verzgerungstimer-in-IEC-61131-3-und-61499-e368e2d)
* [IEC 61499 vs. 61131: Brauchen wir einen neuen Standard für IIoT? Analyse einer hitzigen Debatte um Verteilte Intelligenz](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/IEC-61499-vs--61131-Brauchen-wir-einen-neuen-Standard-fr-IIoT--Analyse-einer-hitzigen-Debatte-um-Verteilte-Intelligenz-e3ahc2r)

## 📺 Video

* [Everything about timers in IEC 61131-3 | TON, TOF & TP explained in an easy-to-understand way! ⏱️](https://www.youtube.com/watch?v=Zlm488qBtZY)
* [IEC 61499 vs. 61131: Brauchen wir einen neuen Standard für IIoT? Analyse einer hitzigen Debatte u...](https://www.youtube.com/watch?v=kp8L-yM5mAs)
* [IEC 61499: Befreit der neue Standard die Industrieautomation? Ein Vergleich mit 61131 und die Brü...](https://www.youtube.com/watch?v=iIbwnDH--M4)

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