_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)

_Päivämäärä: 17.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h5 Elokuu2026! Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

## h5 Elokuu2026!
#### _Nyt levitään! Opit murtamaan salasanoja._
<br>
<br>

### x) Lue/katso ja tiivistä
  [Karvinen 2022: Cracking Passwords with Hashcat](https://terokarvinen.com/2022/cracking-passwords-with-hashcat/)

  - Salasanoja ei tallennetta sellaisenaan syötettyinä merkkijonoina vaan _hasheina_. Hashaus toimii vain yhteen suuntaan, joten niitä ei voi muuntaa takaisin salasanaksi.
  - Hasheja voidaan kuitenkin verrata sanakirjassa oleviin sanoihin työkalujen avulla. Yksi niistä on HashCat.
  - HashCat rajaamaan käytetyn hashaus-tyypin todennäköisiin vaihtoehtoihin ```hashid -m``` -komennolla
  - Itse kräkkääminen tapahtuu ``hashcat -m <int n> '<hash>' <sanakirja> -o solved`` -komennolla. Tämä vertaa syötettyä hashia valitun sanakirjan sisältöön ja tallentaa salasanan uuteen tiedostoon jos sellainen löytyy.
  - HashCatin vahvuus on sen nopeus. Kräkkäystä voi vauhdittaa ajamalla sitä host-koneella hyödyntäen näytönohjaimen tarjoamaa vauhtia 
  
  [Karvinen 2023: Crack File Password With John](https://terokarvinen.com/2023/crack-file-password-with-john/)

  - Toinen murtamistyökalu on John the Ripper. Sen Jumbo-version voi ladata GitHubista ja kääntää lähdekoodista komennoin ``configure`` ja ``make``.
  - Artikkelissa murretaan ZIP-tiedosto, joka on suojattu salasanalla.
    - Salasanatiedot muutetaan ensin hash-muotoon _zip2john_ -työkalulla.
    - Verrataan eri salasanojen hash-arvoja kohde hashiin.
  - John tukee ZIPin lisäksi myös esimerkiksi 7z, PDF, Office, Bitlocker -formaatteja.

<br>
<br>

| HashCat | John the Ripper |
|---|--|
| hashien murtaminen | hashien sekä tiedostojen salasanojen murtaminen |
| todella nopea GPU:lla | hitaampi kuin HashCat |
| vahvuus: nopea hash-cracking | vahvuus: monipuolisempi formaattituki |
| hashcat -m 1400 hash.txt wordlist.txt | john --wordlist=wordlist.txt hash.txt |

<br>
<br>

________________________________________________________________________________________________________________________________________________________________________________________

### a) Asenna Hashcat ja testaa sen toiminta murtamalla esimerkkisalasana.

Oletetaan, että salasana on "SALASANA123". Merkkijonon hash256-tiiviste saadaan luotua komennolla ``echo -n 'SALASANA123' | sha256sum``. 

 - ``-n`` estää ``echo`` -komentoa luomasta rivinvaihtoa merkkijonon loppuun.
 - Rivinvaihto vaikuttaisi hash-arvoon (echo -n 'salasana vs echo 'salasana' tuottavat eri tiivisteet)


<img width="683" height="77" alt="sha256_sum" src="https://github.com/user-attachments/assets/c5c59ed0-b493-408b-b34a-4837be9946c7" />
<br>
<br>

Nyt meillä oli hash, mutta koska hashia ei voi muuttaa takaisin salasanaksi sitä täytyi alkaa vertaamaan muihin hasheihin. Manuaalilla tämä olisi työllistävää ja hidasta, joten avuksi otettiin HashCat -työkalu. 

<br>

**Asennus:** 

    sudo apt-get update
    sudo apt-get -y install hashcat wget

Eri salakirjoitusmuotoja on pitkä lista mutta HashID:n avulla voidaan tunnistaa mahdollisia hash-formaatteja ja tätä kautta saada niille HashCatin mode-numeroita. Se ei kuitenkaan valitse käyttäjälle automaattisesti yhtä oikeaa, joten on hyödyllistä tuntea yleisesti käyetyt salaustavat. 

Jotta HashCat pystyy vertailemaan sille syötettyä hashia, se tarvitsee myös listan sanoista, joihin hashia verrataan. Loin tehtävää varten uuden "testikirjasto.txt" tiedoston. Sen sisältä löytyi merkkijonoja, joista vain yhden _"SALASANA123"_ tulisi olla oikea salasana.


<img width="170" height="266" alt="TESTIKIRJASTO" src="https://github.com/user-attachments/assets/9733622f-d15f-48da-b312-2c8ca68997f6" />
<br>
<br>

**Käyttö:**

    hashid -m <HASH>   // HashCat pyrkii tunnistamaan käytetyn hash-salaustyyypin
    hashcat -m <valittu tyyppi (SHA256 (1400), MD5 (0))> <hash> <sanakirjasto> < -o solved (kirjoittaa osuman erilliseen tiedostoon>


Ajoin siis ``hashcat -m 1400 '366c5c22e389a0e6a562d6ded5e21cdc166ffd571c58b7455f189145e95feae6' testikirjasto.txt -o solved`` -komennon, jolloin HashCat asettu komennon hashin vertailuun. Se muodosti jokaisesta sanakirjan sanasta hashin ja vertasi niitä syötteeseen. Jos osuma löytyisi, se tallentaisi sen uuteen _solved_ -tiedostoon työhakemistossa. Nyt sanakirja oli äärimmäisen lyhyt eikä vertailuja ei tarvinnut suorittaa montaa, joten suoritus oli todella verkkaisa. 


<img width="820" height="627" alt="HASHCAT_LOPPUTULOS" src="https://github.com/user-attachments/assets/7203d7d7-4aa3-4a00-b338-7918509229a5" />
<br>
<br>

Jos ``-o solved`` -vipua ei käytetä, kräkätty salasana (_apina_) näkyy suoraan HashCatin tulosteessa eikä sitä tallenneta muualle.

<br>
<img width="810" height="205" alt="ILMAN TALLENNUSTA" src="https://github.com/user-attachments/assets/82b4f211-d340-4800-a329-86706d8e3318" />

________________________________________________________________________________________________________________________________________________________________________________________


### c) Asenna John the Ripper ja testaa sen toiminta murtamalla jonkin esimerkkitiedoston salasana.

Seurasin asennusohjeita [Karvisen artikkelista](https://terokarvinen.com/2023/crack-file-password-with-john/): latasin OpenWallin repositorion Githubista, konfiguroin ympäristön ja käänsin ohjelman. 

    $ sudo apt-get update
    $ sudo apt-get -y install micro bash-completion git build-essential libssl-dev zlib1g zlib1g-dev zlib-gst libbz2-1.0 libbz2-dev atool zip wget
    $ git clone --depth=1 https://github.com/openwall/john.git
    $ cd john/src/	
    $ ./configure
    $ make -s clean && make -sj4

<img width="1161" height="197" alt="JOHN_installed" src="https://github.com/user-attachments/assets/f6fb490b-6f43-4091-a039-bf9809ee1806" />
<br>
<br>

Lähdin kokeilemaan Johnin toimintaa lataamalla Karvisen [Download tero.zip](https://terokarvinen.com/2023/crack-file-password-with-john/tero.zip) kansion. ZIP-tiedosto oli salasanasuojattu, joten yksinkertainen ``unzip tero.zip`` ei toiminut. 


<img width="461" height="91" alt="PROTECTED_ZIP" src="https://github.com/user-attachments/assets/a07eb2a0-66c8-48fa-8568-a6ef685503bb" />
<br>
<br>

Muunsin ZIPin Johnin hash-muotoon komennolla ``~/john/run/zip2john tero.zip > tero.zip.hash``. Tuloste kertoi, että SECRET.md oli PKZIP-suojattu mutta zip2john pystyi keräämään siitä tarvittavat tiedot. Seuravaksi vuorossa oli varsinainen cracking -vaihe: ajamalla ``~/john/run/john tero.zip.hash`` John kräkkää salasanan. 
 - ``~/john/run/john --show tero.zip.hash`` -komennolla saadaan vielä siivottu lopputulos.


<img width="1051" height="215" alt="JOHN_CRACKED" src="https://github.com/user-attachments/assets/92aa7321-ee96-445e-9602-2887ddbd1437" />
<br>

<img width="844" height="123" alt="--SHOW" src="https://github.com/user-attachments/assets/060bcf0e-c215-4c14-8fa9-93580507da6d" />
<br>

________________________________________________________________________________________________________________________________________________________________________________________

### e) Tiedosto. Tee itse tai etsi verkosta jokin salakirjoitettu tiedosto, jonka saat auki. Murra sen salaus. (Jokin muu formaatti kuin aiemmissa alakohdissa kokeilemasi).

Latasin Kaliin LibreOfficen, jolla pystyi luomaan .PDF-tiedostoja. Loin salasanalla suojatun PDF:n ja lähdin murtamaan sitä Johnilla. 

#### 1. PDF:n luominen

Asensin LibreOfficen komennolla ``sudo apt-get -y install libreoffice``. Loin uuden dokumentin, jonne kirjoitin sisältöä ja suojasin tiedoston avaamisen salasanalla. 

<img width="418" height="81" alt="SALAINEN DOKUMENTTI PDFINFO" src="https://github.com/user-attachments/assets/da86c827-142a-4c9e-b840-c1c19eef6174" />

#### 2. Hashin hankkiminen

Aloitin komennolla ``~/john/run/pdf2john "Salainen dokumentti.pdf" > salainen.hash`` mutta sain virheilmoituksen: _zsh: no such file or directory: /home/henri/john/run/pdf2john_
<br>

PDF-työkalun pitäisi olla Johnin vakiokalustua, joten tarkastin ensin mikä PDF-extractor minulta Johnista löytyy. Komennolla ``ls ~/john/run/*pdf*`` löytyi _pdf2john.pl_ & _pdf2john.py_, joten aiemman komennon pääte oli vain puutteellinen. Oikealla päätteellä ajamalla saatiin luotua PDF:stä hash-tiedosto työhakemistoon.

``~/john/run/pdf2john.pl "Salainen dokumentti.pdf" > salainen.hash`` 


<img width="721" height="229" alt="OIKEAN PDF LÖYTÄMINEN" src="https://github.com/user-attachments/assets/0447c792-4c1b-4acb-aa62-2d2a4b9c9d2d" />
<br>

``cat salainen.hash`` -komennolla saatiin varmistettua, että tiedoston sisällä on PDF-hash. 

#### 3. Salasanan murtaminen Johnilla

Ajoin ``~/john/run/john salainen.hash`` -komennon, jolloin John lähti vertaamaan äsken hankittua hashia omaan sisäiseen sanakirjastoonsa:

<img width="1054" height="385" alt="JOHN CRACKED IT" src="https://github.com/user-attachments/assets/7a783928-405b-4024-b00f-eee1c69bed3a" />

John onnistui murtamaan salasanan: **_"topsecret"_**
<br>
<br>

#### 4. PDF-tiedostoon murtautuminen

Salasana voitiin vielä todenta toimivaksi avaamalla PDF-tiedosto ja syöttämällä Johnin löytämä salasana:

<img width="993" height="331" alt="image" src="https://github.com/user-attachments/assets/49839fe1-7f49-4c6b-9ccf-e89c4110a2e8" />
<img width="744" height="381" alt="AVATTU PDF" src="https://github.com/user-attachments/assets/a6787f16-fb01-4a97-aa19-d3b505bfc51d" />

________________________________________________________________________________________________________________________________________________________________________________________

### f) Linux-käyttäjän salasanan murtaminen

Loin uuden käyttäjän "_hashtest_" ja asetin tälle _rockyou.txt_ -sanakirjasta löytyvän salasanan (_grimy_). Komennolla ``id hashtest`` sain varmistettua, että uuden käyttäjän luominen onnistui

 - uid=1001
   - 1001 on käyttäjän tunniste
- gid=1001
   - käyttäjän ensisijainen ryhmä
- groups=1001
   - mihin ryhmiin käyttäjä kuuluu
 


<img width="610" height="192" alt="USER ADDED" src="https://github.com/user-attachments/assets/70c13e19-55ee-4810-9441-ccd9936c0795" />
<br>

Lähdin murtamaan salasanaa Johnilla mutta törmäsin seuraavaan virheilmoitukseen: 

<img width="602" height="91" alt="NOT LOADED" src="https://github.com/user-attachments/assets/fea14fda-a46e-4026-8d56-e4eddbf58616" />
<br>

Nopealla Googletuksella selvisi, että kyseessä oli todennäköisesti _yescypt_ -formaatin puuttuminen Johnista. Tarkastin Johnin formaatit komennolla ``~/john/run/john --list=formats`` eikä sieltä löytynyt yescryptiä. Samoin _/etc/shadow_ -tiedostosta löytyvä tiiviste alkoi $y$, joka oli yescryptin tunniste. [Baeldung](https://www.baeldung.com/linux/shadow-passwords)

Lähdin siis tutkimaan, miten yescrypt saisi muunnettua esimerkiksi SHA512-muotoon, joka toimi Johnilla toimi varmasti. 

Komennolla ``mkpasswd --method=sha-512 'hakkeri123'`` sain luotua SHA512-muotoisen tiivisteen. Asetin tämän äsken luodun käyttäjän salasanaksi ja tarkastin /etc/shadow -tiedostosta, että salasanatiiviste alkoi $6$, sillä tämä oli SHA-512 formaatti.

<img width="1055" height="91" alt="image" src="https://github.com/user-attachments/assets/c9bee650-40a4-4bba-828b-03998e3f90d7" />
<br>

Tämän jälkeen oli helppo poimia tiiviste omaan tiedostoonsa ja käyttää Johnia normaalisti:

``~/john/run/john --wordlist=testikirjasto.txt hashtest.hash``

John vertasi salasanatiivistettä lyhyen sanakirjaston sisältöön ja löysi sieltä oikean salasanan.

<img width="951" height="250" alt="image" src="https://github.com/user-attachments/assets/96c97300-6440-4ea4-b30b-72f9101104b2" />
<br>

________________________________________________________________________________________________________________________________________________________________________________________

### g) Sanakirja. Oman sanakirjan teko parantaa onnistumismahdollisuuksia. Demonstroi, kuinka teet oman sanakirjan hashcat:n tai john:iin.

Oman sanakirjan käyttäminen vahvuus on hyökkääjän ympäristöön soveltuva osavuus. Sen sijaan, että yritettäisiin valtaavaa määrää satunnaisia salasanoja, voidaan yritykset rajata hyökkääjän jo aiemmin kerätyn tiedon perusteella kohdeympäristöön/-uhriin soveltuviksi. 

#### Uuden sanakirjan luominen

``micro sanakirja.txt`` tai ``echo <sana> <sanakirja.txt>``

#### Käyttö HashCatissa:

``hashcat -m 0 <hash> <sanakirja.txt>``

#### Käyttö Johnissa:

``~/john/run/john --wordlist=<sanakirjasto.txt> <hash>``

________________________________________________________________________________________________________________________________________________________________________________________

### h) Hash rules. Näytä esimerkki HashCatin sääntöjen käytöstä (rules).


Sääntöjen avulla voidaan helposti muokata perussanoista eri versioita. Sanalista voi sisältää esimerkiksi _salasana_ ja _password_ sanat ja sääntöjä käyttämällä kaikkien sanojen perään lisätään säännönmukainen lisä (123, !, jne). 


Otin MD5 hashin _salasana123_ sanasta.

<img width="689" height="77" alt="MD5 HASH salasana123" src="https://github.com/user-attachments/assets/a8fe650e-705c-412b-a7b8-abd706150dcc" />
<br>

Muokkasin aiemmin luomaani sanakirjaa _testikirjasto.txt_ niin, että _salasana123_ ei löytynyt suoraan sieltä. 

<img width="167" height="295" alt="SANAKIRJASTO" src="https://github.com/user-attachments/assets/50881849-da70-44c5-967f-a3a158d9a8e3" />

Loin uuden säännön, joka lisäsi jokaisen sanan perään _123_.
 - Tiedoston luonti komennolla: ```micro 123.rule```
    - $1 -> lisää 1 loppuun
    - $2 -> lisää 2 loppuun
    - $3 -> lisää 3 loppuun

<img width="627" height="47" alt="MICRO RULE" src="https://github.com/user-attachments/assets/a3a9c8a0-5c2e-4cb7-86d8-2154c7caa4dd" />

<br>

#### Sääntöjen ajaminen

HashCatin ajaminen toimi tavalla kuin aiemmin mutta nyt valittu sääntö lisätään mukaan ``-r`` -vivulla: ``hashcat -m 0 441a51e3169e51e31ebca3292b2c89d9 testikirjasto.txt -r 123.rule``

HashCat luki siis sanakirjan sanat, lisäsi jokaisen päätteeksi numerot 123, otti näistä hash-arvot ja vertasi niitä syötettyyn hashiin. Tällöin sanakirjasta löytyvään _salasana_ -sanaan lisättiin perään 123, jolloin sen hash-arvo täsmäsi.

<img width="826" height="450" alt="CRACKED WITH RULES" src="https://github.com/user-attachments/assets/3a608eaf-b1df-4ee5-98f4-ff8630d1570f" />
<br>
________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 17.9.2026.


Karvinen, T. Cracking Passwords with HashCat. 2022. Luettavissa: https://terokarvinen.com/2022/cracking-passwords-with-hashcat/. Luettu 17.9.2026.

Karvinen, T. Crack File Password with John. 2023. Luettavissa: https://terokarvinen.com/2023/crack-file-password-with-john/. Luettu 17.9.2026.

/etc/shadow and Creating yescrypt, MD5, SHA-256, and SHA-512 Password Hashes. Gerganov, H. 2024. Luettavissa: https://www.baeldung.com/linux/shadow-passwords. Luettu 17.9.2026.

HashCat manuaali.

John The Ripper manuaali.
