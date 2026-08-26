_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 22.8.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h1 Kybertappoketju. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

#### x) Lue/katso/kuuntele ja tiivistä.

[Herrasmieshakkerit (RSS)](https://herrasmieshakkerit.fi/)

Kuuntelin Mikko Hyppösen ja Tomi Tuomisen luotsaamasta __Herrasmieshakkerit__ -podcastista jakson _Suomesta maailmalle, vieraana Otto Ebeling__. Ebeling kertoi omasta taustastaan ja työhistoriastaan niin Metalla kuin pienemmissä yrityksissä. Erityisesti jaksosta jäi mieleen Ebelingin kertomat tilanteet "mahdottomista tilanteista", joita oli kuitenkin sattunut useaan otteeseen. Esimerkiksi epäilty hyökkäys osoittautuikin vain yksittäiseksi, pieneksi sähköviaksi. Ebelingin kertomukset myös kansainvälisestä urasta olivat mielenkiintoista kuunneltavaa.


[The Art of Hacking: 4.3 Surveying Essential Tools for Active Reconnaissance](https://learning.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_03/)

- Videolla esiteltiin erilaisia skannaustyökaluja. Näitä olivat Nmap, Masscan, EyeWitness & Udpprotoscanner.
- Jokaisella on omat vahvuutensa:
   - Nmap on yleisin ja monipuolinen
   - Masscan nopein vaihtoehto
   - Udprotoscanner on hyvä vaihtoehto UDP-porttien skannaukseen.
   - EyeWitness taas kerää yhteenvedon web-palveluista sen sijaan, että hakkerin täytyisi vierailla sadalla eri sivulla manuaalisesti.


**[KKO 2003:36](https://www.finlex.fi/fi/oikeuskaytanto/korkein-oikeus/ennakkopaatokset/2003/36)**

17-vuotias henkilö A teki porttiskannauksen Osuuspankin verkkoon. Huolimatta siitä, että skannaus ei onnistunut, Korkein oikeus katsoi järjestelmällisen skannauksen tietomurto yritykseksi. A tuomittiin lopulta tietomurron yrityksestä ja hänet määrättiin maksamaan mittavat korvaukset sekä Osuuspankille. 

[Intelligence-Driven Computer Network Defense
Informed by Analysis of Adversary Campaigns and
Intrusion Kill Chains](https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf)

Kill Chain on järjestelmällinen prosessi, jolla hyödytään vastustajaan hyökkäämisestä. Sitä kuvataan ketjuna, sillä yhden vaiheen epäonnistuminen voi rikkoa koko prosessin.

1. Reconnaissance
   - Kohteen tai kohteiden etsiminen, tunnistaminen ja valitseminen.
   - Teknologioiden, sosiaalisten yhteyksien, sähköpostilistojen kerääminen, jne.
3. Weaponization
   - Haittaohjelman liittäminen kohteelle toimitettavaan payloadiin. PDF- tai Office-tiedostot hyviä esimerkkejä.
4. Delivery
   - Payloadin toimittaminen kohteelle. Yleisiä tapoja ovat esimerkiksi sähköpostiliitteet tai USB-laitteet.
5. Exploitation
   - Kun hyökkäävä payload on onnistuneesti syötetty kohteelle, se voidaan aktivoida
6. Installation
   - Hyökkääjä asentaa takaoven, jonka avulla pääsy järjestelmään säilyy pitkäaikaisesti.
7. Command and Control (C2)
   - Hyökkääjä pääsee hallitsemaan yhteyttä kohdekoneeseen sen muodostaessa yhteys hyökkääjän ylläpitämään palvelimeen.
8. Actions on Objections
   - Hyökkääjä suorittaa tavoitteensa, oli se sitten tietojen keräämistä, varastamista tai häirintää.
  

________________________________________________________________________________________________________________________________________________________________________________________


### a) Asenna Kali virtuaalikoneeseen

VirtualBox minulta löytyi jo entuudestaan. Riitti, että latasin Kali Linux Installerin ja asensin uuden virtuaalikoneen. Tein normaalit määritykset ja testasin, että näppäimistö ja verkko toimivat niin kuin kuuluukin. 

<img width="383" height="124" alt="image" src="https://github.com/user-attachments/assets/47ae2312-d1e8-4e95-895d-34f13e6b59bb" />


________________________________________________________________________________________________________________________________________________________________________________________

### b) Irrota Kali-virtuaalikone verkosta.

Ennen kuin irrotin Kalia verkosta, varmistin, että verkko kuitenkin toimii. Pingasin Googlen DNSää 8.8.8.8. ja sain sieltä vastauksen. 


<img width="675" height="112" alt="image" src="https://github.com/user-attachments/assets/1913d625-a0b7-43ec-b251-3153bea28b23" />


Tämän jälkeen navigoin VirtualBoxin verkkoasetuksiin, josta kytkin verkon pois päältä. Boottasin Kalin uudestaan ja varmistin komennoilla, että kone ei saanut yhteyttä verkkoon. Komentorivin lisäksi yritin myös avata Firefox selaimen, mutta sekään ei ollut yhteydessä verkkoon.


<img width="1163" height="491" alt="image" src="https://github.com/user-attachments/assets/df3de18d-35f6-444a-947d-4542b7685a52" />


________________________________________________________________________________________________________________________________________________________________________________________

### c) Porttiskannaa 1000 tavallisinta tcp-porttia omasta koneestasi

Porttiskannaus tapahtui komennolla

    nmap -T4 -A localhost

- nmap on tiedustelu- ja porttiskannaustyökalu, joka lähettää paketteja ja analysoi vastauksia niihin.
- T4 on vipu skannauksen nopeudelle ja "agressiivisuudelle". Asteikko skannausvauhdille on T1-T5. 
- -A ottaa käyttöön edistyneitä ominaisuuksia. Esimerkiksi palvelu- ja verkkotunnistuksen ja käyttöjärjestelmän tunnistamisen. 

Skannaus tuotti kuvanmukaisen tulosteen: 


<img width="1277" height="332" alt="image" src="https://github.com/user-attachments/assets/9c644ad7-934c-4018-9b74-57ae8e7359ba" />


Odotetusti, kaikki 1000 yleistä TCP-porttia jotka Nmap skannasi olivat suljettuina eli mikään palvelu ei kuunnellut niissä. Network Distance: 0 oli myöskin odotettavissa, sillä liikenne ei kulkenut kuin paikallisesti. Nmap suoritti myös OS detection -toiminnon, jolla se pyrkii päättelemään käyttöjärjestelmää lähettämällä paketteja ja analysoimalla niiden vastauksia. Tässä tapauksessa se ei kuitenkaan onnistunut, sillä kaikki portit olivat suljettuina eikä yksilöitävää dataa saatu riittävästi.


_____________________________________________________________________________________________________________________________________________________________________________________

### d) Asenna kaksi vapaavalintaista demonia ja skannaa uudelleen. Analysoi ja selitä erot.

Kalista löytyi valmiiksi SSH & Apache2, joten päätin käyttää näitä kahta. Käynnistin demonit 

    sudo systemctl start ssh
    sudo systemctl start apache2
    
komennoilla ja ajoin porttiskannauksen uudelleen.


<img width="829" height="264" alt="image" src="https://github.com/user-attachments/assets/2e247eb2-2123-4dea-a1c7-af7fb0d33ecd" />


Tällä kertaa tulos oli erilainen. Porttiskannaus löysi SSH:n kuuntelemasta porttia 22 & Apachen puolestaan porttia 80. Porttinumeroiden lisäksi selvisi niiden tila (open), palvelu (ssh/http) ja versio. -A -vivulla saatiin selville myös palvelutunnistus, jolloin Nmapin oli mahdollista suorittaa HTTP-pyyntö palvelimelle ja palauttamaan HTTP-vastauksen otsikon (_Apache2 Debian Default Page: It works_)

Skannaukseen oli avattu ainoastaan edellämainitut kaksi demonia, joten Nmapin tuhannesta yleisestä TCP-portista 998 oli edelleen suljettuna.

________________________________________________________________________________________________________________________________________________________________________________________

### e) Ratkaise vapaavalintainen kone HackTheBoxista

HackTheBox ei ollut itselleni entuudestaan tuttu. Luennolla oli yhteisesti katsottu, kuinka VPN yhteys määritettiin HTB:n harjoituskoneeseen, joten se ei tuottanut suurempia ongelmia. Käynnistin HTB:n sivulla harjoituskoneen ja latasin itselleni OpenVPN:n konffitiedoston, jonka avulla pääsisin kiinni oikeaan IP-osoitteeseen. 

Päädyin ratkomaan HackTheBoxin Fawn-konetta. Harjoituksen tavoite oli selvittää tietoja FTP-kohdekoneelta muodostamalla siihen yhteys ja varastaa palvelimelta lippu. Osa tehtävistä vaati oikean komennon tietämisen ja osa sisälsi "käytännön harjoituksia", joissa tuli olla yhteydessä kohdekoneeseen komentorivillä.

Heti alkuun tuskailin huomattavan pitkään yhteyden muodostuksen kanssa. Sain yhdistettyä VPN yhteyden **sudo openvpn** -komennolla mutta tästä huolimatta pingaus ei onnistunut. Kokeilin kahta eri konetta (Meow & Fawn) mutta sama ongelma toistui. Lopulta pitkän troubleshootingin jälkeen tajusin kuitenkin, että HTB:ssa aloittelijoiden Starting Point ja tavalliset koneet toimivat eri .opvn konffitiedostojen avulla. Ladattuani oikean konffitiedoston, homma alkoi rullaamaan.

Kun vihdoin sain yhteyden toimimaan, kokeilin tässä vaiheessa jo tuttua ```nmap -T4 -A <IP-OSOITE>``` komentoa, jolla sain skannattua tietoa esiin: 


<img width="727" height="829" alt="image" src="https://github.com/user-attachments/assets/bc030766-7d98-459d-a4d8-844c9c3e09a7" />


Skannaus osoitti, että portissa 21 oli avoinna FTP palvelu. Lisäksi selvisi, että anonyymi kirjautuminen on sallittu ja haussa ollut lippu, _flag.txt_ löytyy palvelimelta. Pystyin seuraavaksi yhdistämään FTP:hen ja kirjautumaan anonymous -käyttäjänä. Kirjautumisessa kysettiin myös salasanaa mutta jättämällä sen vain tyhjäksi päästiin sisään. Sen jälkeen oli yksinkertaista vain listata (ls) sisältö ja ladata (get) itselleen haluttu flag.txt -tiedosto. 

```get``` -komennolla sain ladattua flag.txt tiedoston itselleni ja syöttämällä sen sisällön läpäisin harjoituksen. 


<img width="531" height="456" alt="image" src="https://github.com/user-attachments/assets/df78595b-69d0-4ffb-8280-e2cd7841f27c" />


________________________________________________________________________________________________________________________________________________________________________________________


### Mitä opin? 

- Ensiaskeleet Nmapin käyttöön. Tutustuin porttiskannaukseen ja opettelin analysoimaan sen tuloksia.
- Opin pelaamaan(?) HackTheBoxia ja sain ensimmäisen harjoituksen maaliin. Pääsin/jouduin myös harjoittamaan ongelmanratkaisua konffitiedostojen tiimoilta.
- Tunkeutumistestauksen vaiheittainen eteneminen ja sen askeleet tuli tutuksi ja käyvät järkeen. 
- Löysin Herrasmieshakkerit podcastin, menee kuuntelulistalle!

________________________________________________________________________________________________________________________________________________________________________________________


### Lähteet

Karvinen, T. Tunkeutumistestaus. Opintojakson kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#laksyt. Luettu 22.8.2026.

Hutchins, M. Cloppert, M, Amid R. Intelligence-Driven Computer Network Defense
Informed by Analysis of Adversary Campaigns and
Intrusion Kill Chains. Luettavissa: https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf. Luettu 22.8.2026.

 
Santos O, Taylor R, Sternstein J, McCoy C. The Art of Hacking (Video Collection). 2019. Luettavissa: https://www.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_00/. Luettu 22.8.2026.

KKO:2003:36. 2023. Luettavissa: https://www.finlex.fi/fi/oikeuskaytanto/korkein-oikeus/ennakkopaatokset/2003/36. Luettu 22.8.2026.

Kali Linux. https://www.kali.org/get-kali/#kali-installer-images. Luettu 22.8.2026.

House, N. Nmap Cheat Sheet 2026: All the Commands & Flags. 2026. Luettavissa: https://www.stationx.net/nmap-cheat-sheet/. Luettu 25.8.2026.

HackTheBox Labs. Fawn harjoituskone. Saatavilla: https://app.hackthebox.com/machines/Fawn?sort_by=created_at&sort_type=desc. 

