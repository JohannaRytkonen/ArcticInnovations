Olet nyt saanut tunnukset ja kirjautunut sisään työkoneellesi. Esihenkilösi saapuu esittäytymään ja kertoo, että palvelimella on havaittu normaalista poikkeavaa liikennettä. Pääset siis saman tien tositoimiin. Sinulle annetaan tehtäväksi selvittää onko palvelimeen kohdistunut hyökkäys ja jos on, mitä on tapahtunut?

Muistat, että palvelimet keräävät tietoja tiedostoihin, joita kutsutaan lokeiksi. Näistä lokeista voisi ehkä näkyä jotakin ja päästä kärryille siitä, mitä on tapahtunut. Päätät siis aloittaa tutkimalla lokitiedostoja.

Aloita selvittämällä mitä lokitiedostoja palvelimella on:

sudo ls /var/log/apache2/

Huomaat, että lokitiedostoja on useita. Hyökkäys ei välttämättä ole viimeisimmässä lokitiedostossa vaan se on voinut tapahtua jo aiemmin. Päätät aloitta tutkimisen vanhimmasta lokista, jolloin voit saada parhaan kuvan mahdollisen hyökkäyksen kehittymisestä. Lokit toimivat siten, että viimeisin loki on muodossa access.log ja tätä edeltävä access.log.1 eli mitä suurempi numero, sitä varhaisemmista tapahtumista on kyse.

Avataan vanhin tiedosto less-tuökalulla. Koska tiedosto on .gz tarvitaan tälle tiedostotyypille sopiva lees-työkalu:

sudo zless /var/log/apache2/access.log.4.gz

Less-työkalulla pääsee liikkumaan alas ja ylös nuolimäppäimillä, välilyönti vie sivun alaosaan, b sivun yläosaan ja näppäimella q voidaan poistua näkymästä.

Ennen kuin voit tunnistaa hyökkäyksiä, sinun täytyy ymmärtää miltä normaali liikenne näyttää. Tässä on esimerkki lokitiedosta:

192.168.30.4 - - [29/Apr/2026/15:42:57 +0000] "GET HTTP/1.1" 200 3460 "-"Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0"

192.168.30.4 on IP-osoite, josta pyyntö on tullut
[29/Apr/2026/15:42:57 +0000] on aikaleima, jolloin pyyntö on tapahtunut
"GET HTTP/1.1" kertoo mitä pyydettiin, GET avaa verkkosivun
200 on statuskoodi, joka kertoo palvelimen vastauksen (200-> OK, 302-> uudelleenohjaus, 404-> sivua ei löytynyt, 500-> palvelinvirhe)
3460 kertoo vastauksen koon eli paljonko dataa palautettiin
"-" kertoo, mistä käytääjä tuli, tämä on usein tyhjä curl-tyyppisissä pyynnöissä
"Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0" kertoo, millä työkalulla pyyntö tehtiin. Mozilla/5.0 on historiallinen jäänne ja käytännössä kaikki selaimet ilmoittavat tämän tiedon (yhteensopivuus), X11; Linux x86_64 on käyttöjärjestelmä eli 64-bit Linux, rv:140.0 on selaimen versio, Gecko/20100101 on selaimen moottori ja Firefox/140.0 on varsinainen selain ja sen versio

Epänormaalia käyttöä voidaan havainnoida yksittäisten lokitietojen lisäksi pyyntöjen tahdilla ja määrällä. Normaalissa liikenteessa tahti on yleensä rauhallinen ja pyynnöt kohdistuvat eri sivuille. Suuri määrä pyyntöjä samaan kohteeseen voi kertoa hyökkäyksestä. 

Palataanpa takaisin lokiin. Siirrytään nuolella tiedoston alkuun (ylös) ja lähdetään katsomaan liikennettä.

Ensimmäiset rivit näyttävät tavalliselta liikenteeltä. Näiden jälkeen kuitenkin on nähtävissä hieman erilaisia rivejä:

::1 - -  [30/Apr/2026:10:42:43 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"
::1 - -  [30/Apr/2026:10:45:52 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"
::1 - -  [30/Apr/2026:10:46:50 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"
::1 - -  [30/Apr/2026:10:50:07 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"
::1 - -  [30/Apr/2026:11:08:40 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"
::1 - -  [30/Apr/2026:11:09:13 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"
::1 - -  [30/Apr/2026:11:11:24 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"
::1 - -  [30/Apr/2026:11:12:10 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"
::1 - -  [30/Apr/2026:11:27:51 +0000] "GET /HTTP/1.1" 200 721 "-" "curl/8.5.0"

Tämä ei ole tavallista liikennettä. Pyyntöjä on tehty muutaman minuutin välein samaan osoitteeseen. Rivien lopussa oleva "curl/8.5.0" tarkoittaa sitä, ettei pyyntöä tehnyt tavallinen selain vaan pyyntö on tehty curl-komennolla komentoriviltä. Tässä tapauksessa kuitenkin huomataan, että ::1 tarkoittaa localhost IPv6 osoitetta eli pyyntö on tullut samalta koneelta, ei ulkopuolelta. Vaikka liikenne ei ole tavanomaista liikennettä, todennäköisesti tässä ei ole kyse hyökkäyksestä vaan esimerkiksi ylläpitoon liittyvästä testauksesta.
