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

Latasin OWASP ZAPin ```apt install zaproxy```. Tämä avasi ZAPin. Ohjelma kysyi haluanko pysyvän session, "No, I do not want to persist this session at this moment in time", sillä tässä tehtävssä ei tarvittu pysyvää projektia. 

Tools --> Network --> Server Certificate kohdasta saatiin sertifikaatti. Tallensin sen työpöydälle.

<img width="673" height="679" alt="SERVER CERTIFICATE" src="https://github.com/user-attachments/assets/507260ec-01dd-44b4-a1f7-ac97ef51bc22" />

Seuraavaksi oli aika asentaa se selaimeen. Navigoimalla Firefoxissa **about:prerefences#privacy**_ ja avaamalla sertifikaatit päästiin importtaamaan ZAP-sertifikaatti.

<img width="1387" height="379" alt="image" src="https://github.com/user-attachments/assets/ba0ebc86-01f5-412e-a535-37685b4307ab" />

Firefoxin verkkoasetuksissa vaihdettiin manuaaliseen proxyyn:

<img width="763" height="415" alt="MANUAL PROXY" src="https://github.com/user-attachments/assets/1d189031-1c58-4837-9aa0-dc10647ad588" />

Liikennettä testattiin vierailemalla webbisivulla ja tarkastamalla, tapahtuuko ZAPissa mitään. Sinne oli tallentunut https pyyntöjä, joten ZAP-proxy toimi. 

<img width="529" height="553" alt="ZAP SITES HISTORY" src="https://github.com/user-attachments/assets/7684ea83-870a-4251-a3d9-5c99c7b14f8c" />

<br>
<br>

<img width="1551" height="132" alt="PYYNNÖT" src="https://github.com/user-attachments/assets/17988ddd-fa73-49bc-8175-01741460b4fe" />

<br>
<br>

Sertifikaatti näkyi selaimessa sekä GET -pyyntö päätyi ZAPiin.

<img width="632" height="218" alt="CONNECTION SECURE SERTIFIKAATTI" src="https://github.com/user-attachments/assets/dc2b991c-ecdd-477f-9add-281904cc36cb" />

<img width="955" height="311" alt="GET PYYNTÖ" src="https://github.com/user-attachments/assets/14b882a8-0ac9-4ebb-8512-4d515020d5a7" />

Myös kuvat saatiin kaapattua klikkaamalla ZAPin View --> Enable Image History.

<img width="517" height="197" alt="IMAGE CAPTURED" src="https://github.com/user-attachments/assets/bcdfa6e8-186d-41a6-9254-438ed1ab4a8e" />

<br>
<br>

Asensin OWASP ZAPin ja määritin Firefoxin käyttämään sitä proxyna osoitteessa 127.0.0.1:8080. Generoin ZAPissa CA-sertifikaatin ja asensin sen Firefoxiin luotettuna varmentajana. Testasin toimintaa HTTP- ja HTTPS-sivuilla, jolloin selaimen pyynnöt näkyivät ZAPin History-näkymässä.

Lisäksi otin ZAPissa käyttöön kuvien näyttämisen History-näkymässä. Wikipediaa ladattaessa ZAPiin ilmestyi esimerkiksi wikipedia.png-kuvatiedoston GET-pyyntö. Näin varmistin, että myös kuvien pyynnöt näkyvät ZAPissa.


________________________________________________________________________________________________________________________________________________________________________________________


### b) Kettumaista. Asenna "FoxyProxy Standard" Firefox Addon, ja lisää ZAP proxyksi siihen. Käytä FoxyProxyn "Patterns" -toimintoa, niin että vain valitsemasi weppisivut ohjataan Proxyyn

Asensin FoxyProxyn Firefoxiin. Lisäkkeen _Proxies_ välilehdeltä luotiin uusi proxy, johon jälleen localhost 127.0.0.1 ja portti 8080.

Kaikki liikenne päätyi edelleen ZAPiin, joten kokeilin säätää Firefoxiin aiemmin asetetun proxyn "Manual proxy configuconfiguration" tilasta "Use System proxy settings" ajatuksena, että Foxyproxy tulisi käyttöön. Lopulta ratkaisu olikin hyvin yksinkertainen: Foxyproxysta pitikin vain valita "Proxy by PAtterns". 

<img width="1160" height="465" alt="image" src="https://github.com/user-attachments/assets/0fa20c5a-ecaa-4475-8b53-8fa9288fe6aa" />

Tämän jälkeen muut HTTP-pyynnöt kuin säännönmukaiset eivät päätyneet enää ZAPiin. PortSwiggerin labrat päätyivät perille. 

<img width="1492" height="32" alt="PROXY PATTERN CAPTURE" src="https://github.com/user-attachments/assets/3ef0abcf-1f9d-45ac-83e0-553bb3451e64" />

________________________________________________________________________________________________________________________________________________________________________________________


#### PortSwigger Labs: ratkaise tehtävät

Cross Site Scripting (XSS)
  c) Reflected XSS into HTML context with nothing encoded
  d) Stored XSS into HTML context with nothing encoded
  e) Selitä esimerkin avulla, mitä hyökkääjä hyötyy XSS-hyökkäyksestä. Alert("Hei Tero!") ei vielä tarjoa kummoista pääsyä. (Tässä alakohdassa ei tarvitse tehdä testejä tietokoneella, pelkkä lyhyt ja selkeä selitys riittää.)

  
Path traversal
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
