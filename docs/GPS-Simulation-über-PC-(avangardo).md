# GPS Simulation über PC (avangardo)

```{index} single: GPS Simulation über PC (avangardo)
```

## 🎧 Podcast

* [Automatisierung 4.0: Warum Software die Hardware überholt und was das für deine Skills bedeutet](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/Automatisierung-4-0-Warum-Software-die-Hardware-berholt-und-was-das-fr-deine-Skills-bedeutet-e375eqs)
* [Der E_CTU in der IEC 61499: Ereignisgesteuertes Zählen und warum der Minimalist im Maschinenbau überzeugt](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/Der-E_CTU-in-der-IEC-61499-Ereignisgesteuertes-Zhlen-und-warum-der-Minimalist-im-Maschinenbau-berzeugt-e3a9qnq)
* [IEC 61499 vs. 61131: Notwendige Evolution oder überflüssige Komplikation für das IIoT?](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/IEC-61499-vs--61131-Notwendige-Evolution-oder-berflssige-Komplikation-fr-das-IIoT-e3ahcb0)
* [SINT, INT, DINT: Warum die Wahl des Datentyps über Effizienz und Fehler entscheidet](https://podcasters.spotify.com/pod/show/iec-61499-grundkurs-de/episodes/SINT--INT--DINT-Warum-die-Wahl-des-Datentyps-ber-Effizienz-und-Fehler-entscheidet-e3673b8)
* [800 PS Hightech-Riese: Was die Betriebsanleitung des ROPA Tiger 6S über moderne Landwirtschaft und extreme Sicherheit verrät](https://podcasters.spotify.com/pod/show/ms-muc-lama/episodes/800-PS-Hightech-Riese-Was-die-Betriebsanleitung-des-ROPA-Tiger-6S-ber-moderne-Landwirtschaft-und-extreme-Sicherheit-verrt-e3aub4t)

## 📺 Video

* [2025-02-23 11-43-44 Fusion 360 Übersicht Tutorials](https://www.youtube.com/watch?v=djM9ndIfp-0)
* [2025-03-11 16-53-43 Watch über App, nicht über Ressource](https://www.youtube.com/watch?v=bGwFMVQBj3k)
* [2025-03-15 15-57-14 Arithmetischer Überlauf](https://www.youtube.com/watch?v=TLanGc-c9Ww)
* [2025-03-15 16-27-21 Arithmetischer Überlauf führt zu Division durch 0.](https://www.youtube.com/watch?v=7CyOJPYJVz0)
* [2025-07-20 21-12-23  Fusion 360 Schematic SPICE Simulation deutsch](https://www.youtube.com/watch?v=jHT9nZIXxGU)

## Hardware

## Software

avangardo gibt es leider nicht mehr !

av_gps_generator_pro.zip entpacken, setup.exe ausführen,

GPS Generator PRO v.4.2.2 ist vermutlich die aktuelle Version.

Programm starten,

Hammertelgen.shp importieren:

```{image} https://user-images.githubusercontent.com/69573151/111999138-fa52af00-8b1c-11eb-9cd4-5751e8f06602.png
```

Wichtige Einstellungen:

```{image} https://user-images.githubusercontent.com/69573151/111999784-95e41f80-8b1d-11eb-9009-6bfc465d8229.png
```

```{image} https://user-images.githubusercontent.com/69573151/111999806-9ed4f100-8b1d-11eb-8240-63956069008a.png
```

```{image} https://user-images.githubusercontent.com/69573151/111999886-b2805780-8b1d-11eb-9b8a-669c46bc6f80.png
```

Die Baudrate hier muss mit der Baudrate auf dem CCI1200 übereinstimmen.

CCI.Config => GPS => Baudrate

```{image} https://user-images.githubusercontent.com/69573151/112003417-2d973d00-8b21-11eb-919b-f2099bad6d7e.png
```

als Port-Name tragen Sie in der Regel //./COM ein, nur in sehr seltenen Fällen COM.

die Port Nummer ersehen Sie aus dem Geräte Manager der Systemsteuerung:

```{image} https://user-images.githubusercontent.com/69573151/112002880-a3e76f80-8b20-11eb-8107-2c2f721f626c.png
```

Literatur:

<https://web.archive.org/web/20211028015532/http://www.avangardo.com/how-to/>

(Original-Seite nicht mehr verfügbar)