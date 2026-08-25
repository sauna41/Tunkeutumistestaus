_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 22.8.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on h1 Kybertappoketju. Opettajana toimi Tero Karvinen._

________________________________________________________________________________________________________________________________________________________________________________________

#### x) Lue/katso/kuuntele ja tiivistä. 

**[Herrasmieshakkerit (RSS)](https://herrasmieshakkerit.fi/)**

- 
- 
- 
- 



- sad
- sadad
- sdas


**€ Santos et al: The Art of Hacking (Video Collection): 4.3 Surveying Essential Tools for Active Reconnaissance.**


**[KKO 2003:36](https://www.finlex.fi/fi/oikeuskaytanto/korkein-oikeus/ennakkopaatokset/2003/36)**

- asdas
- sadad
- 

#### a) Asenna Kali virtuaalikoneeseen.
(Jos asennuksessa ei ole mitään ongelmia tai olet asentanut jo aiemmin, tarkkaa raporttia tästä alakohdasta ei tarvita. Kerro silloin kuitenkin, mikä versio ja millä asennustavalla. Jos on ongelmia, niin tarkka ja toistettava raportti).

________________________________________________________________________________________________________________________________________________________________________________________

### b) Irrota Kali-virtuaalikone verkosta.

Ennen kuin irrotin Kalia verkosta, varmistin, että verkko kuitenkin toimii. Pingasin Googlen DNSää 8.8.8.8. ja sain sieltä vastauksen. 

<img width="675" height="112" alt="image" src="https://github.com/user-attachments/assets/1913d625-a0b7-43ec-b251-3153bea28b23" />

Tämän jälkeen navigoin VirtualBoxin verkkoasetuksiin, josta kytkin verkon pois päältä. Boottasin Kalin uudestaan ja varmistin komennoilla, että kone ei saanut yhteyttä verkkoon. Komentorivin lisäksi yritin myös avata Firefox selaimen, mutta sekään ei ollut yhteydessä verkkoon.

<img width="1163" height="491" alt="image" src="https://github.com/user-attachments/assets/df3de18d-35f6-444a-947d-4542b7685a52" />


________________________________________________________________________________________________________________________________________________________________________________________

#### c) Porttiskannaa 1000 tavallisinta tcp-porttia omasta koneestasi

Porttiskannaus tapahtui komennolla

    nmap -T4 -A localhost

- nmap on porttiskannaustyökalu, joka lähettää paketteja ja analysoi vastauksia niihin.
- T4 on vipu skannauksen nopeudelle.
- -A vipu määrittää useita 
<img width="1277" height="332" alt="image" src="https://github.com/user-attachments/assets/9c644ad7-934c-4018-9b74-57ae8e7359ba" />






________________________________________________________________________________________________________________________________________________________________________________________

### d) Asenna kaksi vapaavalintaista demonia ja skannaa uudelleen. Analysoi ja selitä erot.

________________________________________________________________________________________________________________________________________________________________________________________

e) Ratkaise vapaavalintainen kone HackTheBoxista. Omalle tasolle sopiva, useimmille varmaan Starting Pointista. Valitse kone, jota et ole ratkaissut vielä. Ei tunnilla näytetty Meow. (Propellihatuille: jos teet vaikeampia ei-starting-point koneita, niin retired tai vastaava kone, josta saa julkaista writeupin).

________________________________________________________________________________________________________________________________________________________________________________________


### Lähteet

Karvinen, T. Tunkeutumistestaus. Opintojakson kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/tunkeutumistestaus/#laksyt. Luettu 22.8.2026.

Hutchins, M. Cloppert, M, Amid R. Intelligence-Driven Computer Network Defense
Informed by Analysis of Adversary Campaigns and
Intrusion Kill Chains. Luettavissa: https://lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf. Luettu 22.8.2026.

 
Santos O, Taylor R, Sternstein J, McCoy C. The Art of Hacking (Video Collection). 2019. Luettavissa: https://www.oreilly.com/videos/the-art-of/9780135767849/9780135767849-SPTT_04_00/. Luettu 22.8.2026.

KKO:2003:36. 2023. Luettavissa: https://www.finlex.fi/fi/oikeuskaytanto/korkein-oikeus/ennakkopaatokset/2003/36. Luettu 22.8.2026.


