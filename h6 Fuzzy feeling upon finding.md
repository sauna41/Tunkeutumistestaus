_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)

_Päivämäärä: 17.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h6 Fuzzy feeling upon finding. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

### x) Tiivistä

[Hoikkala 2026: Fuzzing with Fuff](https://terokarvinen.com/tunkeutumistestaus/hoikkala-2026-fuzzing-with-ffuf.pdf)

- ffuf on web-fuzzeri, jolla lähetetään HTTP-pyyntöjä ja havaitaan poikkeavuuksia
  - status koodit
  - vastauskoko
  - request-response aika
- ffuf pystytään kohdentamaan mihin tahansa osaan HTTP-pyyntöä
  - URL, header, body, jne
- Hyödyntää sanalistoja syöttämällä jokaisen sanalistan sanan kerrallaan haluttuun kohtaan
  - voidaan käyttää valmiita listoja tai luoda oma
- ffuffiin voidaan määrittää erilaisia parametreja, joiden avulla tuloksia suodatetaan
- uusin versio toimii CRTF-tokenien kanssa
  - preflight/postflight: ffuf tekee määritetyn esikyselyn ja käyttää joka pyynnöllä tuoretta tokenia
<br>

ffufista löytyy ominaisuuksia vaikka kuinka. Käyttö oli tuntui kuitenkin alusta asti suht suoraviivaiselta, sillä koko työkalu on rakennettu mahdollisimman yksinkertaiseksi: "_avoid magic - stay idiomatic and understandable_. Erilaisia flageja on paljon mutta cheat sheet auttaa tässä.


________________________________________________________________________________________________________________________________________________________________________________________


### Vaultline https://ffuf.io.fi/play

#### a) Kirjoita omin sanoin
  ##### Scope. Mikä on kohde?

  - Kohteena on ffuf.io/play sivuston harjoitusympäristö.

  ##### Rules of engagement. Mitä sille saa tehdä, eli mitä tai millaisia menetelmiä saa käyttää?

  - Testauksessa käytetään tehtäviin tarkoitettuja fuzzaus menetelmiä, kuten HTTP-pyyntöjen lähettämistä, hakemistojen ja resurssien enumerointia sekä palvelimen saatujen vastausten suorittamista. 
    
  ##### Mihin oikeutesi tehdä tietoturvatestausta tähän kohteeseen perustuu?

  - Kyseessä on tarkoituksellisesti tietoturvan ja fuzzauksen harjoitteluun tarkoitettu ympäristö. Testauksen rajaaminen tapahtuu vain harjoitustehtäviin eikä mihinkään ulkopuolisiin järjestelmiin suoriteta testausta ilman erillistä lupaa. 
    
  ##### Riskit ja mitigointi. Tuo palvelin on Internetissä. Tunnista lyhyesti riskit ja niiden mitigointi ennen käytännön harjoittelua.
  
  - Koska kyseessä on internet-palvelin, sen fuzzaaminen voi aiheuttaa ylimääräistä liikennettä ja kuormitusta, mikä voi johtaa palvelun suorituskyvyn heikentymiseen. Myös väärin määritetty kohde tai käyttö voi johtaa siihen, että fuzzauspyyntöjä lähetetään väärään ympäristöön.
  - Riskejä mitigoidaan kohdentamalla testaus tarkasti vain halutttuun ympäristöön ja käyttämällä vain sivuston ohjeiden mukaisia listoja ja komentoja.  
________________________________________________________________________________________________________________________________________________________________________________________


#### b) Asenna ffuf versio, joka tukee aivan uutta preflight-ominaisuutta.

Ffuffin 2.3.0 version sai ladattua [ffuffin GitHub-repositoriosta](https://github.com/ffuf/ffuf). 

<img width="509" height="78" alt="FFUF 2.3.0" src="https://github.com/user-attachments/assets/9e3bbab4-7b8f-4f23-bca9-d4321dd786ef" />
<br>



Asetin vielä tämän ffuf-version järjestelmänlaajuisesti käyttöön:

    sudo install -m 755 ffuf /usr/local/bin/ffuf
    
________________________________________________________________________________________________________________________________________________________________________________________


#### c1) Content discovery
_Find the paths that exist but are not linked from anywhere._


Tavoitteena oli tutkia ffuf-harjoitusympäristön HTTP-palvelinta ja löytää palvelimelta olemassa olevia resursseja joihin ei sivulla ole linkitystä. 

Käytin content discovery -fuzzausta, jossa ffuf kokeilee valmiiksi annetun sanalistan sanoja URL-osoitteen eri polkuina. Harjoitusympäristöön oli annettu sanalista, jonka sai käyttöönsä suoraan sivustolta: ``curl -O https://ffuf.io.fi/wordlists/content.txt``. 

Lähdin ffufaamaan sivustoa ensin ilman rajaavia parametreja komennolla ``ffuf -w content.txt -u https://ffuf.io.fi/FUZZ``. 


Ensimmäisellä ajolla ffuf löysi 2000 vastausta. Silmämääräisesti näytti siltä, että kaikki olivat mallia:

- status: 200
- words: 135
- lines: 32
 
Samankaltaisia tuloksia oli liikaa, jotta niistä olisi ollut mitään järkeä lähteä analysoimaan mitään. Manuaalisesti 2000 tuloksen tutkiminen olisi vienyt myös tuhottomasti aikaa, joten lähdin suodattamaan tuloksia.

Automaattinen kalibrointi saatiin käyttöön lisäämällä loppuun vipu ``-ac``. Tämä pyrkii tunnistamaan normaalin wildcard-vastauksen ja suodattamaan sen kaltaiset vastaukset pois.

    ffuf -w content.txt -u https://ffuf.io.fi/FUZZ -ac

<img width="971" height="316" alt="image" src="https://github.com/user-attachments/assets/5282905c-a9ae-4792-8774-4d5f6c4c9ed2" />
<br>

Automaattisen kalibroinnin lisäksi tuloksia voi rajata myös yksittäisten flagien avulla. Samaan tulokseen päästäisiin esimerkiksi käyttämällä esimerkiksi


``-mc``

- match codes: näyttää vain halutut HTTP-statuskoodit 

``-fw``

- filter words: suodattaa pois vastaukset, joissa on tietty määrä sanoja (esim -fw 135)

``fs``

-filter size: suodattaa pois vastaukset, joilla on tietty koko (esim -fs 1119)

<br>


##### Analysointia

``status 200`` tarkoittaa, että palvelin palautti onnistuneen vastauksen. Esimerkiksi _admin, login_ ja _docs_ löytyivät.

``status 301`` tarkoittaa uudelleenohjausta. _backup, files, .git_ ja _api_ palauttivat tämän. Palvelin siis tunnisti pyynnön ja ohjasi pyynnön toiseen osoitteeseen. 

``.env`` ja ``.git`` mahdollisesti viittaavat sovelluksen konfiguraatioon tai Git-versionhallintaan. Navigoimalla sivustoa tavallisena käyttäjä ilman fuzzausta nämä ovat resursseja, joita tavallinen käyttäjä ei todennäköisesti löydä. Tutkimalla niitä tarkemmin olisi mahdollista, että esimerkiksi versiohistoriasta löytyisi tunnuksia tai muuta, joka auttaisi tunkeutumisessa. 

<br>

Tuloksissa oli myös esimerkiksi _login, pricing__ ja _product_ vaikka ne olivat selkeästi näkyvillä ja navigoitavissa sivustolla. Tehtävän tarkoituksena oli löytyy resurssit, joihin ei ole linkitystä, joten vielä oli suodatettavaa jäljellä. Selvitin seuraavaksi, mitä resursseja /play-sivu linkittää ja vertasin niitä ffufin tuloksiin. 

Komennolla ``curl -s https://ffuf.io.fi/play | grep -oE 'href="[^"]+"'`` haettiin /play-sivun linkitetyt resurssit.

    > curl -s <URL>   // hakee sivun HTML-sisällön
    > grep -oE     // etsii HTML:stä href-linkit ja tulostaa vain löydökset
    > ="[^"]+"'    // regular expression (regex). Käytännössä palauttaa yhedn tai useamman merkin, joka ei ole lainausmerkki. 


<img width="923" height="179" alt="image" src="https://github.com/user-attachments/assets/b7f93eef-b2ba-413e-b425-7242c89d91ac" />
<br>
<br>

Näitä tuloksia vertaamalla aiempaan ffuf -tulosteeseen, saatiin selville, että _product, pricing, docs_ ja _login_ resurssit löytyivät sivulta. Lopputuloksena tehtävänannon kysymykseen jäljelle jäävät siis kaikki muut ffufin löytämät tulokset, sillä niihin ei löydy sivustolta linkitystä.



________________________________________________________________________________________________________________________________________________________________________________________
     
#### c2) The interesting non-200
_Two planted paths do not answer 200. One of them a default run will not even consider._

Tehtävänä oli löytää kaksi polkua, jotka eivät vastaa 200-statuksella. Looginen ensimmäinen askel oli suodattaa pois 200-statukset komennolla ``ffuf -w content.txt -u https://ffuf.io.fi/FUZZ -fc 200``


<img width="870" height="153" alt="image" src="https://github.com/user-attachments/assets/4ab70c00-ac46-4706-a257-6cdea2e7e2a9" />
<br>

Kaikki löydökset _server-statusta_ lukuunottamatta olivat 301-statuksella. Tämä tarkoitti, että ne olivat uudelleenohjaavia eli kaksi planted pathia, jotka eivät anna 200-vastausta olivat todennäköisesti näiden joukossa. Lähdin tutkimaan tarkemmin niiden sisältöjä. 




________________________________________________________________________________________________________________________________________________________________________________________

#### c3) Recursion
_The wordlist holds names, not paths, so C1 found you 13 things and none of them nested. Descending finds more._
<br>

Rekursion avulla ffuf jatkaa syvemmälle löytyneiden hakemistojen sisälle. C1-tehtävässä löydettiin 13 tulosta, mutta niiden alta löytyy vielä lisää tutkittavaa. Käytin komentoa

``ffuf -w content.txt -u https://ffuf.io.fi/FUZZ -mc 200 -fw 135 -recursion -recursion-depth 3`` 

jonka avulla ffuf löysi samat tulokset kuin C1-tehtävssä mutta tällä kertaa fuzzaus jatkui myös löydettyjen hakemistojen sisälle (``-recursion``). Myös rekursiosyyvyden pystyi määrittämään omalla vivullaan (``-recursion-depth3``).

<br>
<img width="832" height="577" alt="REKURSIO" src="https://github.com/user-attachments/assets/fc975346-36c4-4ac8-a454-0df568fe0213" />
<br>

Tuloksista nähtiin, että ffuf löysi samoja resursseja (_docs, product, pricing, jne_) kuin aiemmin, mutta niiden sisältä myös uusia löydöksiä, kuten _db.sql.bak_ ja _v2_. 

________________________________________________________________________________________________________________________________________________________________________________________

#### c4) Virtual hosts
_Three hostnames under ffuf.io.fi serve different content from this same address. Find all three._
<br>

Tehtävänä oli löytää kolme resurssia. Lähdin tällä kertaa fuzzaamaan headereita: ``ffuf -w content.txt -u https://ffuf.io.fi/ -H "Host: FUZZ.ffuf.io.fi" -ac``

Ensimmäinen löydös oli _admin_

<img width="967" height="566" alt="image" src="https://github.com/user-attachments/assets/5d005333-cd38-4d93-9f0f-6d5a8fc9af94" />
<br>



________________________________________________________________________________________________________________________________________________________________________________________

#### c9) The login you cannot replay (Has preflight! Has CSRF token!)
_Get into the admin account. A plain password fuzz returns 403 forever, however long you run it._
<br>

##### _Kyseinen tehtävän toiminta oli avattu sivustolla. Myös komennot kyseiseen tehtävään olivat saatavilla. Suoritin tehtävänannon niiden pohjalta ja pyrin muotoilemaan omin sanoin tekemääni._


Hieman erilainen tehtävä, jossa pelkkä fuzzaus URL-osoitteessa ei riittänyt. Tarkoituksena oli löytää oikea salasana _passwords.txt_ -tiedostosta ja kirjautua admin-käyttäjänä sisään. Salasana syötettiin POST-pyynnön bodyyn tokenina (``  -d "csrf_token=CSRFTOKEN&username=admin&password=FUZZ" \``). 

Käytännössä ffuffi siis fuzzasi kaikki tiedoston salasanat läpi mutta ongelmana oli CSFR-token, joka oli voimassa vain yhden pyynnön ajan. Jos token vanhentui tai oli jo käytetty, palvelin palautti 403-vastauksen kirjautumisyritykseen. Tämä ratkaistiin määrittelemällä login.raw -tiedostoon esipyyntö:

    GET /login HTTP/1.1
    Host: ffuf.io.fi
    Accept: text/html

Sen avulla haettiin kirjautumissivu ennen oikeaa POST-pyyntöä ja esipyynnön vastauksesta ffuf poimi CSFR-tokenin -preflight ominaisuudellaan talteen (``-preflight-var 'CSRFTOKEN:name="csrf_token" value="([a-f0-9]+)"'``). Uusi token haettiin ennen jokaista salasana arvausta, sillä tokenit olivat kertakäyttöisiä. 


<img width="897" height="674" alt="SALASANA" src="https://github.com/user-attachments/assets/de129af9-731c-4314-9af4-dc4d6dfd797f" />
<br>
<br>

ffuf löysi salasanan _vaultline2026_. Syöttämällä tämän ja valmiin käyttäjätunnuksen _admin_ päästiin kirjautumaan sisään sivustolle.

<img width="839" height="254" alt="image" src="https://github.com/user-attachments/assets/fed0da3a-3821-4414-a018-499eb45fafe5" />
<br>

________________________________________________________________________________________________________________________________________________________________________________________



#### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 17.9.2026.

Hoikkala, J. 2026. Fuzzing with Fuff. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/hoikkala-2026-fuzzing-with-ffuf.pdf. Luettu 17.9.2026

Ffuf How to play. Luettavissa: https://ffuf.io.fi/play. Luettu 17.9.2026.

ffuf - Fuzz Faster U Fool. GitHub repositorio. Luettavissa: https://github.com/ffuf/ffuf. Luettu 17.9.2026

