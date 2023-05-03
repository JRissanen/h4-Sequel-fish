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

### PortSwigger: [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet) (Cheatsheetistä voit poimia muutaman itselle relevantin kohdan.)

* SQL -kyselyiden loppuosien pois kommentointi:
   * Parametri: `--` toimii lähes kaikissa tietokannoissa.
    * Vaihtoehtoisia tapoja voi olla: `#` ja `-- <välilyönti>`.

* Kaikkien tietokannan taulukoiden ja niiden sarakkeiden listaaminen:
   * Onnistuu jokaisessa tietokannassa komennolla: `SELECT * FROM` alkuosalla.

---

## a) Kalassa. Laadi keihäskalasteluviesti (spear phising) kuvitteelliselle yritykselle. Tavoitteena on saada kohde avaamaan liitteenä oleva Excel-dokumentti ja laittamaan makrot päälle. Käytä ainakin kahta Cialdinin periaatetta. Selitä, miten näitä periaatteita on sovellettu viestiisi. Keksi skennario ja kohde itse. Käytä työssä vain kuvitteellisia yrityksiä ja henkilöitä. Viestiä ei lähetetä mihinkään, se tulee vain raporttiin. Liitteenä olevaa Excel-dokumenttia ei tarvitse tehdä, mutta jos siinä on jotain nokkelaa, voit kuvailla, mitä se olisi.

Skenaario voisi olla vaikka kuvitteellisen yrityksen "Tieto & Turva Oy" osakkeiden arvon romauttaminen, jotta sen kilpailevien yritysten osakkeiden arvo puolestaan nousisi. Itse tottakai sijoittaisin ensin rahaa johnkin kilpailevaan yritykseen ja sen jälkeen vasta lähettäisin kalasteluviestin. Kun kalastelu onnistuisi, ja sana tapahtumasta leviäisi, niin siitä seuraisi tietoturva-alan yritykselle iso kolahdus imagoon ja osakkeisiin.

Oletetaan, että Tieto & Turva Oy:n sivuilla on lueteltu yrityksen työntekijät. Perehtyisin kyseisiin työntekijöihin heidän sosiaalisten mediakanavien kautta ja ottaisin selvää, että kuka olisi todennäköisin heistä klikkaamaan linkkiä sähköpostissa ja minkä takia.

Tieto & Turva Oy:lla on töissä Simo Möttönen, jonka lempi harrastuksiin kuuluu kalastaminen ja hänen viimeisimmässään Instagram päivityksessä Simo valitti kuinka vanhalla virvelillä ei saa enää kaloja yhtä hyvin kuin 20 vuotta sitten.

Naamioisin kalastelusähköpostin näyttämään mahdollisimman paljon joltakin tunnetulta kalastustarvikkeita myyvältä yritykseltä, jolta olettaisin Simon ennenkin ostaneen jotain. Näin ollen hyödyntäisin Cialdinin periaatteista tykkäämis- sekä auktoriteettiperiaatetta.

Sähköposti näyttäisi jotakuinkin seuraavalta:

