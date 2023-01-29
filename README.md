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

![log1](https://user-images.githubusercontent.com/112503770/215336218-cadee3c5-3fb8-4133-b89d-ad29aac387ec.png)

Kuvassa näkyy alimpana:

    Jan 29 15:19:52 Kissa systemd[1]: Finished Cleanup of Temporary Directories.
    
Vasemmalta lukien lokissa on kuukausi, päivä, kellonaika, järjestelmän nimi, järjestelmänhallinta järjestelmä ja lokin kuvaus. Loki tieto on tässä tapauksessa varsin yksiselitteinen ja se kertoo väliaikaisten tietojen tyhjentämisestä.

### /var/log/auth.log
Kirjautumistiedot ja sudo komennon käyttö.

    sudo cat auth.log

![log2](https://user-images.githubusercontent.com/112503770/215336230-108c66b5-9e74-4aed-838f-fad55f799757.png)

Kuvassa näkyy alimpana:

    Jan 29 16:23:54 Kissa sudo: pam_unix(sudo:session): session opened for user root(uid=0) by (uid=1000)
    
Vasemmalta lukien lokissa on kuukausi, päivä, kellonaika, järjestelmän nimi, sudo ja lokin kuvaus. Lokissa kerrottaan, että linuxsin autentikointijärjestelmä pam_unix on avannut sudo session root käyttäjälle käyttäjä id:llä 0, ohjelmalla jonka id on 1000. Oman näkemykseni mukaan kyseessä on loki siitä, kun suorittaa sudo etuliitteellä komentoja.

### /var/log/apache2/access.log
Apachen alaisuudessa olevat netin surffailu tiedot

    sudo cat apache2/access.log
    
![log3](https://user-images.githubusercontent.com/112503770/215336249-2709e451-d3df-46ec-93e9-9d02ecb927a4.png)


Kuvassa näkyy alimpana:

    127.0.02 - - [27/Jan/2023: 14:57:37 +0200] "GET /oon/JEPI HTTP/1.1" 404 488 "-" "Mozilla/5.0 (X11; Linux x86_64; rv: 102.0) Gecko/20100101 Firefox/102.0
    
Vasemmalla on localhost ip:osoite eli oman tietokoneen sisäisesti on yritetty ottaa yhteyttä. Seuraavana päivämäärä, aika ja aikavyöhyke tiedot. Tietoa http pyynnöstä: GET pyyntö, osoite, käytetty http versio, saatu http status koodi ja palautettujen tavujen määrä. Viimeisenä komennossa ilmenee Firefox user agent string reference, jossa eritellään selaimen: Mozilla yhteensopivuus, ohjelmiston alusta, gecko-selain moottorin versionumero, gecko numerosarja(ei ihan selvää mitä varten), käytetyn selaimen nimi ja versionumero. 

### /var/log/apache2/error.log
Apachen virhetilanteet

    sudo cat apache2/error.log
    
![log4](https://user-images.githubusercontent.com/112503770/215336259-7a24333d-2ec0-4402-b5ce-df322215d77d.png)


Kuvassa näkyy alimpana:

    [Sun Jan 29 16.39:50.334936 2023] [core:notice] [pid 764: tid 140143178874176] AH00094: Command line: '/usr/sbin/apache2'

Vasemmalta näkee tarkan päivämäärä, kellonaika ja vuoden. Seuraavana on Apachen ilmoituksen taso, tässä tapauksessa notice, joka on normaalia toimintaa. PID vastaa prosessin id:tä ja tid on säie id (eng: thread id). En osaa tarkalleen sanoa mitä AH00094 tekee, mutta kokonaisuudessaan Command line: '/usr/sbin/apache2' on säikeen aloittamista varten oleva komento usr/sbin/apache2 sijaintina.

## b)
Aiheutetaan lokiin tietoa käyttämällä apache2 palvelinta paikallisesti. Mikäli sinulla ei ole Apache2 palvelinta voit asentaa ja käynnistää sen komennoilla.

    sudo apt-get update
    sudo apt install apache2
    sudo systemctl start apache2
    
Palvelimen olessa onnistuneesti käynnistä voimme vierailla sivulla localhost ja localhost/etsitaa/404. Vierailemalla näillä sivuilla voimme aiheuttaa loki tapahtumia /var/log/apache2/access.log tiedostoon. Voimme tarkastella tiedostoa tarkemmin komennolla.

    sudo tail -2 apache2/access.log

tail komennolla saamme vain lokin loppuosan ja -2 rajoittaa tuloksen kahteen viimeiseen riviin.

![logitehty](https://user-images.githubusercontent.com/112503770/215338718-e0ff46b0-7e81-4d02-8dcf-e13e171f59a6.png)



    127.0.02 - - [29/Jan/2023: 17:29:31 +0200] "GET /etsitaa/404 HTTP/1.1" 404 488 "-" "Mozilla/5.0 (X11; Linux x86_64; rv: 102.0) Gecko/20100101 Firefox/102.0
    127.0.02 - - [29/Jan/2023: 17:29:32 +0200] "GET / HTTP/1.1" 200 3379 "-" "Mozilla/5.0 (X11; Linux x86_64; rv: 102.0) Gecko/20100101 Firefox/102.0

Kuten jo aikaisemmin katsoimme login alussa on IP osoite, joka tässä tapauksessa vastaa paikallista sijaintia siis localhost. Seuraavana tulee pvm, aika ja aikavyöhyke kenttä. Http pyynnön metodi näkyy GET osioss ja seuraavana on haettu endpoint/url. Tämän jälkeen näkyy metodin status koodi, mikä näissä lokeissa on selkeästi erilainen, koska toinen pyyntö on epäonnistunut 404 ja toinen on 200 ok. Statuksen jälkeen on palautettujen tavujen määrä. Lokin lopussa on Firefox user agent string reference, jossa eritellään selaimen: Mozilla yhteensopivuus, ohjelmiston alusta, gecko-selain moottorin versionumero, gecko numerosarja, käytetyn selaimen nimi ja versionumero. 


## Lähteet

    https://arstechnica.com/gadgets/2021/08/linux-bsd-command-line-101-using-awk-sed-and-grep-in-the-terminal/
    https://news.ycombinator.com/item?id=28368958
    https://medium.com/@gggauravgandhi/uid-user-identifier-and-gid-group-identifier-in-linux-121ea68bf510
    https://phoenixnap.com/kb/127-0-0-1-localhost
    https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent/Firefox
    https://httpd.apache.org/docs/2.4/mod/core.html#loglevel
    https://serverfault.com/questions/607873/apache-is-ok-but-what-is-this-in-error-log-mpm-preforknotice
    https://www.dataset.com/blog/apache-error-log-detail/

