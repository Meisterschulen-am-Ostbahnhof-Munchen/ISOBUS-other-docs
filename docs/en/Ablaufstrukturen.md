# Flow Structures

<https://de.wikibooks.org/wiki/C-Programmierung:_Kontrollstrukturen>

## `if` statement

(also called `WENN-Funktion` or <https://excelhero.de/funktionen/excel-wenn-funktion/> in the Microsoft-heavy German education system)

Example: <https://onlinegdb.com/wWii98Jv3>

### `else` statement

The `else` statement is optional.

Example: <https://onlinegdb.com/CFbDfaSX4>

Example: <https://onlinegdb.com/CnSm1M4fn>

## `switch` statement

Example: <https://onlinegdb.com/5_n5GjAkT>

## **Conditional Expression**

Example: <https://www.onlinegdb.com/KURn4fl_M>

In this example you will see:


```{code-block} C
:caption: C-Code

printf(" w = %s", w ? "Wahr" : "Falsch");
```
This means:

If w is true, the text "True" is displayed, and vice versa.

In the example <https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/cci_EasyExample/blob/master/EasyExample/components/AppIso/App_VTClientLev2.c>, you will see the following expression in line <https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/cci_EasyExample/blob/master/EasyExample/components/AppIso/App_VTClientLev2.c#:~:text=IsoVtcCmd_NumericValue(u8Instance%2C%20ObjectPointer_Tagesziel%2C%20%20Tageszaehler%20%20%3E%3D%20Tagesziel%20%20%3F%20OutputString_ZielErreicht%20%3A%20ID_NULL)%3B>:


```{code-block} C
:caption: C-Code

IsoVtcCmd_NumericValue(u8Instance, ObjectPointer_Tagesziel,  Tageszaehler  >= Tagesziel  ? OutputString_ZielErreicht : ID_NULL);
```
Here, the green box with the text "Target Achieved" is shown or hidden.

## **For Loop**

Example: <https://onlinegdb.com/_zCm5ZHsK>

Example: <https://onlinegdb.com/_jcCRpYe3>

## **While Loop**

Example: <https://onlinegdb.com/AnrnomF39G>

## **Do-While Loop**

Example: <https://onlinegdb.com/LQIBQwXest>