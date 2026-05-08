Avataan seuraava loki:

sudo less /var/log/apache2/access.log.1

Huomataan, että tässä lokissa ei ole kuin yksi rivi, joka ei oikeastaan kerro mitään kiinnostavaa. Siirrytään siis seuraavaan lokiin:

sudo less /var/log/apache2/access.log

Myöskään tässä lokissa ei ole enää mitään tietoja. 

Hyvä nyt olet selvittänyt, mitä toimia palvelimella on tehty. Huomasit aiemmin, että kansiossa on myös error-lokeja. Tutkitaan näitä kuitenkin hieman myöhemmin, sillä ensin haluat selvittää, onko hyökkääjä päässyt sisään palvelimelle ja onko hän mahdollisesti saanut arvokkaita tietoja itselleen.

Siirrytään katomaan loki-kansion sisältöä:

sudo ls /var/log

Huomataan, että kansiossa on tiedostot auth.log ja auth.log.1. Näistä voisi selvitä jotakin kohteelle kirjatumisesta.

Lokeja voi lähteä selaamaan rivi kerrallaan, mutta koska näissä lokeissa on hyvin hyvin paljon dataa en suosittele tätä tapaa. Koska lokianalyysi ei ole lukemista vaan se on etsimistä, lähdetään suodattamaan tai kohdistamaan dataa. Dataa voisi suodaattaa esimerkiksi hakemalla tietoja vain tietyltä aikaväliltä. Access-lokien avulla on mahdollista haarukoida aikaikkuna, joilloin epäilyttävää toimintaa on havaittu. Lähdetään tässä tapauksessa liikkeelle kuitenkin vielä kohdennetummin. Haetaan tapahtumia, joissa on jokin meitä kiinnostava sana, kuten "accepted", "failed password", "sudo" tai "session opened".

Lähdetään etsimään ensimmäisenä sanalla "accepted". Tällä voitaisiin löytää onnistuneita sisäänkirjautumisia:

sudo grep "Accepted" var/log/auth.log.1

Tällä haulla löydetään kuusi innistunutta kirjatumista. Näistä neljä on käyttäjän developer kirjautumisia ja lisäksi yksi onnistunut kirjautuminen käyttäjällä admin ja käyttäjällä analyst. Nämä eivät vielä kerro paljoakaan. Tehdä sama haku kuitenkin vielä toiselle lokille:

sudo grep "Accepted" var/log/auth.log

Seuraavaksi voit kokeilla hakea tietoja myös muilla sanoilla. Löydätkö mitään mielenkiintoista?

Joillekin hakusanoille tulee vain muutamia tuloksia ja joillekin useita. Hakuja voi myös yhdistää paremman tarkkuden saamiseksi. Kuitenkin hakemalla IP-osoitteella löytyi kiinnostavaa tietoa:

Accepted password for developer from 192.168.30.4

Tämä tarkoittaa sitä, että hyökkääjän IP-osoiteesta on hyväksytty käyttäjän developer salasana. Tämä vahvistaa sen, etttä hyökkääjä on päässyt sisään järjestelmään.

Muta mitä hyökkääjä sitten on tehnyt järjestelmässä?

"JATKA"









