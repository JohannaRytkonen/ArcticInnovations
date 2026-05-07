Avataan seuraava loki:

sudo zless /var/log/apache2/access.log.2.gz

Lokin ensimmäiset rivit näyttävät taas normaalilta liikenteeltä. Muutaman rivin jälkeen on kuitenkin taas nähtävissä kymmenen GET-pyyntöä samasta IP-osoitteesta hyvin nopealla tahdilla.

Tämän jälkeen lokissa on kymmeniä POST-pyyntöjä dev/login.php-sivulle. Kuten muistamme edeltä POST-pyyynnöt lähettävät dataa esimerkiksi käyttäjätunnuksia ja salasanoja palvelimelle. Hyökkääjä yrittää siis kirjautua järjestelmään toistuvasti. Tämä vaikuttaa jo oikealta brute force hyökkäykseltä, sillä POST-pyyntöjä on tullut kymmeniä pienen ajan sisällä. Hyökkääjä yrittää arvata tunnuksia useilla eri yrityksillä.

Heti näiden kirjautumisyritysten jälkeen lokissa näkyy rivit:

GET /dev/vulnerabilities/sqli/?id=1
GET /dev/vulnerabilities/sqli/?id=1'

Näissä on oleellista huomta kohat ?id=1 ja ?id=1'
Tässä kohdassa hyökkäjä testaa URL-parametria. Yksittäinen heittomerkki on perinteinen SQL injection testi. Hyökkääjä yrittää nähdä rikkoutuuko sovellus, syntyykö SQL-vihettä tai käsitelläänkö syöte turvallisesti.

Seuraavksi SQL-testien jälkeen tehdään kirjautumisyritys POST-pynnöllä dev/login.php-sivulle. Hyökkääjä yrittää päästä sisälle järjestelmään.

Lokeissa näkyy GET dev/index.php, mikä voi tarkoittaa sitä, että hyökkääjä on saanut pääsyn järjestelmään.

Lokitiedoista nähdään myös että ensin y on tehty curl-työkalulla ja myöhemmässä vaiheessa selaimella. Hyökkääjä on siis siirtynyt automatisoidusta komennoista selaimen käyttöön.

Lokissa näkyy myös rivi:

GET /dev/vulnerabilities/sqli/

Tässä hyökkääjä on siirtynyt SQL injection -toiminnallisuuteen ja etsii mahdollista haavoittuvuutta.

GET /dev/security.php
POST /dev/security.php
GET /dev/security.php

Nämä rivit vaikuttavat sitl, että hyökkääjä pyrkiin edelleen muuttamaan tietoturva-asetuksia.

GET /dev/vulnerabilities/sqli/?id=1&Submit=Submit

Tässä hyökkääjä on lisännyt lomakkeelle arvon id=1 ja lähettänyt lomakkeen. Seuraavaksi hyökkääjä lähettää lomakkeen uusilla tiedoilla:

GET /dev/vulnerabilities/sqli/?id=1%27&Submit=Submit

Tässä %27 on URL-koodattu heittomerkki. Tämä on SQL injection -testi. Käyttäjä ilmeisesti yrittää rikkoa SQL-kyselyn. Rivillä näkyy myös palvelimen vastaus 500, mikä tarkoittaa palvelinvirhettä. Palvelin ei siis osannut käsitellä syötettä turvallisesti. Tämä voi tarkoittaa sitä, että palvelin on haavoittuva SQL injection -hyökkäyksille.

Lokist löytyy myös tällainen rivi:

GET /dev/vulnerabilities/sqli/?id=1'or+1%3D1

Tässä lomakkeelle on syötetty tieto id=1'or+1%3D1 ja käytännössä tämä tarkoittaa 1'or1=1. Hyökkääjä yrittää ohittaa sovelluksen normaalin toiminna ja tietoa enemmän kuin pitäisi. Tällä tavalla voi saada palvelimen palauttamaan kaikki rivit. Tämä ei ole pelkkää tastaamista vaan oikea SQL injection -hyökkäys.

Seuraavaksi hyökkääjä siirtyy XXS-sivulle ja jatkaa haavoittuvuuksien tutkintaa siellä:

GET /dev/vulnerabilities/xss_r/

Ensin hyökkääjä tutkii testisyötteellä ?name=test, miten sovellus käsittelee sitä. Tämän jäkeen hyökkääjä tekee oikean hyökkäyksen, joka näytää lokissa tältä:

?name=%3Cscript%3Ealert%28%27XSS%27%29%3C/script%3E

Tämä on URL-koodattua HTML- ja JavaScript-koodia. Mikäli sovelluksessa on haavoittuvuus, voi hyökkääjällä olla mahdollisuus varastaa sessioita, tehsä käyttäjän nimissä toimintoja tai ohjata haitalliselle sivustolle. XXS-hyökkäyksessä haitallista koodia syötetään verkkosovellukseen.
