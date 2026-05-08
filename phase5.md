Okei, seuraavaksi yritetään selvittää, mitä hyökkääjä on tehnyt järjestelmän sisällä.

Aloitetaan tutkiminen auth.log.1 ja auth.log -tiedostoista. Haetaan lokeista kaikki käyttäjän developer toimet, näistä voi selvitä jotakin. Koska rivejä voi olla paljon käytetään grep-työkalua poimimiseen ja avataan tiedot less-työkalulla, jotta niitä pääsee selaamaan:

sudo grep "developer" /var/log/auth.log.1 | less

Nyt pääset selaamaan tuloksia nuolinäppäimillä. Voit lisäksi tehdä hakuja näistä tiedoista kirjoittamalla: / hakusana

Näistä hakutuloksista voidaan hakea rivit, joissa esiintyy hyökkääjän IP-osoite 192.168.30.4 ja tuloksena saadaan muutama rivi. Näistä nähdään, että hyökkääjä on kirjautunut sisään 2.5.2026 kello 13:49:25 ja istunto on päättynyt 2.5.2026 kello 14:00:33. Tämä perusteella hyökkääjä olisi ollut kirjautuneena ainoastaan hieman yli 10 minuuttia.

Tämän jälkeen voidaan etsiä haun "developer" tuloksista esimerkiksi sanaa "sudo", mikä voi kertoa mahdollisista oikeuksien korotuksesta. Kaikki sudo-sanaan liittyvät hakutukset näyttävät kuitenkin olevan jo hyökkäystä edeltävältä ajalta, joten merkkejä oikeuksien korottamisesta ei löytynyt.

Etsitään tämä jälkeen vielä rivejä, joissa olisi sanat "developer" ja "session opened". Näillä hakukriteereillä voitaisiin nähdä, mikäli hyökkääjä on avannut istunnon root-oikeuksilla:

sudo grep "session opened" /var/log/auth.log.1 | grep "developer"

Tälläkään haulla ei löydy tietoa istunnon avaamisesta root-oikeuksilla.

Näiden hakujen perusteella näyttäisi siltä, ettei hyökkääjä ole nostanut oikeuksia ollessaan järjestelmän sisällä.

Tarkistetaan vielä .bash-history-tiedot. Se on lista komennoista, joita käyttäjä on ajanut terminaalissa.

sudo cat /home/developer/.bash_history

Tiedostosta huomataan, että viimeisimmän kirjautumisen jälkeen on heti ensimmäiseksi tiedusteltu omia tietoja komennolla "whoami" ja sen jälkeen lähdetty tutkimaan kansioita ja tiedostoja ja erityistä kiinnostusta on herättänyt kansio nimeltä arctic-data.

Nyt siis tiedetään, että hyökkääjä on päässyt järjestelmään sisään ja hän on ilmeisesti käynyt tutkimassa tai yrittänyt tutkia tiedostoja, joissa voi olla arkaluontoista tietoa. Toistaiseksi ei ole kuitenkaan havaittu, että hyökkääjä olisi korottanut oikeuksiin vaan hän on toiminut developer-käyttäjän oikeuksilla. Tarkistetaan seuraavaksi, mitkä ovat käyttäjän developer oikeudet:

sudo -l -U developer

Tämän komennon vastaus kertoo, että käyttäjällä developer ei ole sudo-oikeuksia ArcticInnovations-palvelimella.

Tarkistetaan vielä käyttäjän developer ryhmät:

id developer

uid=1002(developer) gid=1002(developer) groups=1002(developer),100(users)

Tästä nähdään, että käyttäjä developer kuuluu ensisijaisesti ryhmään developer ja myös toiseen ryhmään users. Developer on tavallinen käyttäjä, eikä hänellä ole root-oiekuksia.

Hyökkääjä on ilmeisesti kiinnostunut palvelimella olevasta tiedosta ja erityisesti arctic-data kansiosta. Tarkistetaan kansion oikeudet, jotta saadaan selville on hyökkääjä nähnyt arvokkaita tietoja ollessaan kirjautuneena käyttäjätunnuksella "developer":

sudo ls -ld opt/arctic-data

oikeudet näyttävät tältä: drwxr-x--- 6 root root. Tässä d tarkoittaa kansiota, sen jälkeen kirjaimet rwx ovat omistajan oikeudet eli luku-, kirjoitus- ja suoritusoikeus, seuraavak kolme merkkiä r-x ovat ryhmän oikeudet eli luku- ja suoritusoikeus ja kolme viimeistä merkkiä --- tarkoittavat, ettei muilla ole lainkaan oikeuksia. Root on tässä kansiossa omistaja ja root on kansiolle lisätty ryhmä. Tämä kertoo siis sen, että käyttäjällä developer ei ollut mitään oikeuksia kansioon, joten tavallisilla oikeuksilla hyökkääjä ei ole nähnyt mitään tietoja.

"JATKA"
