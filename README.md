# daylength
```
## Project setup
```
npm install
```
npm install vue
```
npm install leaflet vue2-leaflet --save
```
### Compiles and hot-reloads for development
```
npm run serve
```
Rakenduse kirjeldus
Kasutatud tehnoloogia:
Rakendus on tehtud Vue.js raamistikus. Kasutatud versiooni Vue 2.6.11
Lisaks on kasutatud kaartide jaoks leaflet 1.7.1 ja vue2-leaflet 2.7.0, millest viimane on ehitatud leafleti peale ja peaks tegema leafleti kasutamist mugavamaks.

Rakenduse ülesehitus:
1.	Kasutaja sisestadu andmete liikumine ja kuvamine.
Rakendus koosneb kolmest komponendist: App.vue, DayLengthEnter.vue, Map.vue.
App.vue on põhikomponent, mis lapskomponendiks on DayLengthEnter ja omakorda, mille lapskomponendiks on Map.vue.
DayLengthEnter komponendis on sisendväljad, mis võtavad vastu info kraadides, minutites ja sekundites (Sisendinfo valideerimine jäi tegemata) ning samuti kuupäeva. Pikkuskraadid peaks olema maksimaalselt 0 – 90 kraadi ja laiuskraadid 0 - 180 kraadi ning raadionuppudega N,S,E,W saab valida, kas on mõeldud põhja, lõuna, ida või lääne koordinaate.
Sisendandmed on seotud data() väljadega ning nupuga “Get info” kutsutakse välja meetod getData, mis võtab sisendandmed, arvutab pikkus- ja laisuskraadid ümber kümnendsüsteemi arvudeks, salvestab need ka data() muutjatesse (et saaks edasi saata ka lapskomponendile) ning paneb kokku sõne (Stringi), millega tehakse paring serverisse ja tagastatud info salvetakase data() väljale dayInfo, millest kuvatakse ka info kasutajale ekraanile.
Kümnendarvudeks ümberarvutatud pikkus- ja laiuskraadid edastatakse ka lapskomponendile Map.vue.
Lapskomponent Map.vue saab props{ } muutujate kaudu kasutajasisestatud koordinaadid vanemkomponent DayLengthenter.vue käest sõneks tehtud kümnendarvudena ning läbi computed{} meetodi edastatakse need ka kaardi asukoha ja kaardil oleva märgise liigutamiseks.

2.	Kaardil klikitud asukoha andmete liikumine.
Kaardi pealt andmete saamine toimub lapskomponendis Map.vue. Kui kasutaja klikib kaardile, siis jälgitakse seda sündmust ja klikimise sündmus kutsub välja meetodi @click="latLng", mis võtab argumendiks ka sündmuse enda info “event” see sündmus sisaldab pikkus- ja laiuskoordinaate, mis on pärit kaardil klikitud kohast ning meetod “latLng” genereerib omakorda sündmuse nimega “changedCoordinates”, mille kaudu saab vanemkomponent DayLengthEnter.vue kätte kaardil klikitud pikkus- ja laiuskraadid ning need salvestatakse data() meetodi muutujate alla. Samuti kutsub see “changedCoordinates” sündmus välja meetodi getData2. Nüüd getData2 meetodis kasutatakse neid uuendatud andmeid, mis just DatLengthEnter.vue komponendis data() meetodi muutujate alla salvestati ja nende põhjal saadetakse päring andmete toomiseks (GET/fetch request). Aga samuti lähevad need pikkus- ja laiusraadide andmed tagasi lapskomponendile ja neid kasutatakse kaardipeal marker liigutamiseks ja kaardi enda liigutamiseks. Seega need andmed, mis pärinevad lapskomponendist Map.vue käivad ära vanemkomponendis ja tulevad tagasi lapskomponenti. See oli vajalik selleks, et andmed pärinevad kahest kohast, aga siis on ühtne tee kaardi juurde nii vanemkomponendist tulnud andmetel, kui palskomponendist tulnud andmetel. Ma ei tea, kas nii on õige teha, aga teistmoodi hetkel ei oskanud.
