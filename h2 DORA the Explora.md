_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable (VirtualBox)_

_Päivämäärä: 1.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h2 DORA the Explora. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

x) Lue/katso/kuuntele ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Lisää mukaan jokin oma havainto, idea tai kysymys)

Buuri 2026: DORA and TLPT testing - Lecture for Haaga-Helia on 31 March 2026 (pdf, 2 MB)
DORA (Regulation ... on digital operational resilience for the financial sector) (vain nämä kaksi artiklaa):
Article 26 "Advanced testing of ICT tools, systems and processes based on TLPT"
Article 27 "Requirements for testers for the carrying out of TLPT"
TIBER-FI procedures and guidelines (pdf, 1 MB) (vain tämä kohta):
5.4 Testing phase: Red team testing (johdantokappale suoraan 5.4 alta, "5.4.1 Red team test plan creation" alkuun asti)
Vapaaehtoinen bonus: Buuri 2026: D26 - Releasing Your Inner TIBER in Regulated Adversary Simulations. Video, 45 min. Disobey 2026.

________________________________________________________________________________________________________________________________________________________________________________________

### a) Asenna Metasploitable 2 virtuaalikoneeseen.

Latasin [Metasploitable 2](https://sourceforge.net/projects/metasploitable/) ja asensin sen uuteen virtuaalikoneeseen. Ennen koneen käynnistystä muutin vielä verkkoasetuksista verkkoadapterin _Host-only_. Konetta käynnistäessä jäin jumiin "Starting up..." ruutuun, jonka sain korjattua vaihtamalla ytimet takaisin vain yhteen.



<img width="718" height="305" alt="image" src="https://github.com/user-attachments/assets/7e891d74-4e4d-426d-ad08-d6dffb2846f5" />
________________________________________________________________________________________________________________________________________________________________________________________

### b) Tee Kalin ja Metasploitablen välille virtuaaliverkko. Jos säätelet VirtualBoxista

Kali saa yhteyden Internettiin, mutta sen voi laittaa pois päältä
Kalin ja Metasploitablen välillä on host-only network, niin että porttiskannatessa ym. koneet on eristetty intenetistä, mutta ne saavat yhteyden toisiinsa

<img width="638" height="230" alt="image" src="https://github.com/user-attachments/assets/0e57c149-9b2d-4c62-9272-6dacc0cb3c24" />

Yhteys onnistui. Seuraavaksi selvitin, oliko kohdekohde aktiivinen ```nmap -sn <IP-OSOITE>``` -komennolla.

KALI INET 192.168.56.102

Käynnistin ensin Metasploitablen ja varmistin, että se ei ole kiinni verkossa pingaamalla 8.8.8.8 että google.com. Kummatkin pingaukset odotetusti epäonnistuivat. Seuraavaksi tarkastin Metasploitablen oman IP-osoitteen ```ipaddr``` -komennolla, jotta koneeseen saataisiin Kalilla yhteys. IP OSOITE 192.168.56.101/24


Seuraavaksi käynnistin Kalin ja yritin ottaa yhteyttä Metasploitable koneeseen. 

<img width="705" height="255" alt="image" src="https://github.com/user-attachments/assets/5a032668-46a4-4f85-96b4-5c7703cb8522" />

________________________________________________________________________________________________________________________________________________________________________________________

### c) Osoita testein, että 1) koneet eivät saa yhteyttä Internetiin 2) Koneet saavat yhteyden toisiinsa

1) Verkkoyhteyttä testattiin kummallakin koneella pingaamalla Googlen IP-osoitetta 8.8.8.8 sekä verkkotunnusta _google.com_. Kummatkin testit tulivat takaisin negatiivisina. Lisäksi Kalilla kokeilin avata Firefoxilla verkkosivua mutta yhteyttä ei ollut. Tämä todistaa, etteivät kummatkaan koneet ole kiinni verkossa.



2) Navigoimalla verkkoselaimessa Metasploitable koneen IP-osoitteeseen päädyttiin kuitenkin Metasploitablen etusivulle, joten kyseiseen koneeseen oli yhteys.

<img width="808" height="476" alt="image" src="https://github.com/user-attachments/assets/eb81f314-ecaa-444a-94c6-abe13415258a" />


________________________________________________________________________________________________________________________________________________________________________________________


d) Etsi Metasploitable porttiskannaamalla (nmap -sn). Tarkista selaimella, että löysit oikean IP:n - Metasploitablen weppipalvelimen etusivulla lukee Metasploitable.


<img width="808" height="476" alt="image" src="https://github.com/user-attachments/assets/eb81f314-ecaa-444a-94c6-abe13415258a" />

________________________________________________________________________________________________________________________________________________________________________________________

e) Porttiskannaa Metasploitable huolellisesti ja kaikki portit (nmap -A -T4 -p-). Poimi 2-3 hyökkääjälle kiinnostavinta porttia. Analysoi ja selitä tulokset näiden porttien osalta. Voit hakea analyysin tueksi tietoa verkosta, muista merkitä lähteet.



________________________________________________________________________________________________________________________________________________________________________________________

f) Vapaaehtoinen bonus: Sisään vaan. Pääsetkö murtautumaan Metasploitableen?
g) Vapaaehtoinen bonus: jos haluat, voit jo kokeilla metasploit-hyökkäysohjelmaa omaan harjoitusmaaliisi. Tätä katsotaan myöhemmin yhdessäkin. (Muista irrottaa kone Internetistä kokeilujen ajaksi. 'sudo msfdb init', 'sudo msfconsole').

________________________________________________________________________________________________________________________________________________________________________________________

Karvinen, T. Tunkeutumistestaus. Opintojakson kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 1.9.2026.

Metasploitable 2. Ladattavissa: https://sourceforge.net/projects/metasploitable/files/latest/download. Luettu 1.9.2026.
