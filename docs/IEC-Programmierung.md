# IEC-Programmierung nach IEC 61131-3

<https://de.wikipedia.org/wiki/International_Electrotechnical_Commission>

<https://de.wikipedia.org/wiki/EN_61131>

<https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/PlcLib>

PLC Lib with IEC 61131 alike functions.

Very much inspired by OSCAT: <http://www.oscat.de/> and CoDeSys: <https://help.codesys.com/>

<https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/TimeDelay>

<https://www.xplore-dna.net/course/view.php?id=15>

<https://www.xplore-dna.net/mod/page/view.php?id=153>

## Grundverknüpfungen

<https://de.wikipedia.org/wiki/Boolesche_Algebra>

### UND

<https://www.xplore-dna.net/mod/page/view.php?id=155>

<https://help.codesys.com/webapp/_cds_operator_and;product=codesys;version=3.5.17.0>

### ODER

<https://www.xplore-dna.net/mod/page/view.php?id=153>

<https://help.codesys.com/webapp/_cds_operator_or;product=codesys;version=3.5.17.0>

### NICHT

<https://www.xplore-dna.net/mod/page/view.php?id=157>

<https://help.codesys.com/webapp/_cds_operator_not;product=codesys;version=3.5.17.0>

### Exclusiv-ODER

<https://www.xplore-dna.net/mod/page/view.php?id=154>

<https://help.codesys.com/webapp/_cds_operator_xor;product=codesys;version=3.5.17.0>

\# Speicherbausteine

<https://de.wikipedia.org/wiki/Flipflop>

### SR-Flipflop

<https://www.xplore-dna.net/mod/page/view.php?id=173>

<https://help.codesys.com/webapp/eRbQSQXbUz_fr6MUILk2nDWQDnE%2FSR;product=Standard;version=3.5.17.0>

<https://help.codesys.com/webapp/sr;product=codesys;version=3.5.11.0>

### RS-Flipflop

<https://www.xplore-dna.net/mod/page/view.php?id=1038>

<https://help.codesys.com/webapp/eRbQSQXbUz_fr6MUILk2nDWQDnE%2FRS;product=Standard;version=3.5.17.0>

<https://help.codesys.com/webapp/rs;product=codesys;version=3.5.11.0>

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

<https://help.codesys.com/webapp/f_trigger;product=codesys;version=3.5.11.0>

<https://www.xplore-dna.net/mod/page/view.php?id=158>

### R_TRIG

<https://help.codesys.com/webapp/0_BqWBsrZvp6OWXd62-zB_e-ILo%2FR_TRIG;product=Standard;version=3.5.17.0>

<https://help.codesys.com/webapp/r_trig;product=codesys;version=3.5.11.0>

<https://www.plcacademy.com/function-block-diagram-programming/>

--> bei R_TRIG Function Block


Quelle: <https://www.plcacademy.com/function-block-diagram-programming/>

### F_TRIG

<https://help.codesys.com/webapp/0_BqWBsrZvp6OWXd62-zB_e-ILo%2FF_TRIG;product=Standard;version=3.5.17.0>

<https://help.codesys.com/webapp/f_trig;product=codesys;version=3.5.11.0>

<https://www.plcacademy.com/function-block-diagram-programming/>

--> bei F_TRIG Function Block


Quelle: <https://www.plcacademy.com/function-block-diagram-programming/>
