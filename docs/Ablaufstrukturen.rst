Ablaufstrukturen
===================================

https://de.wikibooks.org/wiki/C-Programmierung:_Kontrollstrukturen

`if` -Anweisung
------------------------------------

(im Microsoft-lastigen Deutschen Bildungssystem auch `WENN-Funktion` genannt https://excelhero.de/funktionen/excel-wenn-funktion/)

Beispiel: https://onlinegdb.com/wWii98Jv3

`else` -Anweisung
.................................

die `else` -Anweisung ist optional

Beispiel: https://onlinegdb.com/CFbDfaSX4

Beispiel: https://onlinegdb.com/CnSm1M4fn

`switch` -Anweisung
------------------------------------

Beispiel: https://onlinegdb.com/5_n5GjAkT

**Bedingter Ausdruck**
------------------------------------

Beispiel: https://www.onlinegdb.com/KURn4fl_M

in diesem Beispiel sehen Sie:

.. code-block:: C
    :caption: C-Code
    
    printf(" w = %s", w ? "Wahr" : "Falsch");



Das bedeutet: 

ist w Wahr, wird der Text "Wahr" ausgegeben und umgekehrt. 

im Beispiel https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/cci_EasyExample/blob/master/EasyExample/components/AppIso/App_VTClientLev2.c sehen Sie in Zeile https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/cci_EasyExample/blob/master/EasyExample/components/AppIso/App_VTClientLev2.c#:~:text=IsoVtcCmd_NumericValue(u8Instance%2C%20ObjectPointer_Tagesziel%2C%20%20Tageszaehler%20%20%3E%3D%20Tagesziel%20%20%3F%20OutputString_ZielErreicht%20%3A%20ID_NULL)%3B) folgenden Ausdruck:

.. code-block:: C
    :caption: C-Code
    
    IsoVtcCmd_NumericValue(u8Instance, ObjectPointer_Tagesziel,  Tageszaehler  >= Tagesziel  ? OutputString_ZielErreicht : ID_NULL);


hier wird die Grüne Box ein oder ausgeblendet mit dem Text "Ziel Erreicht".

**For-Schleife**
------------------------------------

Beispiel: https://onlinegdb.com/_zCm5ZHsK

Beispiel: https://onlinegdb.com/_jcCRpYe3

**While-Schleife**
------------------------------------

Beispiel: https://onlinegdb.com/AnrnomF39G

**Do-While-Schleife**
------------------------------------

Beispiel: https://onlinegdb.com/LQIBQwXest
