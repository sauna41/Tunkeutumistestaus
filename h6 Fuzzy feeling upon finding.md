_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)

_Päivämäärä: 17.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h5 Fuzzy. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

### x) Lue/katso/kuuntele. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva kustakin artikkelista. Kannattaa lisätä myös jokin oma ajatus, idea, huomio tai kysymys. [Päivitys 2026-09-25 w39 Fri: laitoin Hoikkalan kalvot tähän mukaan, kun sain luvan.]

Hoikkala 2026: Fuzzing with Fuff, kalvot joohoin esityksestä kurssilta.



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


Asetin vielä tämän ffuf-version järjestelmänlaajuisesti käyttöön:

    sudo install -m 755 ffuf /usr/local/bin/ffuf
    
________________________________________________________________________________________________________________________________________________________________________________________


#### c1) Content discovery (Vaultline https://ffuf.io.fi/play tehtävät on numeroitu näin, käytetään tässä samoja.).


Tavoitteena oli tutkia ffuf-harjoitusympäristön HTTP-palvelinta ja löytää palvelimelta olemassa olevia resursseja joihin ei kuitenkaan ole linkitystä. 

Käytin content discovery -fuzzausta, jossa ffuf kokeilee sanalistan sanoja URL-osoitteen eri polkuina.

Harjoitusympäristöltä saatiin ladattua valmis sanalista komennolla ``curl -O https://ffuf.io.fi/wordlists/content.txt``. 

Ensimmäisellä ajolla ffuf löysi 2000 vastausta. Silmämääräisesti näytti siltä, että kaikki olivat mallia:

- status: 200
- words: 135
- lines: 32
 
Tuloksia oli liikaa, jotta niitä olisi ollut järkevää lähteä sorttaamaan manuaalisesti. Automaattinen kalibrointi saatiin käyttöön lisäämällä loppuun vipu ``-ac``. Tämä pyrkii tunnistamaan normaalin wildcard-vastauksen ja suodattamaan sen kaltaiset vastaukset pois.

    ffuf -w content.txt -u https://ffuf.io.fi/FUZZ -ac

<img width="971" height="316" alt="image" src="https://github.com/user-attachments/assets/5282905c-a9ae-4792-8774-4d5f6c4c9ed2" />
<br>

Automaattisen kalibroinnin lisäksi tuloksia voisi rajata myös yksittäisten flagien avulla. Samaan tulokseen päästäisiin esimerkiksi käyttämällä

``-w``

- määrittää wordlistin (_content.txt_)

``-mc``

- match codes: näyttää vain halutut HTTP-statuskoodit 

``-fw``

- filter words: suodattaa pois vastaukset, joissa on tietty määrä sanoja (esim -fw 135)


<br>

##### Analysointi

``status 200`` tarkoittaa, että palvelin palautti onnistuneen vastauksen. _admin, login_ ja _docs_ löytyivät.

``status 301`` tarkoittaa uudelleenohjausta. _backup, files, .git_ ja _api_ palauttivat tämän. Palvelin siis tunnisti pyynnön ja ohjasi pyynnön toiseen osoitteeseen. 

``.env`` ja ``.git`` mahdollisesti viittaavat sovelluksen konfiguraatioon tai Git-versionhallintaan. Navigoimalla sivustoa tavallisena käyttäjä ilman fuzzausta nämä ovat resursseja, joita tavallinen käyttäjä ei todennäköisesti löydä. Tutkimalla niitä tarkemmin olisi mahdollista, että esimerkiksi versiohistoriasta löytyisi tunnuksia tai muuta, joka auttaisi tunkeutumisessa. 

Tuloksissa oli esimerkiksi _login, pricing__ ja _product_ vaikka ne olivat selkeästi näkyvillä ja navigoitavissa sivustolla. Tehtävän tarkoitus oli löytyy resurssit, joihin ei ole linkitystä, joten vielä oli suodatettavaa jäljellä. Selvitin, mitä resursseja /play-sivu linkittää ja vertasin niitä ffufin tuloksiin. 

