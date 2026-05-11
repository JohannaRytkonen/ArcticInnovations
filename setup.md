Ennen kuin päästään tositoimiin, tarvitset turvallisen harjoitteluympäristön. Tarvitset VirtualBox-virtualisointiohjelman ja virtuaalikoneen, jonka lokeja aloitat tutkimaan.

1. Asenna VirtualBox

   Voit ladata VirtualBox-ohjelman virallisislta sivuilta:
   https://www.virtualbox.org

   Valitse asennuspaketti koneesi käyttöjärjestelmän mukaisesti ja lataa se. Latauksen jälkeen kaksoisklikkaa ladattua tiedostoa, jolloin asennus alkaa. Tässä vaiheesa et tarvitse mitään erityisiä asetuksia, jote voit jatkaa eteenpäin painamalla aina "next".

   Asennuksen jälkeen VirtualBoxin ikkunassa valitse oikealta "Expert Mode".

   Lataa sen jälkeen viralliselta sivustolta Extension Pack. Huomaathan valita aiemmin asentamaasi virtualboxin versiota vastaavan extension packin.

   Kun lataus on valmis, palaa VirtualBox-ikkunaan. Valitse "Tools" ja sen jälkeen "Install" ja alapuolelta löydär extension packin, jonka latasit äsken.

2. Tuo kohdekone

   Okei seuraavksi tuodaan VirtualBoxiin labrakone, jota pääset pian tutkimaan. Kyseessä on Linux-palvelin, johon on tehty muutoksia valmiiksi sinulle. Palvelimen nimi on ArcticInnovations-server ja saata ladattua tiedoston alapuolellta.

   Lataa ArcticInnovations-server (.ova)

   Latauksen jälkeen:

   1. Avaa Virtualbox
   2. Klikkaa "file" -> "Import Appliance
   3. Valitse lataamasi .ova-tiedosto
   4. Klikkaa "Finish"

3. Liitetään virtuaalikone Internal Network -verkkoon

    Internal Network -verkko tarkoittaa sitä, että virtallikone toimii täysin eristetyssä ympäristössä. Mikään ulkopuolelta ei siis pääse sisään ja virtuaalikoneella ei ole internetyhteyttä, joten virtuaalikoneesi ei myöskään pääse lähettäämään mitään haitallista ulospäin. Tämä on turvallinen tapa tehdä labra.

     1. Avaa Virtualbox
     2. Tarkista, että ArcticInnovations-server on sammutettuna
     3. Valitse ArcticInnovations-server
     4. Klikkaa "Settings" -> "Network"
     5. Valitse "Adapter 1" -välilehti
     6. "Enable Network Adapter" pitää olla valittuna
     7. "Attached to:" -alasvetovalikosta valitse "Internal Network"
     8. "Name:" valitse oletusverkko "intnet"
     9. "Cable connected" pitää olla valittuna
     10. Klikkaa "OK"

4. Käynnistä kone ja kirjaudu sisään

   Klikkaa ArcticInnovations-serveriä ja käynnistä kone vihreästä nuolesta "Start"

   Käyttäjätunnuksesi on:
   Salasanasi on:

   

      

   
