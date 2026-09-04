_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) / Metasploitable (VirtualBox)_

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


<img width="691" height="453" alt="METASPLOIT BANNER" src="https://github.com/user-attachments/assets/72e08789-56e2-4be4-aa0c-04fc0a4fb322" />

Tehtävät tarvitsivat myös tietokannan, jotta löydetyt hostit ja palvelut voitaisiin tallentaa. [Kalin dokumentoinnista]([Metasploit Framework](https://www.kali.org/docs/tools/starting-metasploit-framework-in-kali/)) löysin yksinkertaisen komennon PostgreSQL-tietokannan alustamiseen ja sen yhdistämiseen Metasploittiin.

    sudo msfdb init
    ### Ja tarkistettiin tilanne
    db_status

Tietokanta alustettiin poistumalla ensin Metasploitista, ajamalla init ja tarkistamalla status Metasploitissa.

________________________________________________________________________________________________________________________________________________________________________________________

### b) Tallenna porttiskannauksen tuloksia Metasploitin tietokantoihin. Skannaa niin, että Metasploitable tulee mukaan. Kannattaa ottaa mukaan ainakin versioskannaus -sV (joka on banner grabbing plus).

Aluksi tarkastin verkkoasetukseni: Kalin sekä Metasploitablen verkkoadapterit olivat vaihdettu Host-Only -moodiin ja ne pystyivät pingaamaan toisiaan mutta kummallakaan ei ollut pääsyä internettiin.


<img width="627" height="242" alt="PING TEST" src="https://github.com/user-attachments/assets/20a32390-8918-4389-a247-c22eb125cc06" />

Seuraavaksi oli aika suorittaa porttiskannaus. Tämä tapahtui komennolla ```db_nmap -sV <METASPLOITABLEN IP-OSOITE>```. 
- db_nmap suorittaa porttiskannauksen ja tallentaa tulokset tietokantaan (db).
- -sV suorittaa version detectionin, eli hakee palveluiden versiot. 

Sain DNS-varoituksen, sillä Kalilla ei ollut verkkoyhteyttä mutta koska skannaus kohdennettin suoraan tiettyyn IP-osoitteeseen pystyi varoituksen sivuuttamaan. Toiminto ajettiin normaalisti loppuun ja tulosteessa oli porttiskannauksen tiedot. Koska käytin ```db_nmap``` -komentoa, tulokset tallentuivat tietokantaan. 

Tallentuminen tietokantaan voitiin varmentaa ```hosts``` ja ```services``` -komennoilla. 

________________________________________________________________________________________________________________________________________________________________________________________

### c) Tarkastele Metasploitin tietokantoihin tallennettuja tietoja komennoilla "hosts" ja "services". Kokeile suodattaa näitä listoja tai hakea niistä.


Komennolla ```hosts``` pystyttiin tarkastelemaan skannauksessa löydettyjä laitteita, eli tässä tapauksessa Metasploitable konetta. Tietoon tallentuivat IP- ja MAC-osoite sekä veikkaus käyttöjärjestelmästä ja käyttötarkoituksesta (_Linux & server_). Jos hosteja olisi useampi, voitaisiin etsiä tietty haluttu host komennolla

    hosts -S <IP-OSOITE>

<img width="823" height="151" alt="HOSTS" src="https://github.com/user-attachments/assets/aa1fa8b9-8522-443b-8025-d2610d0b0108" />

Komennolla ```services``` saatiin esiin kyseisen hostin käyttämät palvelut ja avoimet portit. -sV parametrin ansiosta myös palveluiden versiot olivat näkyvissä. 

<img width="852" height="407" alt="SERVICES" src="https://github.com/user-attachments/assets/ec0fd0b9-6d7e-4543-87e8-5a22dbe28310" />

Palveluita pystyi helposti suodattamaan joko palvelun tai portin perusteella. 

    sevices -S <palvelu>
    services -p 80

<img width="850" height="168" alt="SSH" src="https://github.com/user-attachments/assets/eac3c183-4651-4337-acee-78684866f967" />

<img width="841" height="95" alt="PORT 80" src="https://github.com/user-attachments/assets/7a984247-e1c1-412c-b521-58e29fd96a1a" />


________________________________________________________________________________________________________________________________________________________________________________________

### d) Internet famous. Etsi Metasploitablen mukana tulevista hyökkäyksistä (en: exploits; search) sellainen, joka on ollut julkisuudessa.

Valitsin **vsftpd 2.3.4 portissa 21**. Kyseinen exploit voitiin etsiä ```search vsftpd```, jolloin Metasploit palautti kaikki vsftpd -exploitit. Haluttua backdoor exploittia pystyi tutkimaan vielä tarkemin komennolla ```info exploit/unix/ftp/vsftpd_234_backdoor```.

<img width="760" height="431" alt="VSFTPD exploit" src="https://github.com/user-attachments/assets/9f5b1499-9ed5-4587-83dd-d6eafa421b5f" />


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

<img width="855" height="147" alt="LHOST OSOITE" src="https://github.com/user-attachments/assets/53e9d76c-161c-49c7-b254-e85140b323d1" />

Tämä johtui siitä, että Metasploitille ei oltu vielä määritetty _Local Host (LHOST)_  IP-osoitetta, eli tässä tapauksessa Kali-koneen osoitetta. Sain määritettyä sen komennolla ```set LHOST <KALIN IP-OSOITE>```. 

Tämän korjauksen jälkeen exploit komennot uudestaan onnistuneesti. 


<img width="844" height="149" alt="BACKDOOR HAS SPAWNNED" src="https://github.com/user-attachments/assets/6080dcc4-d739-41cb-a469-10d924955c89" />

Yritin seuraavaksi lähteä hakemaan tietoa kohdekoneesta komennoilla ```whoami``` ja ```hostname```. Nämä eivät kuitenkaan toimineet vaan palauttivat _"Unknown command"_ virheilmoitukset. LÄHDE löytyi, että meterpreter tottelee eri käskyjä:

    getuid
    sysinfo

<img width="466" height="132" alt="image" src="https://github.com/user-attachments/assets/f188c0ec-380c-46de-ac51-b04f493d340f" />

________________________________________________________________________________________________________________________________________________________________________________________


### g) Kerää levittäytymisessä (lateral movement) tarvittavaa tietoa metasploitablesta. Analysoi tiedot. Selitä, miten niitä voisi hyödyntää.

________________________________________________________________________________________________________________________________________________________________________________________


### h) Murtaudu Metasploitableen jollain toisella tavalla. (Jos tämä kohta on vaikea, voit tarvittaessa turvautua verkosta löytyviin läpikävelyohjeisiin. Merkitse silloin raporttiin, missä määrin tarvitsit niitä).

Käyttäjärjestelmä ja käyttäjä oli jo saatu tietoon, joten oli aika lähteä tutkimaan muita hyödyllisiä perustietoja. Ensimmäisenä lähdin selvittämään verkkoyhteyksiä

    ipconfig
    ________________________________________________________________________________________________________________________________________________________________________________________



### i) Demonstroi Meterpretrin ominaisuuksia.

________________________________________________________________________________________________________________________________________________________________________________________

### j) Tallenna shell-sessio tekstitiedostoon script-työkalulla (script -fa log001.txt) tai tmux:lla.

________________________________________________________________________________________________________________________________________________________________________________________

### k) Pivot point. Laita kaikki harjoituksen tiedostot (script -fa, nmap -oA...) samaan kansioon. Hae sopiva pivot point (sovellus, versio, osoite, MAC-numero) 'grep -r' -komennolla. Keksi uskottava esimerkkikysymys, johon haet vastausta.

________________________________________________________________________________________________________________________________________________________________________________________

### l) Attaaack! Mitä Mitre Attack taktiikoita ja tekniikoita käytit tässä harjoituksessa? (Tässä alakohdassa "Attaack!" ei tarvitse tehdä lisää testejä koneella, koska testit on jo tehty.)


________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet

Karvinen, T. Tunkeutumistestaus kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#h3-eternalhomework. Luettu 4.9.2026.

Metasploit-framework. Kali.org. Luettavissa: https://www.kali.org/tools/metasploit-framework/. Luettu 4.9.2026.

Metasploit Framework docs. Kali.org. 2025. ttps://www.kali.org/docs/tools/starting-metasploit-framework-in-kali/. Luettu 4.9.2026.

Nmap Cheat Sheet. GeeksForGeeks. 2025. Luettavissa: https://www.geeksforgeeks.org/ethical-hacking/nmap-cheat-sheet/. Luettu 4.9.2026.

Nmap Output Formats: -oN, -oX, -oG, -oA and Parsing Results. Ping Labz. 2026. Luettavissa: https://www.pinglabz.com/nmap-output-formats/. Luettu 4.9.2026.

