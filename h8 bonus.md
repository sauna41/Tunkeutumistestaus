_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable 2 (VirtualBox)

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h8 bonus. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________


## H8 Bonus
_Tähän raporttiin on kerätty kaikki suorittamani vapaaehtoiset tehtävät kotitehtävistä h1-7_



h2
Vapaaehtoinen bonus: Buuri 2026: D26 - Releasing Your Inner TIBER in Regulated Adversary Simulations. Video, 45 min. Disobey 2026.

f) Vapaaehtoinen bonus: Sisään vaan. Pääsetkö murtautumaan Metasploitableen? 
g) Vapaaehtoinen bonus: jos haluat, voit jo kokeilla metasploit-hyökkäysohjelmaa omaan harjoitusmaaliisi. Tätä katsotaan myöhemmin yhdessäkin. (Muista irrottaa kone Internetistä kokeilujen ajaksi. 'sudo msfdb init', 'sudo msfconsole').

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


pencode tukee laajaa kirjastoa eri formaatteja:

<img width="800" height="701" alt="image" src="https://github.com/user-attachments/assets/73674cde-3db1-4e03-8597-f9199e48d42e" />
_pencoden manuaali & tuetut formaatit_


#### Vapaaehtoinen: Mitmproxy. Asenna MitmProxy. Esittele sitä terminaalissa (TUI). Ota TLS-purku käyttöön. Poimi historiasta hakupyyntö, muokkaa sitä ja lähetä uudelleen.


#### Vapaaehtoinen: Ratkaise lisää PortSwigger Labs -tehtäviä. Kannattaa tehdä helpoimmat "Apprentice" -tason tehtävät ensin.


