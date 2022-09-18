pconvert
===================================


Liegt hier:

M:\\Landmaschinenmechanik\\Unterricht\\SL\\DLG\\pconvert

Gestartet wird es mit pconvert.exe

Aufgrund des langsamen WLAN wieder ratsam das vorab auf den eigenen PC zu kopieren. 

So, hierzu noch 2 Hinweise:

Lizenzrechtlich:

Wir haben folgenden Hinweis erhalten von der DLG (www.DLG.org) DLG TestService GmbH, (https://de.wikipedia.org/wiki/Deutsche_Landwirtschafts-Gesellschaft)

Wie telefonisch besprochen, ist der Käufer zusätzlich berechtigt die Software für unterrichtszwecke an der Meisterschule zu kopieren. Die Software darf ausschließen an der Meisterschule und nicht kommerziell eingesetzt werden.

Ich will Sie darauf hinweisen dass diese Software technisch nicht geschützt ist (Lizenzschlüssel, Dongle). SIE selbst sind also dafür verantwortlich KEINE Kopie der Software für Kommerzielle Zwecke anzufertigen. Sollten Sie die Software kommerziell einsetzen wollen müssen Sie bei der DLG eine Lizenz für 1995€ kaufen. 

Wenn Sie im Nachgang des Unterrichts nächste Woche oder in Vorbereitung mit der Software zuhause **üben** sehe ich das als Unterrichtszweck. 

Wenn ein Kunde zu ihnen kommt und Sie reparieren damit seine Maschine und sie stellen Eine Rechnung ist das klar Kommerziell. 

https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/Install-ISOBUS-Environment/wiki/pconvert

*   Hier kommen in Zukunft die Anleitungen rein für Pconvert. 

So,

ich beschreibe das mit dem PEAK USB Adapter, https://www.peak-system.com/PCAN-USB.199.0.html

andere Adapter werden auch unterstützt:

siehe Liste ganz am Ende dieser Mail.

Als erstes brauchen wir den Basis-Treiber von PEAK:

Gerätetreiber-Setup für Windows von o.g. Seite runterladen,

bei Optionen mindestens PCAN Basic und Gerätetreiber wählen.

Jetzt sollte man mit PCAN-View prüfen ob der PEAK funktioniert.

Nun folgt die Installation des erweiterten Treibers (Pconvert geht auch ohne, aber wenn man später PCAN-View mit Pconvert oder Busmaster mit Pconvert zusammen benutzen möchte ist der erweiterte Treiber besser)

M:\\Landmaschinenmechanik\\Unterricht\\SL_**\\DLG\\pconvert\\progs\\can\\CANdriver\\peak\\OEM\\Redistributable

Dann legt man im NetCFG32 ein Netz an:

M:\\Landmaschinenmechanik\\Unterricht\\SL_**\\DLG\\pconvert\\progs\\can\\CANdriver\\peak

Dieses nennt man dlg\_net

.. image:: https://user-images.githubusercontent.com/69573151/94334981-67efa980-ffd8-11ea-9fd8-113bc6955ae7.jpeg

Jetzt startet man pconvert:

M:\\Landmaschinenmechanik\\Unterricht\\SL_**\\DLG\\pconvert dort pconvert.exe

dann CAN – Can Tools – Net Client Mode einen Haken setzen

in dem erscheinenden Fenster wählt man „TRACE“ und schon zeigt er was am Bus los ist:

.. image:: https://user-images.githubusercontent.com/69573151/94334983-67efa980-ffd8-11ea-8ee0-ed068a57ed0d.png

Sollte das nicht funktionieren wählt man nochmal CAN – Can Tools – Net Client Table – Close and Set Net Client.

Insbesondere wenn das Netz nicht DLG\_net heisst.**

.. image:: https://user-images.githubusercontent.com/69573151/94334982-67efa980-ffd8-11ea-8b0f-3a9e9a771d35.png

.. image:: https://user-images.githubusercontent.com/69573151/93021356-73a79d00-f5e2-11ea-8593-d1aa723ad8e5.png

.. image:: https://user-images.githubusercontent.com/69573151/93021359-7e623200-f5e2-11ea-9546-445c9bef2f61.png

.. image:: https://user-images.githubusercontent.com/69573151/93021370-891cc700-f5e2-11ea-81d8-33800bf482d4.png

.. image:: https://user-images.githubusercontent.com/69573151/93021494-2677fb00-f5e3-11ea-8136-af3307e95905.png
