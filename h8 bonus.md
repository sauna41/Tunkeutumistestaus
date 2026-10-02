_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable 2 (VirtualBox)

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h8 bonus. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________


## H8 Bonus
_Tähän raporttiin on kerätty kaikki suorittamani vapaaehtoiset tehtävät kotitehtävistä h1-7_

________________________________________________________________________________________________________________________________________________________________________________________


### h2

#### Buuri 2026: D26 - Releasing Your Inner TIBER in Regulated Adversary Simulations. Video, 45 min. Disobey 2026.

#### Sisään vaan. Pääsetkö murtautumaan Metasploitableen?



#### Vapaaehtoinen bonus: jos haluat, voit jo kokeilla metasploit-hyökkäysohjelmaa omaan harjoitusmaaliisi. Tätä katsotaan myöhemmin yhdessäkin. (Muista irrottaa kone Internetistä kokeilujen ajaksi. 'sudo msfdb init', 'sudo msfconsole').

________________________________________________________________________________________________________________________________________________________________________________________

h3

n) Vapaaehtoinen: Titityy. Saatko Metasploitableen tty-shellin, eli esimerkiksi avattua koko ruudulle piirtävän nano:n?
o) Vapaaehtoinen, vaikea: Kokeile jotain kilpailevaa hyökkäystyökalua tai vihamielistä etäkäyttötyökalua, kuten Sliver tai Scarecrow.
p) Vapaaehtoinen: Asenna ja korkkaa Metasploitable 3. Karvinen 2018: Install Metasploitable 3 – Vulnerable Target Computer
q) Vapaaehtoinen: Peekaboo. Demonstroi, kuinka hyökkääjä vakoilee meterpreterillä. Kuuntele mikrofonilla, ota kuvia tai videota kameralla. (Huolehdi, ettei ulkopuolisia joudu kuunnelluksi tai katselluksi.)
m) Vapaaehtoinen: Etsi esimerkki Mitre Attack proseduurista (procedure), jossa joku uhkatoimija on käyttänyt samoja tekniikoita.


________________________________________________________________________________________________________________________________________________________________________________________


h4

#### Asenna pencode ja muunna sillä jokin merkkijono (encode a string)

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



#### Hakupyynnön muokkaus ja lähetys





________________________________________________________________________________________________________________________________________________________________________________________

#### Vapaaehtoinen: Ratkaise lisää PortSwigger Labs -tehtäviä. Kannattaa tehdä helpoimmat "Apprentice" -tason tehtävät ensin.

________________________________________________________________________________________________________________________________________________________________________________________


### Lähteet: 

https://github.com/ffuf/pencode. Luettu 1.10.2026.

Mitmproxy. Ladattavissa: https://www.mitmproxy.org/. Luettu 1.10.2026.

