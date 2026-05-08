Okei, meillä on vielä lokeja käymättä läpi. Avataan siis seuraava loki:

sudo zless /var/log/apache2/access.log.2.gz

Lokin ensimmäiset rivit näyttävät taas normaalilta liikenteeltä. Muutaman rivin jälkeen on kuitenkin taas nähtävissä kymmenen GET-pyyntöä samasta IP-osoitteesta hyvin nopealla tahdilla. Tällä kertaa pyyntöjen välillä on muutaman sekunnin tauko.

Näiden GET-pyyntöjen jälkeen lokissa on kymmeniä POST-pyyntöjä dev/login.php-sivulle. Kuten muistamme edeltä, POST-pyyynnöt lähettävät dataa, esimerkiksi käyttäjätunnuksia ja salasanoja palvelimelle. Hyökkääjä yrittää siis kirjautua järjestelmään toistuvasti. Tämä vaikuttaa jo oikealta hyökkäykseltä, sillä POST-pyyntöjä on tullut kymmeniä pienen ajan sisällä. Hyökkääjä yrittää arvata tunnuksia useilla eri yrityksillä. Tällaista hyökkäystä kutsutaan brute force -hyökkäykseksi.

Heti näiden kirjautumisyritysten jälkeen lokissa näkyy rivit:

GET /dev/vulnerabilities/sqli/?id=1
GET /dev/vulnerabilities/sqli/?id=1'

Näissä on oleellista huomata kohat ?id=1 ja ?id=1'
Tässä tilanteessa käyttäjä syöttää verkkosivuilla olevaan lomakekenttään arvoja tarkoituksenaan testata URL-parametria. Yksittäinen heittomerkki on perinteinen SQL-injektio-testi. Hyökkääjä yrittää nähdä rikkoutuuko sovellus, syntyykö SQL-virhettä tai käsitelläänkö syöte turvallisesti.

Seuraavksi SQL-testien jälkeen tehdään kirjautumisyritys POST-pynnöllä dev/login.php-sivulle. Hyökkääjä yrittää ilmeisesti päästä sisälle järjestelmään.

Tämän jälkeen lokissa näkyy "GET dev/index.php", mikä voi tarkoittaa sitä, että hyökkääjä on saanut pääsyn järjestelmään. Index.php-sivu on verkkosivuston oletusetusivu, joka näytetään automaattisesti, kun käyttäjä menee sivuston osoitteeseen ilman tarkkaa tiedostopolkua eli esimerkiksi kirjautumissivun kautta.

Lokitiedoista nähdään myös että ensin pyyntöjä on tehty curl-työkalulla ja myöhemmässä vaiheessa selaimella. Hyökkääjä on siis siirtynyt automatisoidusta komennoista selaimen käyttöön.

Okei, jatketaan. Skrollataan lokitietoja alaspäin. Hyökkääjä on tehnyt myös GET-pyynnön dev/vulnerabilities/sqli-sivulle:

GET /dev/vulnerabilities/sqli/

Tässä hyökkääjä on siirtynyt SQL injection -toiminnallisuuteen ja etsii mahdollista haavoittuvuutta. Seuraavaksi käyttäjä on löytänyt security-sivun:

GET /dev/security.php
POST /dev/security.php
GET /dev/security.php

Käyttäjä ilmeisesti pyrkiin edelleen muuttamaan tietoturva-asetuksia ja valmistelee mahdollisesti hyökkäystä. 

Jatketaan taas eteenpäin. Alempana lokissa näkyy, että hyökkääjä syöttää verkkosivun lomakkeelle uudelleen arvoja:

GET /dev/vulnerabilities/sqli/?id=1&Submit=Submit

Tässä hyökkääjä on lisännyt lomakkeelle arvon id=1 ja lähettänyt lomakkeen. Seuraavaksi hyökkääjä lähettää lomakkeen uusilla tiedoilla:

GET /dev/vulnerabilities/sqli/?id=1%27&Submit=Submit

Tässä %27 on URL-koodattu heittomerkki. Tämä on taas samanlainen SQL-injektio -testi kuin edelläkin. Käyttäjä mahdollisesti yrittää rikkoa SQL-kyselyn. Rivillä näkyy myös palvelimen vastaus 500, mikä tarkoittaa palvelinvirhettä. Palvelin ei siis osannut käsitellä syötettä turvallisesti. Tämä voi tarkoittaa sitä, että palvelin on haavoittuva SQL-injektio-hyökkäyksille.

Alempaa löytyy myös tällainen rivi:

GET /dev/vulnerabilities/sqli/?id=1'or+1%3D1

Tässä lomakkeelle on syötetty tieto id=1'or+1%3D1 ja käytännössä tämä tarkoittaa 1'or1=1. Hyökkääjä yrittää ohittaa sovelluksen normaalin toiminnan ja saada tietoa enemmän kuin pitäisi. Tällä tavalla voi saada palvelimen palauttamaan kaikki rivit. Tämä ei ole enää pelkkää testaamista vaan oikea SQL-injektio-hyökkäys. SQL-injektio-hyökkäyksessä on siis kyse siitä, että hyökkääjä syöttää verkkosovellukseen haitallista koodia, jotta saisi pääsyn tietokantaan. 

Seuraavaksi hyökkääjä siirtyy XSS-sivulle ja jatkaa haavoittuvuuksien tutkintaa siellä:

GET /dev/vulnerabilities/xss_r/

Ensin hyökkääjä tutkii testisyötteellä ?name=test, miten sovellus käsittelee syötettä. Tämän jäkeen hyökkääjä tekee oikean hyökkäyksen, joka näytää lokissa tältä:

?name=%3Cscript%3Ealert%28%27XSS%27%29%3C/script%3E

Tämä on URL-koodattua HTML- ja JavaScript-koodia. Mikäli sovelluksessa on haavoittuvuus, voi hyökkääjällä olla mahdollisuus varastaa sessioita, tehdä käyttäjän nimissä toimintoja tai ohjata haitalliselle sivustolle. XSS-hyökkäyksessä (Cross-Site Scripting) haitallista koodia siis syötetään verkkosivulle ja tämä koodi suoritetaan toisen käyttäjän selaimessa silloin, kun toinen käyttäjä avaa kyseisen sivun. Näin tapahtuu, koska sovellus ei käsittele käyttäjän syötettä turvallisesti.

Noin, nyt olet jo saanut aika paljon tietoa tapahtumista. Käyttäjä on siis tehnyt tiedustelua, yrittänyt murtaa salasanoja brute force hyökkäyksellä ja muokata tietoturva-asetuksia. Hyökkääjä on lisäksi tehnyt SQL-injektio-hyökkäyksen sekä XSS-hyökkäyksen.

"JATKA"
