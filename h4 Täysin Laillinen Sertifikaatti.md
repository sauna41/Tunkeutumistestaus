_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable 2 (VirtualBox)_

_Päivämäärä: 11.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h4 Täysin Laillinen Sertifikaatti. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________


### x) Lue/katso ja tiivistä

[OWASP 2021: OWASP Top 10:2021](https://owasp.org/Top10/A01_2021-Broken_Access_Control/[)
  - Broken Access Control eli puutteellinen pääsynhallinta tarkoittaa tilannetta, jossa käyttäjä pystyy tekemään asioita tai näkemään tietoja, joihin hänellä ei pitäisi olla oikeuksia. OWASP:n mukaan ongelma voi esimerkiksi mahdollistaa toisen käyttäjän tietojen katselun, ylläpitäjän toimintojen käyttämisen tavallisena käyttäjänä tai suojattujen sivujen käyttämisen ilman kirjautumista.

  - Haavoittuvuus voi syntyä esimerkiksi muuttamalla URL-osoitteen parametria tai lähettämällä palvelimelle itse muokatun HTTP-pyynnön. Pääsynhallinnan tarkistuksia ei siis pitäisi tehdä ainoastaan selaimen puolella, koska hyökkääjä voi muuttaa selaimen lähettämää dataa. Tarkistukset pitää tehdä palvelimella.

  - OWASP:n suosittelema periaate on deny by default, eli pääsy evätään oletuksena ja sallitaan vain silloin, kun käyttäjällä on siihen oikeus. Lisäksi käyttöoikeuksien pitäisi perustua esimerkiksi käyttäjän rooliin ja resurssin omistajuuteen
  
  <br>
  <br>


PortSwigger Academy:

  [Insecure direct object references (IDOR)](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
  - DOR on pääsynhallinnan haavoittuvuus, jossa sovellus käyttää käyttäjän antamaa tunnistetta suoraan jonkin resurssin hakemiseen ilman riittävää käyttöoikeuden tarkistamista.
  - IDOR liittyy yleensä horizontal privilege escalation -tilanteeseen: käyttäjä ei saa korkeampia käyttöoikeuksia, mutta pystyy käyttämään toisen saman tasoisen käyttäjän tietoja.
  - IDOR voi koskea myös suoraan palvelimella olevia tiedostoja. Jos esimerkiksi käyttäjän tiedosto löytyy muuttamalla URL-osoitteessa tiedoston numeroa, hyökkääjä voi yrittää vaihtaa numeron toisen käyttäjän tiedostoon.
    
  [Path traversal](https://portswigger.net/web-security/file-path-traversal)
  - Path traversal eli directory traversal on haavoittuvuus, jossa hyökkääjä pystyy vaikuttamaan palvelimella käsiteltävään tiedostopolkuun ja tämän avulla lukemaan tiedostoja sovelluksen tarkoitetun hakemiston ulkopuolelta.
  - Path traversal -suojauksia voidaan yrittää kiertää esimerkiksi käyttämällä absoluuttista polkua, vaihtoehtoisia traversal-muotoja tai URL-koodausta. PortSwigger antaa esimerkkeinä muun muassa ....//-muodon sekä URL-koodatun ../-sekvenssin.
  - 
  [Cross-site scripting](https://portswigger.net/web-security/cross-site-scripting)
  - Cross-Site Scripting (XSS) on haavoittuvuus, jossa hyökkääjä pystyy saamaan oman JavaScript-koodinsa suoritettavaksi uhrin selaimessa. XSS voi syntyä esimerkiksi silloin, kun käyttäjän syöttämä teksti sijoitetaan verkkosivun HTML-koodiin ilman asianmukaista käsittelyä tai HTML-koodausta.
  - XSS voidaan jakaa esimerkiksi seuraaviin tyyppeihin:
      - Reflected XSS – haitallinen syöte tulee esimerkiksi HTTP-pyynnön parametrina ja palautetaan heti vastauksessa.
      - Stored XSS – haitallinen syöte tallennetaan palvelimelle ja näytetään myöhemmin muille käyttäjille.
      - DOM-based XSS – haavoittuvuus syntyy selaimessa JavaScriptin käsitellessä käyttäjän hallitsemaa dataa.
  - XSS:n vaikutus riippuu siitä, mitä hyökkääjä pystyy selaimessa tekemään. Pelkkä alert() osoittaa JavaScriptin suorittamisen, mutta todellisessa hyökkäyksessä samaa mahdollisuutta voidaan käyttää esimerkiksi käyttäjän tietojen varastamiseen, luvattomien toimintojen suorittamiseen tai käyttäjän selainistunnon väärinkäyttöön.

________________________________________________________________________________________________________________________________________________________________________________________


### a) Totally Legit Sertificate. Asenna OWASP ZAP, generoi CA-sertifikaatti ja asenna se selaimeesi. Laita ZAP proxyksi selaimeesi. Laita ZAP sieppaamaan myös kuvat, niitä tarvitaan tämän kerran kotitehtävissä. Osoita, että hakupyynnöt ilmestyvät ZAP:n käyttöliittymään

Asensin OWASP ZAPin Kalissa komennolla ```apt install zaproxy``` ja avasin ohjelman komennolla ```zaproxy```. 

Tämä avasi ZAPin. Ohjelma kysyi haluanko pysyvän session,
  "_No, I do not want to persist this session at this moment in time_", 
sillä tässä tehtävssä ei tarvittu pysyvää projektia. 

ZAPin valikoista _Tools_ --> _Network_ --> _Server Certificate_ saatiin generoitua sertifikaatti, jonka tallensin.

<img width="673" height="679" alt="SERVER CERTIFICATE" src="https://github.com/user-attachments/assets/507260ec-01dd-44b4-a1f7-ac97ef51bc22" />

Seuraavaksi oli aika lisätä sertifikaatti selaimeen. Navigoimalla Firefoxissa **about:prerefences#privacy**_ ja avaamalla sertifikaattiasetukset päästiin importtaamaan ZAP-sertifikaatti. Sertifikaatin avulla annettiin luottamus verkkosivujen tunnistamiseen.

<img width="1387" height="379" alt="image" src="https://github.com/user-attachments/assets/ba0ebc86-01f5-412e-a535-37685b4307ab" />

Kun sertifikaatti oli määritetty, vaihdoin Firefoxin käyttämään proxyna ZAPia. Paikallinen proxy toimi localhost -osoitteessa, eli 127.0.0.1 ja portissa 8080. Tämän jälkeen ZAPin pitäisi kaapata HTTP- ja HTTPS-liikenne ZAPiin.

<img width="763" height="415" alt="MANUAL PROXY" src="https://github.com/user-attachments/assets/1d189031-1c58-4837-9aa0-dc10647ad588" />

Liikennettä testattiin vierailemalla webbisivulla ja tarkastamalla, tapahtuuko ZAPissa mitään. Huomattiin, että HTTPS-liikenne kulki ZAPin kautta, joten se toimi niin kuin kuuluikin. 

<img width="529" height="553" alt="ZAP SITES HISTORY" src="https://github.com/user-attachments/assets/7684ea83-870a-4251-a3d9-5c99c7b14f8c" />

<br>
<br>

<img width="1551" height="132" alt="PYYNNÖT" src="https://github.com/user-attachments/assets/17988ddd-fa73-49bc-8175-01741460b4fe" />

<br>
<br>

Sertifikaatti näkyi selaimessa sekä GET -pyyntö päätyi ZAPiin.

<img width="632" height="218" alt="CONNECTION SECURE SERTIFIKAATTI" src="https://github.com/user-attachments/assets/dc2b991c-ecdd-477f-9add-281904cc36cb" />

<img width="955" height="311" alt="GET PYYNTÖ" src="https://github.com/user-attachments/assets/14b882a8-0ac9-4ebb-8512-4d515020d5a7" />

Tehtävässä vaadittiin myös kuvien sieppaaminen, jonka sai kytkettyä päälle klikkaamalla ZAPin _View_ --> _Enable Image History._ Tämän jälkeen myös kuvatiedostojen pyynnöt löytyivät ZAPista.

<img width="517" height="197" alt="IMAGE CAPTURED" src="https://github.com/user-attachments/assets/bcdfa6e8-186d-41a6-9254-438ed1ab4a8e" />

<br>
<br>


#### Lopputulema

OWASP ZAP saatiin toimimaan Firefoxin proxyna osoitteessa 127.0.0.1:8080. ZAPin CA-sertifikaatti asennettiin Firefoxiin, minkä jälkeen myös HTTPS-liikenne näkyi ZAPissa. Lisäksi ZAP määritettiin näyttämään kuvat History-näkymässä.


________________________________________________________________________________________________________________________________________________________________________________________


### b) Kettumaista. Asenna "FoxyProxy Standard" Firefox Addon, ja lisää ZAP proxyksi siihen. Käytä FoxyProxyn "Patterns" -toimintoa, niin että vain valitsemasi weppisivut ohjataan Proxyyn

Asensin FoxyProxy -lisäosan Firefoxiin. FoxyProxyn _Proxies_ välilehdeltä luotiin uusi proxy ZAPia varten. Proxyn määritykset olivat:
  - Type: HTTP
  - Host: 127.0.0.1
  - Port: 8080
  - Proxy by Patterns: Wildcard *.web-security-academy.net/*
      - 


Kaikki liikenne päätyi tässä kohtaa edelleen ZAPiin. Kokeilin säätää Firefoxiin aiemmin asetetun proxyn "_Manual proxy configuconfiguration_" tilasta "_Use System proxy settings_"  tilaan ajatuksena, että tällöin käytössä olisi vain äsken luotu säännön mukainen proxy eikä manuaalisesti asetettu ZAP, joka sieppaa kaiken liikenteen. Tämä ei kuitenkaan toiminut, sillä kaikki liikenne päätyi edelleen ZAPiin. Lopulta ratkaisu olikin hyvin yksinkertainen: Foxyproxysta piti vain valita "Proxy by Patterns". 

<img width="1170" height="421" alt="image" src="https://github.com/user-attachments/assets/018910ed-a89b-499a-91f5-c71a7d2ed291" />

<br>
<br>

Tämän jälkeen muu kuin proxyn säännönmukainen liikenne ei päätyneet enää ZAPiin. PortSwiggerin labrat (_*.web-security-academy.net/*_) päätyivät perille. 

<img width="1492" height="32" alt="PROXY PATTERN CAPTURE" src="https://github.com/user-attachments/assets/3ef0abcf-1f9d-45ac-83e0-553bb3451e64" />

________________________________________________________________________________________________________________________________________________________________________________________


### PortSwigger Labs: ratkaise tehtävät

#### Cross Site Scripting (XSS)
  ##### c) Reflected XSS into HTML context with nothing encoded

Labrassa sovelluksen hakutoiminto sisälsi XSS-haavoittuvuuden: sisältö palautettiin sivun HMTL-kontekstiin ilman oikeaoppista koodausta. Hakukenttään tuli syöttää ```<script>alert("SYÖTE")</script>```. Haun jälkeen syötetty JavaScript suoritettiin, jolloin selain aktivoi alert-ikkunan. Tämä toimii, koska syötetty syöte palautetaan HTML-sivulle ilman suojausta, jolloin skripti ladataan ja ajetaan muun HTML-sisällön kanssa.


<img width="631" height="175" alt="image" src="https://github.com/user-attachments/assets/da7f5f32-114d-41fc-970f-da0f81c744db" />

  ##### d) Stored XSS into HTML context with nothing encoded

Labraharjoituksessa skripti voitiin tallentaa sovellukseen. Blogipostauksista löytyi kommenttilaatikko, johon voitiin syöttää jälleen ```<script>alert("SYÖTE")</script>```. Jättämällä kommentti syötettiin siis skripti ja kun kommentin sisältävä sivu avattiin uudelleen, skripti ladattiin muun HTML-sisällön kanssa. 


  <img width="573" height="690" alt="image" src="https://github.com/user-attachments/assets/8955063c-3135-4ad2-a2c3-8b3fc367bd86" />


  <img width="643" height="181" alt="image" src="https://github.com/user-attachments/assets/7b3362b8-8143-4e6b-ac88-e1fdf64a4d8a" />

  
  
#### e) Selitä esimerkin avulla, mitä hyökkääjä hyötyy XSS-hyökkäyksestä. Alert("Hei Tero!") ei vielä tarjoa kummoista pääsyä.

On totta, että pelkkä _alert()_ ei aiheuta suurta haittaa. Se kuitenkin osoittaa, että hyökkääjän on mahdollista ajaa omaa JavaScript -koodiaan selaimessa. Tällöin hyökkääjällä voi yrittää muuttaa sivuston sisältöä tai käyttää sovelluksen omia toimintoja uhrin istunnon yhteydessä. Jos esimerkiksi ylläpitäjän oikeuksilla liikkeellä oleva käyttäjä avaa XSS-hyökkäyksen sisältävän sivun, on mahdollista, että hyökkääjän JavaScriptiä ajettaisiin ylläpitäjäoikeuksin.

  
#### Path traversal
  f) File path traversal, simple case. Laita tarvittaessa Zapissa kuvien sieppaus päälle.
  g) File path traversal, traversal sequences blocked with absolute path bypass
  h) File path traversal, traversal sequences stripped non-recursively
Insecure Direct Object Reference (IDOR)
  i) Insecure direct object references

  ________________________________________________________________________________________________________________________________________________________________________________________

#### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h3-eternalhomework. Luettu 11.9.2026.

OWASP 2021: OWASP Top 10:2021. Luettavissa: https://owasp.org/Top10/A01_2021-Broken_Access_Control/. Luettu 11.9.2026.

Insecure direct object references (IDOR). Portswigger Academy. https://portswigger.net/web-security/access-control/idor. Luettu 11.9.2026.

Path traversal. Portswigger Academy. Luettavissa: https://portswigger.net/web-security/file-path-traversal. Luettu 11.9.2026.

Cross-site scripting. Portswigger Academy. Luettavissa: https://portswigger.net/web-security/cross-site-scripting. Luettu 11.9.2026.

Cross-site scripting Labs. Portswigger Labs. Luettavissa: https://portswigger.net/web-security/all-labs#cross-site-scripting:~:text=Cross%2Dsite%20scripting,-LAB. Luettu 11.9.2026.

Path Traversal Labs. Portswigger Labs. Luettavissa: https://portswigger.net/web-security/all-labs#path-traversal:~:text=Path%20traversal,-LAB. Luettu 11.9.2026.
