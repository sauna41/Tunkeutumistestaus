_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable (VirtualBox)_

_Päivämäärä: 1.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h2 DORA the Explora. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

### x) Lue/katso/kuuntele ja tiivistä.

**[Buuri 2026: DORA and TLPT testing - Lecture for Haaga-Helia on 31 March 2026]( https://terokarvinen.com/buuri-2026-dora-and-threat-lead-penetration-testing/buuri-2026-dora-and-threat-lead-penetration-testing--teros-pentest-course.pdf)**
- DORA (Digital Operational Resilience Act) on EU-asetus, joka velvoittaa rahoitusalan toimijoita hallitsemaan digitaalisia riskejä. Tähän lukeutuvat myös tunkeutumistestaukset.
- Suomen Pankin omistama TIBER-FI on Euroopan Unionin laajainen, Suomen oma Red Teaming -testaus kehys. 
- TIBER projekti kestää noin 12-18 kk. Se koostuu suunnitteluvaiheesta, testausvaiheesta & lopetusvaiheesta.
     - Projektiin osallistuu Control Team, joka toimii ohjaajana, tunkeutujan roolissa oleva Red Team & puolustava osapuolena Blue Team.
     -Leg-upit ovat apuja, joita hyökkääjille voidaan antaa esimerkiksi aikarajoitteiden vuoksi, jotta projekti etenee. Leg-upit voivat olla esimerkiksi tunnuksia, tietoja tai vinkkejä teknisestä toteutuksesta.


**[DORA (Regulation ... on digital operational resilience for the financial sector)](https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng)**
- Article 26 "Advanced testing of ICT tools, systems and processes based on TLPT"
     - Finanssialan toimijoille tehdään Threat-Led Penetration Testingiä tarkoituksena selvittää, kuinka hyvin ne kestävät hyökkäyksiä.
     - Tehdään vähintään kolmen vuoden välein
     - Testaus kohdistuu ICT-järjestelmiin, työkaluihin ja prosesseihin käyttäen todellisia uhkia jäljitteleviä hyökkäyksiä.
- Article 27 "Requirements for testers for the carrying out of TLPT"
     - Testaajilla täytyy olla kokemusta tietoturvatestauksesta: pätevyys, riippumattomuus & sopiva
     - Myös ulkopuolisen testaajan tulee täyttää DORA:n vaatimukset
     - Tavoite on onnistua toteuttamaan luotettava, puolueeton ja riittävän vaativa testaus
     - Tulokset on dokumentoitava riittävän kattavasti

       
**[TIBER-FI procedures and guidelines: 5.4 Testing phase: Red team testing](https://www.suomenpankki.fi/globalassets/bof/en/money-and-payments/the-bank-of-finland-as-catalyst-payments-council/tiber-fi/tiber-fi-2.0-procedures-and-guidelines.pdf)**
- Red Team testing suunnittelee ja toteuttaa todellista hyökkäävän simuloivan hyökkäyksen
- Testi sisältää testisuunnitelman sekä aktiivisen hyökkäyksen
- Eteneminen tapahtuu tiedustelu -> valmistelu -> hyökkäys -> exploit -> liikkuminen -> tavoitteen saavuttaminen 
- Jos testaajille annetaan lisätietoja kohteesta, puhutaan Grey box testistä kun taas Black boxissa testaajille ei ole annettu etukäteen hyödyllistä tietoa
- Jos testaus ei muuten etene, voidaan testaajille antaa avustusta leg-upeilla.
________________________________________________________________________________________________________________________________________________________________________________________

### a) Asenna Metasploitable 2 virtuaalikoneeseen.

Latasin [Metasploitable 2](https://sourceforge.net/projects/metasploitable/) ja asensin sen uuteen virtuaalikoneeseen. Ennen koneen käynnistystä muutin vielä verkkoasetuksista verkkoadapterin _Host-only_. Käynnistin koneen ja kirjauduin sisään oletustunnuksilla msdadmin / msfadmin. 


<img width="718" height="305" alt="image" src="https://github.com/user-attachments/assets/7e891d74-4e4d-426d-ad08-d6dffb2846f5" />

________________________________________________________________________________________________________________________________________________________________________________________

### b) Tee Kalin ja Metasploitablen välille virtuaaliverkko

Lähdin rakentamaan Kalin ja Metasploitablen välille eristettyä verkkoa. Tämä oli mahdollista Host-Only-verkossa, jossa kyseiset koneet ovat yhteydessä toisiinsa mutta niillä ei ole pääsyä laajempaan internettiin. VirtualBoxissa kummankin koneen verkkoadapterin vaihdettiin _Host-only_, jolloin ne toimivat samassa Host-verkossa. 


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
  - -p- määrittää skannauksen TCP-portteihin välillä 1–65535

Skannauksesta löytyi 25 avointa porttia. Analysoin tuloksia [Exploitability Guiden](https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/) avulla ja päädyin valitsemaan portit 21, 1524 & 3632.


1. Portissa 21 FTP-palvelu, jossa anonymous kirjautuminen on sallittu, joten hyökkääjän on mahdollista päästä sisään ilman tunnusta tai salasanaa. Lisäksi Nmap tunnisti tietyn version palvelusta, joka mahdollistaa hyökkääjän tarkastavan onko kyseiseeen versioon tiedettyjä haavoittuvuuksia. Kyseisessä 

<img width="543" height="60" alt="image" src="https://github.com/user-attachments/assets/e6a5c269-20d1-4e61-9a95-a9351677a362" />

<br>
<br>

2. Portissa 1524 oli _open bindshell Metasploitable root shell_. Rootilla on laajat oikeudet koko järjestelmään, joten hyökkääjän päästessä sisään root shelliin olisi mahdolista saada root oikeudet ja täten tehdä lähes mitä vain.

 <img width="758" height="465" alt="image" src="https://github.com/user-attachments/assets/f30ae7d3-c1ab-41bf-a219-8d259efa9e4b" />


3. Portti 3632 oli distcdd -palvelu. Distributed C Compiler ohjelmien kääntämistä nopeutetaan jakamalla kääntäminen usealle eri koneelle, jolloin se hyödyntää etäyhteyden muihin koneisiin. Hyökkääjän on siis mahdollista saada kohdekone suorittamaan komentoja verkon yli etänä. 

<img width="769" height="22" alt="image" src="https://github.com/user-attachments/assets/0c4a6acd-b296-4a49-a571-0bae527b16c6" />

<br>
<br>

Yleisellä tasolla se, että Nmap tunnisti kaikki aktiiviset palvelut ja niiden tarkat versionumerot antaa hyökkääjälle jo paljon pinta-alaa. Tarkkojen versionumeroiden avulla on helppo tarkastaa kaikki tunnetut haavoittuvuudet ja toimia niiden pohjalta. Metasploitablen tapauksessa palvelut olivat vanhempia versioita, joka tekee tästä todella helppoa hyökkääjälle. Metasploitableen pääsi myös kirjautumaan merkittävin heikolla tunnus/salasana yhdistelmällä (msfadmin)


________________________________________________________________________________________________________________________________________________________________________________________

Karvinen, T. Tunkeutumistestaus. Opintojakson kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/. Luettu 1.9.2026.

Buuri, M. DORA and TLPT testing - Lecture for Haaga-Helia on 31 March 2026. 2026. Luettavissa: https://terokarvinen.com/buuri-2026-dora-and-threat-lead-penetration-testing/buuri-2026-dora-and-threat-lead-penetration-testing--teros-pentest-course.pdf. Luettu 1.9.2026.

REGULATION (EU) 2022/2554 OF THE EUROPEAN PARLIAMENT AND OF THE COUNCIL of 14 December 2022 on digital operational resilience for the financial sector and amending Regulations (EC) No 1060/2009, (EU) No 648/2012, (EU) No 600/2014, (EU) No 909/2014 and (EU) 2016/1011. EUR-Lex. Luettavissa: https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng. Luettu 1.9.2026.

Metasploitable 2. Ladattavissa: https://sourceforge.net/projects/metasploitable/files/latest/download. Luettu 1.9.2026.

Metasploitable 2 Exploitability Guide. Rapid7 Docs.  https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/. Luettu 1.9.2026.

House, N. Nmap Cheat Sheet 2026: All the Commands & Flags. 2026. Luettavissa: https://www.stationx.net/nmap-cheat-sheet/. Luettu 25.8.2026.

TIBER-FI procedures and guidelines. 5.4 Testing phase. 2025. Luettavissa: https://www.suomenpankki.fi/globalassets/bof/en/money-and-payments/the-bank-of-finland-as-catalyst-payments-council/tiber-fi/tiber-fi-2.0-procedures-and-guidelines.pdf. Luettu 1.9.2026.
