_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable 2 (VirtualBox)

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h8 bonus. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________


## H8 Bonus
_Tähän raporttiin on kerätty kaikki suorittamani vapaaehtoiset tehtävät kotitehtävistä h1-7_

________________________________________________________________________________________________________________________________________________________________________________________


### Asenna pencode ja muunna sillä jokin merkkijono (encode a string)

pencode on työkalu, jonka avulla voidaan rakentaan payloadien encoding-ketjuja. Se automatisoi datan muuttamisen usealla eritavalla. Penetraatiotestauksessa samaa payloadia voidaan joutua esittämään eri formaateissa riippuen siitä, mihin kohtaan sitä ollaan syöttämässä, jolloin pencode käytännössä suorittaa: payload --> JSON encoding --> URL encoding --> base64 encoding. 

Asennus tapahtui [GitHubin](https://github.com/ffuf/pencode) ohjeistuksella:

    go install github.com/ffuf/pencode/cmd/pencode@latest

Työkalun käyttö oli hyvin yksinkertaista: annoin parametrina stringin ja pencode muutti automaattisesti syötteen haluttuihin muotoihin.

<img width="693" height="158" alt="image" src="https://github.com/user-attachments/assets/e25bba76-89b4-4896-a72d-8595c515ba38" />
<br>
<br>

pencode tukee laajaa kirjastoa eri formaatteja:

<img width="800" height="701" alt="image" src="https://github.com/user-attachments/assets/73674cde-3db1-4e03-8597-f9199e48d42e" />

_pencoden manuaali & tuetut formaatit_

________________________________________________________________________________________________________________________________________________________________________________________


### Vapaaehtoinen: Mitmproxy. Asenna MitmProxy. Esittele sitä terminaalissa (TUI). Ota TLS-purku käyttöön. Poimi historiasta hakupyyntö, muokkaa sitä ja lähetä uudelleen

Mitmproxy on työkalu verkkoliikenteen sieppaukseen ja muokkaukseen sekä TLS-salauksen purkamiseen. Tarkastelu, muokkaus ja uudelleenlähetys tapahtuu sen omassa komentorivikäyttöliittymässä. 

#### Käyttöönotto

Asensin Mitmproxyn sen [omilta sivuilta](https://www.mitmproxy.org/) ja käynnistin proxyn komennolla ``mitmproxy``. Tämä avasi tyhjän näkymän eikä verkkoliikenne päätynyt proxyyn. Olin aiemmin määrittänyt selaimen proxy-asetuksia FoxyProxy -harjoituksia varten, joten suljin FoxyProxyn ja vaihdoin Firefoxin verkkoasetuksista proxy-palvelimeksi ``127.0.0.1`` ja portiksi ``8080``. 

Tämän jälkeen navigoimalla sivulle "ttp://mitm.it" päästiin lataamaan mitmproxy sertifikaatti.

<img width="880" height="1078" alt="image" src="https://github.com/user-attachments/assets/547470f2-1029-449b-9a16-509dab958723" />
_mitmproxyn sertifikaatin lataaminen_

Firefox käyttää itsenäistä sertifikaattivarastoa, joten uusi sertifikaatti oli lisättävä manuaalisesti. Tämä tapahtui Firefoxin asetuksista: _Settings --> Privacy & Security --> Certificates --> View certificates --> Import_

<img width="820" height="974" alt="image" src="https://github.com/user-attachments/assets/9f0f2885-702d-4835-97ab-a075b5b5223c" />

_sertifikaatin importtaus_
<br>

Tämän jälkeen HTTPS-liikenne näkyi mitmproxyn käyttöliittymässä. 


#### Terminaalin esittely

<img width="873" height="521" alt="image" src="https://github.com/user-attachments/assets/f5e87ef7-c2b5-497f-9c71-35a470393607" />

<liikenne proxyssa>
<br>

Mitmproxyyn tallentui selaimen HTTPS-pyyntöjä, joista voitiin tarkastella tarkemmin HTTP-metodia, URL-osoitetta, headereita ja POST-vastausta.

Pyyntöjä pystyi selamaan ja klikkaamalla/enterillä pääsi tarkastelemaan tiettyä pyyntöä tarkemmin. 
- Muokkauksia pystyi tekemään _edit_-modessa painamalla e-näppäintä.

<img width="980" height="396" alt="image" src="https://github.com/user-attachments/assets/07670ba0-c681-4b44-b2ed-c792628ab3c8" />

_mitm TUI_
<br>

``e``-näppäimellä pystyi muokkaamaan erinäisiä ominaisuuksia:

<img width="967" height="433" alt="EDIT MODE" src="https://github.com/user-attachments/assets/0fc2a9fb-71f1-4c53-8695-fdb4ef8ce6d5" />
_muokattavat osat_ 
<br>


________________________________________________________________________________________________________________________________________________________________________________________

### Ratkaise lisää PortSwigger Labs -tehtäviä

#### Unprotected admin functionality with unpredictable URL

Labrassa oli suojaamaton admin-paneeli. Se löytyi tutkimalla sivun lähdekoodia, jonka sai avattua tarkasteluun right-clikkaamalla webbisivua. Lähdekoodista löytyi suora viittaus admininiin:

<img width="760" height="217" alt="image" src="https://github.com/user-attachments/assets/2e96c9f9-5dc8-452d-b31d-81a35e62f9f1" />

_lähdekoodin tutkimista_
<br>

Lähdekoodista paljastui suoraan, että admin-paneeli linkkasi https://URL/admin-j8prvl osuuden takaa (``adminPanelTag.setAttribute('href', '/admin-j8prvl');``). Muuttamalla URLin loppuosa, päästiin sisään. Paneelissa voitiin hallinnoida käyttäjiä ja poistaa tehtäväannnon mukaisesti "carlos" -käyttäjä.


<img width="608" height="229" alt="image" src="https://github.com/user-attachments/assets/4ea21324-5534-4ac2-9d8c-8548181058f0" />

_käyttäjienhallinta admin-paneelissa_
<br>

#### Unprotected admin functionality

Toinen labra, jossa oli suojaamaton admin-paneeli. Tällä kertaa se löytyi lisäämällä URLin loppuun _robots.txt_, joka on tarkoitettu hakukoneille. Se kertoo, mitä hakukoneet saavat indeksoida ja ne saattavat myös paljastaa admin-polkuja kuten tässä labraharjoituksessa. 


<img width="592" height="76" alt="image" src="https://github.com/user-attachments/assets/de17b3a3-82d7-4f36-bb4d-e22b3ec93100" />

_robots.txt_
<br>

Admin-paneelin polku paljastui ja sinne pystyi navigoimaan jälleen vaihtamalla URLista /robots.txt --> /administrator-panel


<img width="608" height="229" alt="image" src="https://github.com/user-attachments/assets/4ea21324-5534-4ac2-9d8c-8548181058f0" />

_käyttäjienhallinta admin-paneelissa_
<br>


________________________________________________________________________________________________________________________________________________________________________________________


### Lähteet: 

https://github.com/ffuf/pencode. Luettu 1.10.2026.

Mitmproxy. Ladattavissa: https://www.mitmproxy.org/. Luettu 1.10.2026.

