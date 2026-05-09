Noin, nyt olet selvittänyt mitä hyökkääjä on tehnyt, mihin hän on päässyt ja mitä tietoja nähnyt. Tässä tapauksessa näyttäisi siltä, että pahimmalta on säästytty. Vaikka hyökkääjällä on ollut pääsy järjestelmään, hän ei ole korottanut oikeuksia eikä siis hyvin todennäköisesti ole nähnyt erittäin salaisia ja arvokkaita asiakirjoja. Haluat kuitenkin varmitua, ettei tallaiseen tilanteeseen enää jouduta uudelleen ja mietit mitä kaikkea pitäisi tehdä, jotta tällaisiltä tilanteilta vältyttäisiin jatkossa. Muistat nähneesi palvelimella myös error-lokeja. Voisiko niistä olla apua? Voisiko niistä nähdä, mikä kaikki palvelimella on rikki ja vaatisi korjausta. Niimpä päätät käydä vilkiasemassa myös nämä lokit:

sulo zless /var/log/apache2/error.log.6.gz

Ensimmäinen loki kertoo uudelleen käynnistyksistä ja viimeiset rivit huomauttavat, että ServerName ei ole asetettu ja apache käyttää IP osoitetta. Tämä ei ole kriittinen huomio, mutta kertoo jotakin puutteellisesta konfiguraatiosta. Jatketaan seuraavaan lokiin:

sulo zless /var/log/apache2/error.log.5.gz

Ensimmäiset rivit eivät kerro mitään mielenkiintoista, mutta niiden jälkeen näkyy kymmeniä rivejä PHP-virheitä, jotka ovat tulleet samasta hyökkääjän IP-osoitteesta:

PHP Warning: Undefined array key "user_token" in /var/www/html/dev/login.php on line 18

Tämä kertoo sovellusvirheestä. Tämä virhe liittyy puuttuvaan user_token-parametriin ja kertoo huonosta syötteen käsittelystä. Syötteiden validoinnissa on siis puutteita. 

Näiden virheiden alapuolella on seuraavanlaista tietoa:

PHP fatal errror: Uncaught mysqli_sql_exeptation: You have an error in your SQL syntax: check the manual that corresponds to your MariaDB server version for the right syntax to use near ''1''' at line 1

Tämä tarkoittaa sitä, että SQL-kysely on rikki syntaksiltaan. Virhe johtuu todennäköisesti käyttäjän syötteestä. Kohassa ''1''' käyttäjä on syöttänyt heittomerkkejä verkkosivun lomakekenttään testatakseen toimintaa ja tehdäkseen hyökkäyksen. Tämä viittaa siihen, että järjestelmässä on SQL injektio -haavoittuvuus. Virhe myös palauttaa liikaa tietoa. Tästä nähdään, että käytössä on muun muassa MariaDB.

Okei, siirrytään katomaan seuraava error-loki:

sulo zless /var/log/apache2/error.log.4.gz

Tästä lokista ei ilmene mitään meitä kiinnostavaa tietoa. Jatketaan siis seuraavaan lokiin:

sulo zless /var/log/apache2/error.log.3.gz

