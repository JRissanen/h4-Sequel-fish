# h4-Sequel-fish

This is a repository for Penetration testing course 2023 Tunkeutumistestaus ict4tn027-3009 course exercise h4

---
Olen tehnyt kaikki harjoitukset omalla tietokoneellani. </br>

Koneen infot: </br>
Edition: Windows 10 Pro </br>
Version: 22H2 </br>
OS build: 19045.2728 </br>
Processor: Intel(R) Core(TM) i5-6500 CPU @ 3.20GHz   3.19 GHz </br>
Installed RAM: 16,0 GB </br>
System type: 64-bit operating system, x64-based processor </br>

Virtuaalikoneet ovat Oracle VM VirtualBoxissa, jonka versio on: Version 6.1.40. </br>

Virtuaalikoneiden infot: </br>

Metasploitable: </br>
Type: Linux </br>
Version: Debian (64-bit) </br>
Base Memory: 1024 MB </br>
Video Memory: 16 MB </br>
Processors: 1 CPU

Kali: </br>
Type: Linux </br>
Version: Debian (64-bit) </br>
Base Memory: 2024 MB </br>
Video Memory: 128 MB </br>
Processors: 2 CPU

Varmistan aina ennen tehtävien aloittamista, että molemmat virtuaalikoneet ovat yhteyksissä toisiinsa pingaamalla kali-koneelta metasploitable2-konetta: `ping 192.168.60.3`. </br>
Varmistan myös aina, ettei kumpikaan virtuaalikone saa verkkoyhteyttä pingaamalla google.com sekä googlen ensisijaista dns-serveriä 8.8.8.8: `ping google.com` ja `ping 8.8.8.8`.

Jos haluat enemmän tietoa minun tunkeutumistestausympäristöstä, niin voit lukea artikkelini: </br>
https://github.com/JRissanen/h1-OmaLabra

---

## x) Lue ja tiivistä (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva.)

### Cialdinin suostuttelun periaatteet (haluamastasi lähteestä)

Valitsin artikkelin [THE SCIENCE OF PERSUASION, Seven Principles of Persuasion](https://www.influenceatwork.com/7-principles-of-persuasion/), koska sen on julkaissut itse Robert Cialdini yhdessä [INFLUENCE AT WORK (IAW®)](https://www.influenceatwork.com/about-iaw/) tiiminsä kanssa. Ajattelin, että suoraan Cialdinilta itseltään saisi parhaiten aiheesta irti ja että se olisi hänen kirjoittamanaan parhaiten selitetty.

Cialdinin tutkimusten mukaan on olemassa __kuusi__ pääperiaatetta, jotka vaikuttavat ihmisten päätöstentekoon.

1. Vastavuoroisuus (Reciprocity) </br>
   - Ihmiset tuntevat velvollisuuden antaa takaisin saman arvoinen palvelus, lahja tai kehu, jonka ovat itse ensin saaneet.

2. Vähäinen saatavuus (Scarcity)
   - Ihmiset haluvavat enemmän niitä asioita, joita on vähemmän saatavilla.

3. Auktoriteetti (Authority)
   - Ihmiset seuraavat uskottavien/tunnettujen, "paljon tietävien" ammattilaisten ohjeistuksia sekä auktoriteetin omaavia henkilöitä kuten poliiseja.

4. Jatkuvuus/pysyvyys (Consistency)
   - Ihmiset haluvat olla johdonmukaisia aiempien päätöstensä kanssa, eli he ovat todennäköisempiä jatkamaan aiempia sitoumuksiaan.

5. Tykkääminen (Liking)
   - Ihmiset ovat taipuvaisia myöntymään asioihin, kun kyseessä on henkilö, josta he pitävät (samaistuminen, kohteliaisuus, yhteinen tavoite).

6. Sosiaalinen todiste (Social Proof)
   - Ihmiset (etenkin kun ovat epävarmoja) usein ottavat muista ihmisistä mallia tehdessään omaa päätöstänsä.

### PortSwigger: [SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks)

* `UNION` -avainsana mahdollistaa yhden tai useamman ylimääräisen `SELECT` -kyselyn suorittamisen ja tulosten liittämisen alkuperäiseen kyselyyn.
* Esim. `SELECT a, b FROM table1 UNION SELECT c, d FROM table2`
   * Tämä UNION -hyökkäys palauttaisi vastauksena yhden tulostaulukon kahdella sarakkeella, jotka sisältävät arvoja `table1` sarakkeista `a` ja `b` sekä `table2` sarakkeista `c` ja `d`.

* Kaksi vaatimusta, jotta UNION -hyökkäys onnistuu:
   * Yksittäisten kyselyiden on palautettava sama määrä sarakkeita.
   * Jokaisen sarakkeen tietotyypin (datatype) on oltava yhteensopiva yksittäisten kyselyiden välillä.

* Kaksi tehokasta tapaa saada sarakkeiden määrä selville:
   * Ensimmäinen tapa on syöttää `' ORDER BY 1--` ja sen jälkeen `' ORDER BY 2--` jne. kunnes järjestelmä palauttaa virheilmoituksen, tai ei palauta enää mitään.
   * Toinen tapa on syöttää `' UNION SELECT NULL--` ja sen jälkeen `' UNION SELECT NULL,NULL--` jne. kunnes järjestelmä palauttaa virheilmoituksen, tai ei palauta enää mitään.

### PortSwigger: [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)(Cheatsheetistä voit poimia muutaman itselle relevantin kohdan.)

* SQL -kyselyiden loppuosien pois kommentointi:
   * Parametri: `--` toimii lähes kaikissa tietokannoissa.
    * Vaihtoehtoisia tapoja voi olla: `#` ja `-- <välilyönti>`.

* Kaikkien tietokannan taulukoiden ja niiden sarakkeiden listaaminen:
   * Onnistuu jokaisessa tietokannassa komennolla: `SELECT * FROM` alkuosalla.

---

## a) Kalassa. Laadi keihäskalasteluviesti (spear phising) kuvitteelliselle yritykselle. Tavoitteena on saada kohde avaamaan liitteenä oleva Excel-dokumentti ja laittamaan makrot päälle. Käytä ainakin kahta Cialdinin periaatetta. Selitä, miten näitä periaatteita on sovellettu viestiisi. Keksi skennario ja kohde itse. Käytä työssä vain kuvitteellisia yrityksiä ja henkilöitä. Viestiä ei lähetetä mihinkään, se tulee vain raporttiin. Liitteenä olevaa Excel-dokumenttia ei tarvitse tehdä, mutta jos siinä on jotain nokkelaa, voit kuvailla, mitä se olisi.





























## Lähteet
https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h4-sequel-fish </br>
https://www.influenceatwork.com/7-principles-of-persuasion/ </br>
https://www.influenceatwork.com/about-iaw/ </br>
https://portswigger.net/web-security/sql-injection/union-attacks </br>