Komennolla ``curl -s https://ffuf.io.fi/play | grep -oE 'href="[^"]+"'`` haettiin /play-sivun linkitetyt resurssit.

    > curl -s <URL>   // hakee sivun HTML-sisällön
    > grep -oE     // etsii HTML:stä href-linkit ja tulostaa vain löydökset
    > ="[^"]+"'    // regular expression (regex). Käytännössä palauttaa yhedn tai useamman merkin, joka ei ole lainausmerkki. 


<img width="923" height="179" alt="image" src="https://github.com/user-attachments/assets/b7f93eef-b2ba-413e-b425-7242c89d91ac" />
<br>

Näitä tuloksia vertaamalla aiempaan ffuf -tulosteeseen, saatiin selville, että _product, pricing, docs_ ja _login_ resurssit löytyivät sivulta. Lopputuloksena tehtävänannon kysymykseen jäljellä jäävät siis kaikki muut ffufin löytämät tulokset.



________________________________________________________________________________________________________________________________________________________________________________________
     
#### c2) The interesting non-200



________________________________________________________________________________________________________________________________________________________________________________________

#### c3) Recursion
_The wordlist holds names, not paths, so C1 found you 13 things and none of them nested. Descending finds more._
<br>

Rekursion avulla ffuf jatkaa syvemmälle löytyneiden hakemistojen sisälle. C1-tehtävässä löydettiin 13 tulosta, mutta niiden alta löytyy vielä lisää tutkittavaa. Käytin komentoa

``ffuf -w content.txt -u https://ffuf.io.fi/FUZZ -mc 200 -fw 135 -recursion -recursion-depth 3`` 

jonka avulla ffuf löysi samat tulokset kuin C1-tehtävssä mutta tällä kertaa fuzzaus jatkui myös löydettyjen hakemistojen sisälle (``-recursion``). Myös rekursiosyyvyden pystyi määrittämään omalla vivullaan (``-recursion-deth3``).

<br>
<img width="832" height="577" alt="REKURSIO" src="https://github.com/user-attachments/assets/fc975346-36c4-4ac8-a454-0df568fe0213" />
<br>

Tuloksista nähtiin, että ffuf löysi samoja resursseja (_docs, product, pricing, jne_) kuin aiemmin, mutta niiden sisältä myös uusia löydöksiä, kuten _db.sql.bak_ ja _v2_. 

________________________________________________________________________________________________________________________________________________________________________________________

#### c4) Virtual hosts
_Three hostnames under ffuf.io.fi serve different content from this same address. Find all three._
<br>

Tehtävänä oli löytää kolme resurssia. Lähdin tällä kertaa fuzzaamaan headereita. 

Ensimmäinen löydös oli _admin_

<img width="967" height="566" alt="image" src="https://github.com/user-attachments/assets/5d005333-cd38-4d93-9f0f-6d5a8fc9af94" />
<br>

Muuttamalla parametreja 

________________________________________________________________________________________________________________________________________________________________________________________

#### c9) The login you cannot replay (Has preflight! Has CSRF token!)

Todennäköinen toimintaketju tehtävässä on

1. ffuf lähettää preflight-requestin
2. palvelin palauttaa tokenin tai cookien
3. preflight-var poimii kyseisen arvon
4. ffuf suorittaa login-requestin
5. ffuf korvaa salasanan poimitulla tokenilla


HTML:ää tutkimalla varmistin, että CSFR-token on kirjautumislomakkeessa.

<img width="966" height="168" alt="image" src="https://github.com/user-attachments/assets/d8808ebe-afdd-4885-85c4-69100d97f6a4" />



________________________________________________________________________________________________________________________________________________________________________________________



#### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 17.9.2026.

Hoikkala, J. 2026. Fuzzing with Fuff. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/hoikkala-2026-fuzzing-with-ffuf.pdf. Luettu 17.9.2026

Ffuf How to play. Luettavissa: https://ffuf.io.fi/play. Luettu 17.9.2026.

ffuf - Fuzz Faster U Fool. GitHub repositorio. Luettavissa: https://github.com/ffuf/ffuf. Luettu 17.9.2026

