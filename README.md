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
>
>![48245783026_a31896b87d_b(cropped)](https://user-images.githubusercontent.com/116954333/235949163-c7f2cde3-ab6b-433b-a251-bc92d1c75438.jpg) ![fishing-887523_1280](https://user-images.githubusercontent.com/116954333/235949232-8791fbd6-aa3b-42bf-961b-f9a533953e20.jpg) ![48245872477_f61c356e91_b(cropped)](https://user-images.githubusercontent.com/116954333/235949271-b819a045-c9bb-400a-a13d-935e0ba3b0c0.jpg)
>
>
>
>Katso koko tarjouskatalogimme [tästä linkistä](https://github.com/JRissanen/h4-Sequel-fish/blob/main/README.md)
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

Aloitin avaamalla labran ja valitsemalla etusivulta "Corporate gifts" välilehden, jolloin huomasin hakukentän tekstissä osion: </br> `/filter?category=Corporate+gifts` ja ajattelin, että siitä on hyvä lähteä kokeilemaan haavoittuvuuden löytymistä.

![Screenshot_2](https://user-images.githubusercontent.com/116954333/235913070-cf68a054-40d9-4d61-9365-c914e8780971.png)

Ensimmäisenä lähdin kokeilemaan tiivistämässäni PortsSwiggerin [SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks) artikkelissa opetettua `' ORDER BY 1--` metodia. </br>
Syötin siis hakukenttään: `category='+ORDER+BY+1--`.

![Screenshot_3](https://user-images.githubusercontent.com/116954333/235915113-57e0917a-b75e-4638-876d-29c1a14297c9.png)

Tästä ei seurannut mitään ihmeellistä tulostusta, mutta artikkelissa sanottiin, että pitäisi syöttää samaa syötettä, mutta korottaa vain viimeistä numeroa aina yhdellä, kunnes verkkosivu antaa virhetulostuksen, tai ei tulosta enää mitään, joten kokeilin sitä suraavaksi. </br>
Kun pääsin neljään asti, verkkosivu antoi "Internal Server Errorin", joten päättelin, että tietokannassa on kolme saraketta.

![Screenshot_4](https://user-images.githubusercontent.com/116954333/235916548-8d70d1ea-2c28-467e-850f-1e49fc276518.png)

Seuraavaksi koitin syöttää hakukenttään: `'+UNION+SELECT+NULL,NULL,NULL--`, jotta saisin labran ohjeistuksen mukaan tietokannan palauttamaan yhden ylimääräisen rivin, joka sisältää `null` arvoja. Ja labra meni tällä syötteellä läpi.

![Screenshot_5](https://user-images.githubusercontent.com/116954333/235918623-62cee88d-7162-4573-9736-621035aee16a.png)

---

## d) SQL injection UNION attack, finding a column containing text.

Aloitin lukemalla labran ohjeistuksen.

![Screenshot_1](https://user-images.githubusercontent.com/116954333/235924376-ba6cdd64-a362-4b29-a507-fc43e9673f68.png)

Labra vaikuttaisi olevan saman tyylinen kuin edellinnenkin. </br>
Tällä kertaa pitää vain saada labran antama satunnainen arvo näkyviin tulosteessa, eikä ylimääräistä "null" -arvoa. </br>
Avasin labran ja satunnainen merkkijono, jonka labra halusi minun noutavan tietokannasta oli "W1ekNs". </br>
Ajattelin aloittaa samalla tavalla haavoittuvuuden etsinnän, eli kohdasta "Corporate gifts" ja verkkosivun hakukentästä.

![Screenshot_2](https://user-images.githubusercontent.com/116954333/235928629-ac54b9d2-9b5f-429b-836a-50764aa1f52c.png)

Ensiksi ajattelin vain kokeilla syötettä: `'+UNION+SELECT+'W1ekNs'--`. </br>
Se antoi kuitenkin vain "Internal Server Errorin". </br>
Kokeilin taas `'+ORDER+BY+1--` jne. ja tulos oli sama kuin viime tehtävässä, eli neljän kohdalla tuli virhettä, joten pitäisi olla kolme saraketta. </br>
Kokeilin myös `'+UNION+SELECT+NULL--` ja lisäsin aina yhden `NULL` lisää, eli sama logiikka kuin `' ORDER BY 1`. </br>
Syötteellä `'+UNION+SELECT+NULL,NULL,NULL--` verkkosivu ei tulostanut enää "Internal Server Erroria", vaan normaalin tulosteen, joten ajattelin, että kolmannessa sarakkeessa olisi silloin jotain erilaista, kuin kahdessa muussa. (En ole varma oliko havainto oleellinen).

Luin samaa [SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks) artikkelia uudestaan ja huomasin kohdan:

![Screenshot_3](https://user-images.githubusercontent.com/116954333/235939462-5ba6ec73-4603-46f1-a8bd-5edbdc6cf568.png)

Eli UNION -hyökkäyksiin voi syöttää tietoa etsiessä merkkijonoja `'a'` syntaksilla eri sarakkeiden kohdalle. </br>
Aiempien kokeilujeni perusteella tiedän, että tietokannassa on kolme saraketta, joten lähdin koittamaan merkkijonoa "W1ekNs" eri sarakkeisiin artikkelin ohjeistaman logiikan mukaan. </br>
Syötin siis aluksi: `'+UNION+SELECT+NULL,NULL,'W1ekNs'--`. Se ei mennyt läpi. </br>
Seuraavaksi koitin: `'+UNION+SELECT+NULL,'W1ekNs',NULL--`, ja sillä syötteellä labra ratkesi. </br>
Eli toinen sarake oli se, joka oli haavoittuvainen.

![Screenshot_4](https://user-images.githubusercontent.com/116954333/235941841-b41b58cc-c7dd-4284-a2a1-28da0932e683.png)

---

## e) SQL injection UNION attack, retrieving data from other tables.

Aloitin lukemalla labran ohjeistuksen.

![Screenshot_1](https://user-images.githubusercontent.com/116954333/236437372-9c17ffc5-e067-48e1-be36-844f9464d8ab.png)

Tässä labrassa on tietokanta, joka sisältää taulukon "users", jossa on sarakkeet "username" ja "password". </br>
Tarkoitus on siis hyödyntää näitä tietoja samaan haavoittuvuuteen, eli tuotteiden kategoarialajittelijaan (product category filter). </br>
Ajattelin ensiksi koittaa hyödyntää aiemmassa [SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks) artikkelissa opetettua logiikkaa: </br>
`SELECT a, b FROM table1 UNION SELECT c, d FROM table2`. 

Ensin menin labran etusivulta kohtaan "Tech gifts", jonka jälkeen lisäsin sivun hakukohtaan: </br>
`'+UNION+SELECT+'administrator',+''+FROM+users`. </br>
Ajatukseni oli, että hakisin suoraan "administrator" käyttäjän "users" taulukosta ja jättäisin salasanan parametrin tyhjäksi. Tämä ei kuitenkaan onnistunut.

![Screenshot_2](https://user-images.githubusercontent.com/116954333/236445787-86211c52-5e28-443a-a029-8bd05bec72e8.png)

Labran ohjestuksessa taidettiinkin mainita, että ensin pitäisi saada kaikki käyttäjätiedot näkyviin, joten seuraava ideani  oli kokeilla syötettä: `'+UNION+SELECT+*+FROM+users`. </br>
Ajatukseni oli, että saisin `*` parametrin avulla kaikki käyttäjät näkyviin, mutta sekään ei onnistunut.

![Screenshot_3](https://user-images.githubusercontent.com/116954333/236446811-9aecb9af-b913-4750-9fc3-7ff0d7a95447.png)

Tein näitä tehtäviä seuraavana päivänä edellisistä tehtävistä, joten huomasin, että olin unahtunut käyttää `--` parametriä loppuosan pois kommentoimiseen, joten koitin syöttää saman syötteen, kuin äsken, mutta lisäämällä `--` tällä kertaa loppuun. eli: `'+UNION+SELECT+*+FROM+users--` ja sain seuraavan tulostuksen:

![Screenshot_4](https://user-images.githubusercontent.com/116954333/236448713-9186984e-1ba4-4391-a238-cdb831dba1b6.png)

Sieltä näkyi "carlos", "wiener" ja "administrator" käyttäjien salasanat. </br>
Kopioin "administrator" käyttäjän salasanan ja menin "My acoount" sisäänkirjautumis näkymään ja syöttämällä "administrator" käyttäjän tiedot, pääsin kirjautumaan sisään ja labra meni läpi.

![Screenshot_5](https://user-images.githubusercontent.com/116954333/236449448-179408ab-435a-4bd8-a977-4476bdf76166.png)

---

## f) SQL injection UNION attack, retrieving multiple values in a single column.

Aloitin lukemalla labran ohjeistuksen.

![Screenshot_1](https://user-images.githubusercontent.com/116954333/236620996-6a90276f-7f62-411d-80f5-272a24839e41.png)

Tässä labrassa on sama ohjeistus kuin viime labrassakin. </br>
Menin etusivulta kohtaan "Corporate gifts" ja ajattelin kokeilla samalla syötteellä, jolla edellinen labra ratkesi, eli: </br>
`'+UNION+SELECT+*+FROM+users--`.

![Screenshot_2](https://user-images.githubusercontent.com/116954333/236621274-c9ac87b7-15f0-44c8-be70-519e0a5f0f6f.png)

Ei ratkennut sentään samalla syötteellä, joten jotain täytyy tehdä eritavalla tällä kertaa. </br>
Luin taas samaa [SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks) artikkelia ja huomasin kohdan: 

![Screenshot_3](https://user-images.githubusercontent.com/116954333/236621452-7e85f999-403a-4696-83f4-6a175326e670.png)

Eli käyttämälla "double-pipe" `||` parametria ja `'~'` parametria Oracle tietokannoissa on mahdollista yhdistää käyttäjänimen ja salansanan arvot. Ajattelin kokeilla tätä seuraavaksi syötteellä: `'+UNION+SELECT+username+||+'~'+||+password+FROM+users--`.

![Screenshot_4](https://user-images.githubusercontent.com/116954333/236621822-5a413e0d-caf4-4ae8-a23e-e375ce0aec59.png)

Ei toiminut tämäkään... </br>
Labran vinkki osiossa neuvottiin katsomaan [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet) hyödylisiä payloadeja. </br>
Kokeilin seuraavaksi muokata vähän syötettä esimerkiksi:

`'+UNION+SELECT+username+||+~+||+password+FROM+users--` </br>
`'+UNION+SELECT+username||+~+||password+FROM+users--` </br>
`'+UNION+SELECT+username+||~||+password+FROM+users--` </br>
`'+UNION+SELECT+username||~||password+FROM+users--` </br>
`'+UNION+SELECT+username||'~'||password+FROM+users--` </br>
`'+UNION+SELECT+username+||'~'||+password+FROM+users--`

Mikään näistä ei kuitenkaan toiminut. </br>
En keksinyt enää enempää kokeiltavaa, joten katsoin labran ratkaisun.

![Screenshot_5](https://user-images.githubusercontent.com/116954333/236623108-e24b424d-e0f4-4216-ab77-978c33178ec9.png)

Eli en ollut osannut sijoittaa `NULL` arvoa `SELECT` parametrin jälkeen, joten siksi yksikään syötteistäni ei mennyt läpi. </br>
En osaa sanoa, miksi `NULL` arvo on pakollinen, eikä sitä ratkaisussakaan selitetä... </br>
Tutkin asiaa ja löysin samasta [SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks) artikkelista kohdan:

![Screenshot_8](https://user-images.githubusercontent.com/116954333/236625198-e5598011-d629-47ae-94da-4320599887d2.png)

Eli ilmeisesti jokaisen `UNION` hyökkäyksen tietotyypin on vastattava `SELECT` kyselyllä lähetettävää tietokannasta haettavan sarakkeen tietotyyppiä. `NULL` arvo vastaa kaikkia yleisimpiä tietotyypejä, joten sen käyttäminen maksimoi hyökkäyksen onnistumisen.

Syötin sitten ratkaisu syötteen sivun hakukenttään ja sain käyttäjänimet ja salasanat näkyviin. </br>
Kirjauduin "administrator" käyttäjänä sisään kohdasta "My account" ja labra meni läpi.

![Screenshot_6](https://user-images.githubusercontent.com/116954333/236623449-b6dd301d-61cd-43fc-8e45-13cc698f724d.png)

![Screenshot_7](https://user-images.githubusercontent.com/116954333/236623503-2d03183e-9574-4f67-b419-581bc232207a.png)

---

## g) SQL injection attack, querying the database type and version on Oracle.

Aloitin lukemalla labran ohjeistuksen.

![Screenshot_1](https://user-images.githubusercontent.com/116954333/236623880-11758aaf-97e8-4373-9723-9f367c4828c6.png)

Eli tässä labrassa pitää saada tietokannan versio selville. </br>
Heti labran avattua, sivulla näkyy lisäohjeistusta:

![Screenshot_2](https://user-images.githubusercontent.com/116954333/236624082-badb90dc-683b-43ee-a3ac-b7d67c068d91.png)

Lisäohjeistuksesta (ja aiemmista labroista) päätellen kyseessä on ainakin Oracle tietokanta, joten katsomalla [SQL injection cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet), voin ainakin käyttää Oracleen toimivia payloadeja. </br>
Sieltä löysinkin seuraavan syötteen:

![Screenshot_3](https://user-images.githubusercontent.com/116954333/236624314-c8ddb9ea-13be-4ea3-b7e7-9ca530808129.png)

Menin siis taas etusivulta "Corporate gifts" ja kokeilin ensimmäiseksi syöttää hakukenttään: </br>
`'+UNION+SELECT+banner+FROM+v$version`. Ei toiminut. </br>
Kokeilin myös toista vaihtoehtoa: `'+UNION+SELECT+version+FROM+v$instance`. Sekään ei toiminut... </br>
Kokeilin molempia myös `--` parametrin kanssa, mutta sillä ei ollut vaikutusta.






























## Lähteet
https://terokarvinen.com/2023/tunkeutumistestaus-2023-kevat/#h4-sequel-fish </br>
https://www.influenceatwork.com/7-principles-of-persuasion/ </br>
https://www.influenceatwork.com/about-iaw/ </br>
https://portswigger.net/web-security/sql-injection/union-attacks </br>
https://portswigger.net/web-security/all-labs </br>
https://portswigger.net/web-security/sql-injection </br>














