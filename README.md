# LinuxPalvelimet-h4-Tukki
    Kirjoittanut: Jesse Reunamo
    Kurssi:       Linux-palvelimet
    Linkki:       https://terokarvinen.com/2023/linux-palvelimet-2023-alkukevat/

## x)
Luin Ars Technica artikkelin Linuxin komentorivistä, miten käyttää eri teksinhallinta komentoja.

- Artikkeli listaa putkittamisen eri muotoja: `; > >> < |`
- `;` kahden eri komennon välissä suorittaa komennot peräkkäin.
- `>` siirtää komennon tulosteen uuteen tiedostoon. Nimi `>` komennon perään.
- `>>` siirtää komennon tulosteen olemassa olevan tiedoston loppuun. Nimi `>>` komennon perään.
- `<` käyttää merkin jälkeen olevaa tiedostoa komennon syötteenä
- `|` merkillä voidaan putkittaa komennon tuloste seuraavalle komennolle
- Artikkelissa esitellään komennot: grep, sed ja awk.
- grep komennolla voit poimia rivejä tekstistä haluamallasi hakutermillä.
- Komento `sudo netstat -anp | grep apache` Näyttää kaikki avoimena olevat portit, mutta tulokset on rajoitettu vain riveihin joissa on apache. (Netstat vaatii erillisen paketin toimiakseen)
- sed komento korvaa tietyn tekstin haluamallasi tekstillä. 
- Komento `sed 's/koira/kissa/'` vaihtaa tekstin ensimmäisen koiran kissaksi.
- awk komento, joka tarjoaa AWK-scripti kielen tekstinhallintaa varten. Huomattavasti monimutkaisempi, kuin grep ja sed. (Awk vaatii erillisen paketin toimiakseen)
- Artikkelissa oli vain yksi kommentti, joka iloitsi komentorivityökalujen esittelyä nuoremmille sukupolville. 

## a)
Tässä osiossa analysoin logitiedostojen tietoja.

### /var/log/syslog
Yleisloki, mikäli tiedolla ei ole omaa logia.

    sudo less syslog | grep Finished
    
Putkitin lisäksi grep komennon, jolla voin hieman rajoittaa tulosteen kokoa. 



Kuvassa näkyy alimpana:

    Jan 29 15:19:52 Kissa systemd[1]: Finished Cleanup of Temporary Directories.
    
Vasemmalta lukien lokissa on kuukausi, päivä, kellonaika, järjestelmän nimi, järjestelmänhallinta järjestelmä ja lokin kuvaus. Loki tieto on tässä tapauksessa varsin yksiselitteinen ja se kertoo väliaikaisten tietojen tyhjentämisestä.

### /var/log/auth.log
Kirjautumistiedot ja sudo komennon käyttö

### /var/log/apache2/access.log
Apachen alaisuudessa olevat netin surffailu tiedot

### /var/log/apache2/error.log
Apachen virhetilanteet

## b)



## Lähteet

    https://arstechnica.com/gadgets/2021/08/linux-bsd-command-line-101-using-awk-sed-and-grep-in-the-terminal/
    https://news.ycombinator.com/item?id=28368958

