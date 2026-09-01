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

### b) Tee Kalin ja Metasploitablen välille virtuaaliverkko

Lähdin rakentamaan Kalin ja Metasploitableb välille eristettyä verkkoa. Tämä oli mahdollista Host-Only-verkossa, jossa kyseiset koneet ovat yhteydessä toisiinsa mutta niillä ei ole pääsyä laajempaan internettiin. VirtualBoxissa kummankin koneen verkkoadapterin vaihdettiin _Host-only_, jolloin ne toimivat samassa Host-verkossa. 


<img width="373" height="53" alt="VERKON RAKENNEKUVA" src="https://github.com/user-attachments/assets/a37548b7-0c56-4044-86d6-0b8114915903" />

Kun verkkoasetukset oli säädetty kohdilleen, avasin kummatkin koneet ja tarkastin niiden ip-osoitteet ```ip addr``` -komennolla. IP-osoitteet olivat 192.168.56.101 & 192.168.56.102. 

Seuraava askel oli varmistaa, että Metasploitable oli aktiivinen. Tämä tapahtui Kalissa komennolla ```nmap -sn <METASPLOITABLEN IP-OSOITE>```. Komennolla tarkistetaan onko laite verkossa mutta ei kuitenkaan suoriteta porttiskannausta.
 - _sn_ tarkoittaa Ping Scan:ia.

<img width="657" height="142" alt="image" src="https://github.com/user-attachments/assets/f0f1f226-5d2f-4339-90c4-b83cd7650437" />

Metasploitable löytyi ja oli aktiivinen. Pystyin Kali-koneella pingaamaan onnistuneesti Metasploitablea. 


<img width="638" height="230" alt="PINGAUS KONEIDEN VÄLILLÄ" src="https://github.com/user-attachments/assets/0e57c149-9b2d-4c62-9272-6dacc0cb3c24" />


________________________________________________________________________________________________________________________________________________________________________________________

### c) Osoita testein, että 
  1) koneet eivät saa yhteyttä Internetiin
  2) Koneet saavat yhteyden toisiinsa
  
Kummankaan koneen ei pitäisi olla yhteydessä internettiin jo pelkästään VirtualBoxin asetusten kautta. Varmistin asian kuitenkin myös käytännössä kummallakin koneella. Pingaamalla Googlen IP-osoitetta 8.8.8.8 sekä verkkotunnusta _google.com_. tulostui odotetusti vain virheilmoituksia. Lisäksi Kalilla kokeilin avata Firefoxilla verkkosivua mutta yhteyttä ei ollut. Tämä todistaa, etteivät kummatkaan koneet saaneet yhteyttä internettiin.

<img width="550" height="155" alt="image" src="https://github.com/user-attachments/assets/25cb9839-02c8-4194-838e-716bd482c70d" />

<img width="389" height="101" alt="image" src="https://github.com/user-attachments/assets/ff2f8c2c-b2a1-4137-88b2-cd7db99700c1" />



<br>
<br>

Kuitenkin navigoimalla verkkoselaimessa Metasploitable koneen IP-osoitteeseen päädyttiin Metasploitablen etusivulle. Tämä puolestaan todisti, että Kali Ja Metasploitable kykenivät olemaan yhteydessä toisiinsa Host-only-verkon kautta vaikka internettiin ei ollutkaan pääsyä. Metasploitablea pystyi myös pingaamaan sen IP-osoitteella.

<img width="808" height="476" alt="image" src="https://github.com/user-attachments/assets/eb81f314-ecaa-444a-94c6-abe13415258a" />

<img width="631" height="123" alt="image" src="https://github.com/user-attachments/assets/0f135036-d9cf-49cc-b93f-f0f70fd12274" />



________________________________________________________________________________________________________________________________________________________________________________________


### d) Etsi Metasploitable porttiskannaamalla

<img width="658" height="145" alt="image" src="https://github.com/user-attachments/assets/23dc1f4d-c085-4dd5-9873-70d78da59852" />


Askeleet tulivatkin jo aiemmissa osuuksissa todistettua mutta askeleet tähän olivat:

1. ```nmap -sn 192.168.56.0/24``` - host discovery. Komento selvitti kaikki verkossa 192.168.56.xxx olevat aktiiviset laitteet.
2. Tuloksesta selvisi Metasploitablen IP-osoite. Tämä oli helppo päätellä, sillä verkossa oli vain itse Kali ja Metasploitable. 
3. Kalissa navigoimalla löydettyyn IP-osoitteeseen päädyttiin Metasploitablen etusivulle. Tämä varmisti, että IP-osoite oli oikein.

<img width="808" height="476" alt="image" src="https://github.com/user-attachments/assets/eb81f314-ecaa-444a-94c6-abe13415258a" />


________________________________________________________________________________________________________________________________________________________________________________________

### e) Porttiskannaa Metasploitable huolellisesti ja kaikki portit (nmap -A -T4 -p-). Poimi 2-3 hyökkääjälle kiinnostavinta porttia. Analysoi ja selitä tulokset näiden porttien osalta.

Suoritin Metasploitablelle perusteellisen skannauksen ```nmap -A -T4 -p-```  -komennolla.

  - -A ottaa skannaukseen mukaan käyttöjärjestelmän, palveluiden ja skriptien tunnistamisen.
  - T4 määrittää skannauksen nopeuden
  - -p- määrittää skannauksen TCP-portteihin välillä 1-655535

Skannauksesta löytyi 25 avointa porttia. 


1. Portissa 21 FTP-palvelu, jossa anonymous kirjautuminen on sallittu. Lisäksi Nmap tunnisti tietyn version palvelusta, joka mahdollistaa hyökkääjän tarkastavan onko kyseiseeen versioon tiedettyjä haavoittuvuuksia.

<img width="543" height="60" alt="image" src="https://github.com/user-attachments/assets/e6a5c269-20d1-4e61-9a95-a9351677a362" />


2. toinen portti

3. kolmas portti

________________________________________________________________________________________________________________________________________________________________________________________

f) Vapaaehtoinen bonus: Sisään vaan. Pääsetkö murtautumaan Metasploitableen?
g) Vapaaehtoinen bonus: jos haluat, voit jo kokeilla metasploit-hyökkäysohjelmaa omaan harjoitusmaaliisi. Tätä katsotaan myöhemmin yhdessäkin. (Muista irrottaa kone Internetistä kokeilujen ajaksi. 'sudo msfdb init', 'sudo msfconsole').

________________________________________________________________________________________________________________________________________________________________________________________

Karvinen, T. Tunkeutumistestaus. Opintojakson kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 1.9.2026.

Metasploitable 2. Ladattavissa: https://sourceforge.net/projects/metasploitable/files/latest/download. Luettu 1.9.2026.