> Lähettäjä: kala.veikot@kala&veikot.com </br>
> Vastaanottaja: simo.mottonen@tieto&turva.com
>
>
>
>Terve Simo! </br>
>Olemme keränneet tähän uutiskirjeeseen alennustuotteita, jotka saattaisivat kiinnostaa sinua aiempien ostoksiesi perusteella:
>
>
>(kuvia virveleistä ja syöteistä yms kalastustarvikkeista) </br>
>Katso koko tarjouskatalogimme [tästä linkistä](https://github.com/JRissanen/h4-Sequel-fish/edit/main/README.md)
>
>
>
>Ystävällisin terveisin, </br>
>Kalaveikot Oy

Näin saattaisin saada Simon klikkaamaan naamioimaani haitallista linkkiä.

---

## b) [SQL injection vulnerability allowing login bypass](https://portswigger.net/web-security/all-labs).

Aloitin lukemalla ohjeet, joista näin että labran ratkaistakseen pitää päästä kirjautumaan sisään "administrator" käyttäjänä haavoittuvaisen "login" funktion kautta. 

![Screenshot_1](https://user-images.githubusercontent.com/116954333/235458831-fa607bb7-4ccf-417f-a176-7f9e4010329f.png)

Kerratessani aiempaa PortSwiggerin artikkelia [SQL -injektioista](https://portswigger.net/web-security/sql-injection) huomasin siellä ratkaisun tähän labraan puolivahingossa ja ajattelin sitten koittaa sitä kun kerran sen sieltä löysin.

![Screenshot_2](https://user-images.githubusercontent.com/116954333/235494310-bd335808-f4b0-4734-9325-3628412aebed.png)

Menin siis labran verkkokaupan etusivulta kohtaan "My account" ja siellä syötin käyttäjänimeksi `administrator'--` ja salasanaksi `admin`, vaikka salasanaksi olisi voinut kirjoittaa ihan mitä tahansa, koska `--` parametri kommentoi sen pois. Labra meni läpi.

![Screenshot_3](https://user-images.githubusercontent.com/116954333/235499918-99ab5826-d358-4d0c-8960-ed4b277ad030.png)

![Screenshot_4](https://user-images.githubusercontent.com/116954333/235499951-e03a1c52-e866-4de9-bacc-49cac5d91eec.png)

---

## c) SQL injection UNION attack, determining the number of columns returned by the query.

Aloitin lukemalla labran ohjeistuksen.

![Screenshot_1](https://user-images.githubusercontent.com/116954333/235911034-8101d720-ef8e-4096-a38f-9b09a7a76af1.png)

Labran ratkaisuun vaaditaan siis UNION -hyökkäyksen käyttämistä, jotta saadaan selville sarakkeiden määrä. </br>
Ohjeistuksessa myös mainitaan, että sivun haavoittuvuus on tuotteiden kategoarialajittelijassa (product category filter).

Aloitin avaamalla labran ja valitsemalla etusivulta "Corporate gifts" välilehden, jolloin huomasin hakukentän tekstissä `/filter?category=Corporate+gifts` osion, ja ajattelin, että siitä on hyvä lähteä kokeilemaan haavoittuvuuden löytymistä.

![Screenshot_2](https://user-images.githubusercontent.com/116954333/235913070-cf68a054-40d9-4d61-9365-c914e8780971.png)

Ensimmäisenä lähdin kokeilemaan tiivistämässäni PortsSwiggerin [SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks) artikkelissa opetettua `' ORDER BY 1--` metodia. </br>
Syötin siis hakukenttään: `category='+ORDER+BY+1--`.

![Screenshot_3](https://user-images.githubusercontent.com/116954333/235915113-57e0917a-b75e-4638-876d-29c1a14297c9.png)

Tästä ei seurannut mitään ihmeellistä tulostusta, mutta artikkelissa sanottiin, että pitäisi syöttää samaa syötettä, mutta korottaa vain viimeistä numeroa aina yhdellä, kunnes verkkosivu antaa virhetulostuksen, tai ei tulosta enää mitääm, joten kokeilin sitä suraavaksi. </br>
Kun pääsin neljään asti, verkkosivu antoi "Internal Server Errorin", joten päättelin, että tietokannassa on kolme saraketta.

![Screenshot_4](https://user-images.githubusercontent.com/116954333/235916548-8d70d1ea-2c28-467e-850f-1e49fc276518.png)

Seuraavaksi koitin syöttää hakukenttään: `'+UNION+SELECT+NULL,NULL,NULL--`, jotta saisin labran ohjeistuksen mukaan tietokannan palauttamaan yhden ylimääräisen rivin, joka sisältää `null` arvoja. Ja labra meni tällä syötteellä läpi.

![Screenshot_5](https://user-images.githubusercontent.com/116954333/235918623-62cee88d-7162-4573-9736-621035aee16a.png)

---

## d) SQL injection UNION attack, finding a column containing text.
































## Lähteet
https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h4-sequel-fish </br>
https://www.influenceatwork.com/7-principles-of-persuasion/ </br>
https://www.influenceatwork.com/about-iaw/ </br>
https://portswigger.net/web-security/sql-injection/union-attacks </br>
https://portswigger.net/web-security/all-labs </br>
https://portswigger.net/web-security/sql-injection </br>














