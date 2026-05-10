Nyt olet tutkinut hyökkäyksen etenemisen lisäksi järjestelmässä ilmenneitä ongelmia. Koska et halua, että samanlainen hyökkäys toistuu, päätät korjata havaittuja ongelmia.

Aiemmin palvelimta tutkiessasi havaitsit muun muassa brute force hyökkäyksen, onnistuneen SSH-kirjautumisen palvelimelle sekä mahdollisen pääsyn joihinkin tiedostoihin.

Aloitetaan SSH-kirjautumisen koventamisella. Avataan SSH-asetukset:

sudo nano /etc/ssh/sshd_config

tee tiedostoon seuraavat muutokset:

PermitRootLogin no
MaxAuthTries 3

Tallenna tiedosto painamalla ctrl + x sen jälkeen y ja hyväksy enter-näppäimellä. Käynnistetään SSH uudelleen:

sudo systemctl restart ssh

Näiden asetusten tarkoituksena on estää root-kirjautuminen ja rajoittaa kirjautumisyritykset kolmeen yritykseen.

Seuraavaksi parannetaan brute force -suojausta. Otetaan Fail2Ban-työkalu käyttöön. Se on työkalu, joka tunnistaa toistuvat epäonnistuneet tai epäilyttävät tapahtumat ja estää lähdeosoitteen pääsyn palveluun. Avataan asetustiedosto:

sudo nano /etc/fail2ban/jail.local

Muokataan tiedostoon kohtaan [sshd]:

enabled=true

Käynnistetään Fail2ban ja otetaan käyttöön:

sudo systemctl enable fail2ban
sudo systemctl start fail2ban

Tarkistetaan vielä työkalun tila:

sudo systemctl status fail2ban

Seuraavaksi tarkistetaan tiedostojen käyttöoikeudet ja rajoitetaan niitä, mikäli ne eivät ole kunnossa. Tehdään tämä nyt kansiolle arctic-data, jossa on erittäin arvokasta tietoa. Voit halutessasi muuttaa myös muiden tiedostojen ja kansioiden käyttöoikeuiksia:

sudo ls -ld /opt/arctic-data
sudo chmod 750 arctic-data

Nämä oikeudet varmistavat, että ainoastaan omistaja ja ryhmä pääsevät kansioon ja muut käyttäjät estetään.

Seuraavaksi pyritään vähentämään Apache-palvelimen tietovuotoa. Avataan ensin asetukset:

sudo nano /etc/apache2/conf-available/security.conf

Muutetaan tiedostoon:

ServerTokens Prod
ServerSignature Off

Käynnistetään apache uudelleen:

sudo systemctl restart apache2

Tämä piilottaa palvelimen versiotiedon ja vähentää hyökkääjille näkyvää tietoa.

Näiden tehtyjen toimenpiteiden lisäksi kaikki palvelut kannattaa päivittää uusimpaan versioon, sillä vanhat versiot voivat olla haavoittuvia. Muistathan huolehtia päivittämisestä säännllisesti, se ei ole vain kertaluontoinen asia.
Lisäksi organisaatiossa tulisi kiinnittää huomiota salasanoihin. Salasanojen tulisi olla vahvoja, jotta niiden murtaminen ei olisi liian yksinkertaista. Lisäksi monivaiheisen tunnistatumisen käyttöönotto lisää turvallisuutta merkittävästi.

Onneksi olkoon! olet nyt päässyt ohjatun kierroksen loppuun. Nohevana blue teamin jäsenenä olet tietysti koko ajan palvelinta tutkiessasi tehnyt muistiinpanoja ja työstänyt raporttia havainnoistassi ja tehdyistä toimenpiteistä. Nyt lähetätkin aikaansannoksesi esihenkilöllesi ja jäät odottamaan kiitoksia hyvin hoidetusta työstä. 


