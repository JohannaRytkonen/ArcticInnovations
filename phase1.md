Olet nyt saanut tunnukset ja kirjautunut sisään työkoneellesi. Esihenkilösi saapuu esittäytymään ja kertoo, että palvelimella on havaittu normaalista poikkeavaa liikennettä. Pääset siis saman tien tositoimiin. Sinulle annetaan tehtäväksi selvittää onko palvelimeen kohdistunut hyökkäys ja jos on ,mitä on tapahtunut?

Muistat, että palvelimet keräävät tietoja tiedostoihin, joita kutsutaan lokeiksi. Näistä lokeista voisi ehkä näkyä jotakin ja päästä kärryille siitä, mitä on tapahtunut. Päätät siis aloittaa tutkimalla lokitiedostoja.

Aloita selvittämällä mitä lokitiedostoja palvelimella on:

sudo ls /var/log/apache2/

Huomaat, että lokitiedostoja on useita. Hyökkäys ei välttämättä ole viimeisimmässä lokitiedostossa vaan se on voinut tapahtua jo aiemmin. Päätät aloitta tutkimisen vanhimmasta lokista, jolloin voit saada parhaan kuvan mahdollisen hyökkäyksen kehittymisestä.
