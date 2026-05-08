Lokit eivät kuitenkaan loppuneet tähän, joten avataan järjestyksessä seuraava loki:

sudo zless /var/log/apache2/access.log.3.gz

Siirrytään lokin alkuun (ylös nuolella) ja aletaan tarkatella sisältöä. Ensimmäisillä riveillä on GET-pyyntöjä dvwa ja dvwa/login.php -sivuille. Nämä pyynnöt on tehty komentokehotteena, sillä rivien lopussa näkyy "curl/8.15.0". Näistä riveistä ei kuitenkaan ilmene muuta erityisesti kiinnostavaa.

Lokin viimeiset 10 riviä ovat kuitenkin mielenkiintoisia. Kaikki rivit ivat keskenään identtisiä:

192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"
192.168.30.4 - - [01/May/2026:8:56:13 +0000] "GET /dvwa/login.php HTTP/1.1" 200 1943 "-" "curl/8.15.0"

Samasta IP-osoitteessa on tullut sama GET-pyyntö dvwa/login.php-sivulle kymmenen kertaa saman sekunnin aikana ja pyyntö on lähetetty komentokehotteelta curl-työkalulla. Koska pyynnöt ovat tuleen nopeasti, ne eivät ole ihmisen tekemiä vaan todennäköisesti kysessä automatisoitu toiminta. Emme kuitenkaan voi vielä sanoa tätä vaihetta hyökkäykseksi, sillä GET-pyyntö ainoastaan avaa sivun uudelleen ja uudelleen, eikä aiheuta juurikaan sen enempää. Hyökkääjä mahdollisesti testaa sivua, tarkistaa vastauksia ja valmistautuu hyökkäykseen.

"JATKA"

