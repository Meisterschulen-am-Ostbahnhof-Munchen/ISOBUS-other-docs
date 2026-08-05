# IEC programming according to IEC 61131-3
<https://de.wikipedia.org/wiki/International_Electrotechnical_Commission>
<https://de.wikipedia.org/wiki/EN_61131>
<https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/PlcLib>
PLC Lib with IEC 61131 alike functions.
Very much inspired by OSCAT: <http://www.oscat.de/> and CoDeSys: <https://www.helpme-codesys.com/>

<https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/TimeDelay>

<https://www.xplore-dna.net/course/view.php?id=15>

<https://www.xplore-dna.net/mod/page/view.php?id=153>

## Basic Interconnections

<https://de.wikipedia.org/wiki/Boolesche_Algebra>

### AND

<https://www.xplore-dna.net/mod/page/view.php?id=155>

<https://content.helpme-codesys.com/de/CODESYS%20Development%20System/_cds_operator_and.html>

### OR

<https://www.xplore-dna.net/mod/page/view.php?id=153>

<https://content.helpme-codesys.com/de/CODESYS%20Development%20System/_cds_operator_or.html>

### NOT

<https://www.xplore-dna.net/mod/page/view.php?id=157>

<https://content.helpme-codesys.com/de/CODESYS%20Development%20System/_cds_operator_not.html>

### Exclusive OR

<https://www.xplore-dna.net/mod/page/view.php?id=154>

<https://content.helpme-codesys.com/de/CODESYS%20Development%20System/_cds_operator_xor.html>

# Memory Components

<https://de.wikipedia.org/wiki/Flipflop>

### SR Flip-Flop

<https://www.xplore-dna.net/mod/page/view.php?id=173>

<https://content.helpme-codesys.com/de/libs/Standard/Current/Bistable-Function-Blocks/SR.html>

<https://content.helpme-codesys.com/de/CODESYS%20Safety%20for%20EtherCAT%20Safety%20Module/el6900_fb_sr.html>

### RS Flip-Flop

<https://www.xplore-dna.net/mod/page/view.php?id=1038>

<https://content.helpme-codesys.com/de/libs/Standard/Current/Bistable-Function-Blocks/RS.html>

<https://content.helpme-codesys.com/de/CODESYS%20Safety%20for%20EtherCAT%20Safety%20Module/el6900_fb_rs.html>

### TOGGLE (Impulse Switch)

<http://www.oscat.de/>

oscat_basic333_de.pdf Page 280; Item 17.16. TOGGLE

## Time-based functions

### TON: On-delay

This function block delays a signal from the on-time by a specified duration.

<https://www.xplore-dna.net/mod/page/view.php?id=167>

<https://content.helpme-codesys.com/en/libs/Standard/Current/Timer/TON.html>

### TOF: Off-delay

This function block extends a signal from the off-time by a specified duration.

<https://www.xplore-dna.net/mod/page/view.php?id=168>

<https://content.helpme-codesys.com/en/libs/Standard/Current/Timer/TOF.html>

### TONOF: On/Off Delay

<http://oscat.de/images/OSCATBasic/oscat_basic333_de.pdf>
Page 215

### TP: Pulse Generation

<https://www.xplore-dna.net/mod/page/view.php?id=166>

<https://content.helpme-codesys.com/en/libs/Standard/Current/Timer/TP.html>

## Querying Signal Edges of an Operand

Edge Detection

<https://www.xplore-dna.net/mod/page/view.php?id=158>

### R_TRIG

<https://content.helpme-codesys.com/de/libs/Standard/Current/Trigger/R_TRIG.html>

<https://www.plcacademy.com/function-block-diagram-programming/>

--> at R_TRIG Function Block

Source: <https://www.plcacademy.com/function-block-diagram-programming/>

### F_TRIG

<https://content.helpme-codesys.com/de/libs/Standard/Current/Trigger/F_TRIG.html>

<https://www.plcacademy.com/function-block-diagram-programming/>

--> at F_TRIG Function Block

Source: <https://www.plcacademy.com/function-block-diagram-programming/>